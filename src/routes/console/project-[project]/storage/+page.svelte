<script lang="ts">
    import { page } from '$app/stores';
    import { goto } from '$app/navigation';
    import { Button } from '$lib/elements/forms';
    import {
        Empty,
        GridItem1,
        CardContainer,
        Heading,
        PaginationWithLimit,
        Id
    } from '$lib/components';
    import { Pill } from '$lib/elements';
    import Create from './create.svelte';
    import { Container } from '$lib/layout';
    import { base } from '$app/paths';
    import { tooltip } from '$lib/actions/tooltip';
    import type { Models } from '@appwrite.io/console';
    import type { PageData } from './$types';

    export let data: PageData;

    let showCreate = false;

    const project = $page.params.project;

    async function bucketCreated(event: CustomEvent<Models.Bucket>) {
        showCreate = false;
        await goto(`${base}/console/project-${project}/storage/bucket-${event.detail.$id}`);
    }
</script>

<Container>
    <div class="u-flex u-gap-12 common-section u-main-space-between">
        <Heading tag="h2" size="5">Buckets</Heading>

        <Button on:click={() => (showCreate = true)} event="create_bucket">
            <span class="icon-plus" aria-hidden="true" /> <span class="text">Create bucket</span>
        </Button>
    </div>

    {#if data.buckets.total}
        <CardContainer
            total={data.buckets.total}
            offset={data.offset}
            event="bucket"
            on:click={() => (showCreate = true)}>
            {#each data.buckets.buckets as bucket}
                <GridItem1 href={`${base}/console/project-${project}/storage/bucket-${bucket.$id}`}>
                    <svelte:fragment slot="title">{bucket.name}</svelte:fragment>
                    <svelte:fragment slot="status">
                        {#if !bucket.enabled}
                            <Pill>Disabled</Pill>
                        {/if}
                    </svelte:fragment>

                    <Id value={bucket.$id}>{bucket.$id}</Id>

                    <svelte:fragment slot="icons">
                        <li>
                            <span
                                class:u-opacity-20={!bucket.encryption}
                                class="icon-lock-closed"
                                aria-hidden="true"
                                use:tooltip={{
                                    content: bucket.encryption
                                        ? 'Encryption enabled'
                                        : 'Encryption disabled'
                                }} />
                        </li>
                        <li>
                            <span
                                class:u-opacity-20={!bucket.antivirus}
                                class="icon-shield-check"
                                aria-hidden="true"
                                use:tooltip={{
                                    content: bucket.antivirus
                                        ? 'Antivirus enabled'
                                        : 'Antivirus disabled'
                                }} />
                        </li>
                    </svelte:fragment>
                </GridItem1>
            {/each}
            <svelte:fragment slot="empty">
                <p>Add a new bucket</p>
            </svelte:fragment>
        </CardContainer>

        <PaginationWithLimit
            name="Buckets"
            limit={data.limit}
            offset={data.offset}
            total={data.buckets.total} />
    {:else}
        <Empty
            single
            href="https://appwrite.io/docs/storage"
            target="bucket"
            on:click={() => (showCreate = true)} />
    {/if}
</Container>

<Create bind:showCreate on:created={bucketCreated} />
