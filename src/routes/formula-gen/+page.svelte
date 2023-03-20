<script lang="ts">
	import { goto } from "$app/navigation";
	import Card from "../../components/Card.svelte";
	import BackButton from "../../components/BackButton.svelte";
	import { createForm } from 'felte';
	import { reporter, ValidationMessage } from '@felte/reporter-svelte';
	import { validator } from '@felte/validator-yup';
	import * as yup from 'yup';
	import ValidationIcon from '../../components/ValidationIcon.svelte';
    import Icon from '@iconify/svelte';
    import paperPlaneSharp from '@iconify/icons-ion/paper-plane-sharp';
	import SaveButton from "../../components/SaveButton.svelte";

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

    let formula = ''
	const schema = yup.object().shape({
		function: yup.string().required(),
		
	});

	const { form, touched, data, isValid, errors } = createForm({
		initialValues: {
			function: '',
		},
		extend: [validator({ schema }), reporter],
		onSubmit: values => {
			alert(JSON.stringify(values))

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

</script>
<div class="p-4">
    <BackButton click={() => goto('/')}/>
    <Card styling="mx-auto align-middle w-1/3 shadow-md" title="Formula Generator">
        <svelte:fragment slot="content">
            <div class="border-b-2" />
        <form use:form>
            <div>
                <div class="form-control relative">
                    <label class="label label-text text-gray-700" for="function">Please enter a valid function</label>
                    <textarea
                        id="function"
                        data-theme="light"
                        class="textarea input-bordered {changeFocus(
                            $touched.function,
                            $errors.function
                        )}"
                        
                        name="function"
                    />
                    <ValidationIcon touched={$touched.function} errors={$errors.function} />
                </div>
                <ValidationMessage for="function" let:messages={message}>
                    <span class="text-red-600 text-sm pt-1">{message || ''}</span>
                </ValidationMessage>
            </div>
            <div class="grid m-2 tooltip tooltip-bottom" data-tip="Convert to Formula">
                <button class="btn btn-accent mx-auto align-middle text-black disabled:text-gray-500 rounded-md border-2" disabled={$isValid ? false : true} type="submit"><Icon icon={paperPlaneSharp} class='w-6 h-6' /></button>

            </div>
            <div>
                <div class="form-control relative">
                    <label class="label label-text text-gray-700" for="function">Generated formula</label>
                    <textarea
                        id="formula"
                        class="font-semibold text-black bg-white textarea input-bordered"
                        name="formula"
                        placeholder="Formula will appear here"
                        disabled
                        bind:value={formula}
                    />
                </div>
            
            </div>
        </form>
        </svelte:fragment>
    </Card>
</div>
