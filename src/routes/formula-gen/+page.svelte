<script lang="ts">
	import { goto } from '$app/navigation';
	import Card from '../../components/Card.svelte';
	import BackButton from '../../components/BackButton.svelte';
	import { createForm } from 'felte';
	import { reporter, ValidationMessage } from '@felte/reporter-svelte';
	import { validator } from '@felte/validator-yup';
	import * as yup from 'yup';
	import ValidationIcon from '../../components/ValidationIcon.svelte';
	import Icon from '@iconify/svelte';
	import paperPlaneSharp from '@iconify/icons-ion/paper-plane-sharp';
	import resetIcon from '@iconify/icons-system-uicons/reset';
	import Loading from '../../components/loading.svelte';
	import { toasts, ToastContainer, FlatToast } from 'svelte-toasts';

	const showErrorToast = (title: any = 'An error occurred', description: any = '') => {
		toasts.add({
			title: title,
			description: description,
			duration: 4000,
			placement: 'top-center',
			type: 'error',
			theme: 'dark',
			showProgress: true
		});
	};

	function validateSyntax(val: any) {
		if (val.split(0) !== '=') {
			showErrorToast('Incorrect syntax. Please begin with an equal sign');
		}
	}

	yup.setLocale({
		mixed: {
			default: 'Not valid',
			required: 'Must not be empty'
		},
		string: {
			email: 'Must be a valid email',
			min: 'Must not be empty'
		}
	});

	let inputFunction = '';

	let formula = '';
	const schema = yup.object().shape({
		function: yup.string().required()
	});

	let loading: boolean;

	function parseFormula(func: any) {
		loading = true;

		fetch(`https://localhost:7015/api/GetExcelFormula?formula=${func}`, {})
			.then((response) => response.json())
			.then((data) => {
				formula = data?.formula;
				loading = false;
			})
			.catch((error) => {
				loading = false;
				console.log('Error', error);
			});
	}

	const {
		form,
		touched,
		data: dataStore,
		isValid,
		errors
	} = createForm({
		initialValues: {
			function: inputFunction
		},
		extend: [validator({ schema }), reporter],
		onSubmit: (values) => {	
				let val: any = values.function;
				if (val.charAt(0) !== '=') {
					showErrorToast('Incorrect syntax. Please begin with an equal sign');
				} else {
                parseFormula(values.function)
                }
	
		}
	});

	$: changeFocus = (touch: any, error: any) => {
		let focusClass = `border-green-600`;
		if (touch === true && error === null) {
			return focusClass;
		} else if (touch === false && error === null) {
			return;
		} else {
			return (focusClass = `border-red-600`);
		}
	};

	const addKeyword = (char: any) => {
		inputFunction += char;
	};

	$: console.log(inputFunction);
</script>

<div class="p-4">
	<BackButton click={() => goto('/')} />
	<Card styling="mx-auto align-middle w-1/3 shadow-md" title="Formula Generator">
		<svelte:fragment slot="content">
			<div class="border-b-2" />

			<div class="mt-2 flex gap-2">
				<button class="btn btn-sm" on:click={() => addKeyword('SUM')}>SUM</button>
				<button class="btn btn-sm" on:click={() => addKeyword('SUBTRACT')}>SUBTRACT</button>
				<button class="btn btn-sm" on:click={() => addKeyword('MULTIPLY')}>MULTIPLY</button>
				<button class="btn btn-sm" on:click={() => addKeyword('DIVIDE')}>DIVIDE</button>
				<button class="btn btn-sm" on:click={() => addKeyword('(')}>{'('}</button>
				<button class="btn btn-sm" on:click={() => addKeyword(')')}>{')'}</button>
			</div>
			<form use:form>
				<div>
					<div class="form-control relative">
						<label class="label label-text text-gray-700" for="function"
							>Please enter a valid function</label
						>
						<textarea
							id="function"
							data-theme="light"
							class="textarea input-bordered {changeFocus($touched.function, $errors.function)}"
							name="function"
                            placeholder="eg. =SUM(AP, BA)"
							bind:value={inputFunction}
						/>
						<button
							data-tip="Clear"
							class="tooltip tooltip-right absolute inset-y-0 right-0 flex items-center pr-3"
							type="button"
							on:click={() => ((inputFunction = ''), (formula = ''))}
							><Icon icon={resetIcon} class="w-5 h-5" /></button
						>
						<ValidationIcon touched={$touched.function} errors={$errors.function} />
					</div>
					<ValidationMessage for="inputFunction" let:messages={message}>
						<span class="text-red-600 text-sm pt-1">{message || ''}</span>
					</ValidationMessage>
				</div>
				<div class="grid m-2 tooltip tooltip-bottom" data-tip="Convert to Formula">
					{#if loading}
						<button
							class="btn btn-accent mx-auto align-middle text-black disabled:text-gray-500 rounded-md border-2"
							disabled
							type="button"><Loading /></button
						>
					{:else}
						<button
							class="btn btn-accent mx-auto align-middle text-black disabled:text-gray-500 rounded-md border-2"
							disabled={$isValid ? false : true}
							type="submit"><Icon icon={paperPlaneSharp} class="w-6 h-6" /></button
						>
					{/if}
				</div>
			</form>

			<div>
				<div class="form-control relative">
					<label class="label label-text text-gray-700" for="function">Generated formula</label>
					<div id="formula" class="font-semibold text-black bg-white textarea input-bordered">
						{formula}
					</div>
				</div>
			</div>
		</svelte:fragment>
	</Card>
</div>
<ToastContainer let:data>
	<FlatToast {data} />
</ToastContainer>
