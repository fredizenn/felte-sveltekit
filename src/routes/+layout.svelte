<script lang="ts">
	import Nav from "../components/Nav.svelte";
    import "../app.css";
	import Sidebar from "../components/Sidebar.svelte";
</script>

<div>
    <Nav />
    <div class="h-full flex mx-auto">
        <!-- <Sidebar /> -->
    </div>
    <slot />
</div>
