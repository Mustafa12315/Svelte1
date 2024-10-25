<script>
    export let data;
    const posts = data.posts;
    import Post from "$lib/Post.svelte";
	import Header from "../../lib/Header.svelte";

</script>
<Header headingTitle="News"  />
<h2>News about learning SvelteKit</h2>

{#each posts as post}
    <Post {post} />
{/each}

<style>
    
</style>