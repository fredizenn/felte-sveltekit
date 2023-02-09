<script lang="ts">
	import { goto } from "$app/navigation";



    let todos: any[] = []
    
    let uid = 1;

    function add(input: any) {
        const todo = {
        id: uid++,
        done: false,
        description: input.value
        } 

        todos = [todo, ...todos]
        input.value = ''
        // todos = todos.concat({ done: false, text: ''})
    }

    function clear() {
        todos = todos.filter(t=> !t.done)
    }

</script>
<div class="mx-auto mt-8 align-middle rounded-md w-2/6">
    <div class="p-4 text-center">
        <div class="font-bold text-gray-500 p-4">Killing Time</div>
        <div class="p-4">Welcome to this random application I built with SvelteKit.</div>
        <div class="text-xs text-gray-500 font-semibold">John Wick's To-do list</div>
    </div>
   
    <!-- <div class="p-4 text-center">
        {#if user.loggedIn}
        <button on:click={toggle} class="btn btn-info text-white">Log Out</button>
        {/if}
        {#if !user.loggedIn}
        <button on:click={toggle} class="btn btn-info text-white">Log In</button>
        {/if}
    </div> -->
    <div class="grid">
        {#each todos as todo}
        <div class="flex p-2 justify-center space-x-2">
            <input type="checkbox" class="cursor-pointer" bind:checked={todo.done}>
            <input type="text"  placeholder="Enter task here" class="input input-bordered w-full lg:w-1/2"
             disabled={todo.done}
             bind:value={todo.description}>
        </div>
        {/each}
        
    </div>
    <div class="flex justify-center mt-2 space-x-2">
        <button class="btn btn-info text-white" on:click={add}>Add New</button>
        <button class="btn btn-error text-white" on:click={clear}>Clear Completed</button> 
    </div>
    <div class="text-xs text-gray-400 text-center p-4">
        <button class="btn btn-primary text-white" on:click={() => goto('/felte-form')}>Go to the Felte form</button>
    </div>
    <!-- <div class="mt-4 grid grid-cols-2 gap-2 grid-rows-4">
        <div class="flex space-x-2">
            <input type="checkbox" class="cursor-pointer">
            <input type="text"  placeholder="Hehe" class="input input-bordered w-full" >
        </div>
        
    </div> -->
   


</div>