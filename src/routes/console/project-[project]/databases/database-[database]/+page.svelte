<script lang="ts">
    import { Empty, PaginationWithLimit } from '$lib/components';
    import { Button } from '$lib/elements/forms';
    import { Container, GridHeader } from '$lib/layout';
    import { columns, showCreate } from './store';
    import Table from './table.svelte';
    import Grid from './grid.svelte';
    import type { PageData } from './$types';

    export let data: PageData;
</script>

<Container>
    <GridHeader title="Collections" {columns} view={data.view}>
        <Button on:click={() => ($showCreate = true)} event="create_collection">
            <span class="icon-plus" aria-hidden="true" />
            <span class="text">Create collection</span>
        </Button>
    </GridHeader>

    {#if data.collections.total}
        {#if data.view === 'grid'}
            <Grid {data} />
        {:else}
            <Table {data} />
        {/if}

        <PaginationWithLimit
            name="Collections"
            limit={data.limit}
            offset={data.offset}
            total={data.collections.total} />
    {:else}
        <Empty
            single
            href="https://appwrite.io/docs/databases#collection"
            target="collection"
            on:click={() => ($showCreate = true)} />
    {/if}
</Container>
