<script>
    import MCPCard from './MCPCard.svelte';

    export let mcps = [];
    export let onCardClick;
</script>

<div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6">
    {#each mcps as mcp (mcp.id)}
        <MCPCard {mcp} onCardClick={onCardClick} />
    {/each}
</div>