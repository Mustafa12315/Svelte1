<script>
    import Footer from "$lib/Footer.svelte";
    import picture from '$lib/pic/bird-8788491_1280.jpg';
</script>
<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<div>
    <h2>Hello, svelte!</h2>
</div>

<Footer thisismypropname={picture} />

<style>
    :global(*){
        font-family: cursive;
    }
</style>