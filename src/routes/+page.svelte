<script>
    import LeafletMap from '$lib/LeafletMap.svelte';
    import { base } from '$app/paths';
</script>

<nav>
    <a href="{base}/">Home</a>
    <a href="{base}/about">About</a>
</nav>

<main>
    <LeafletMap />
</main>