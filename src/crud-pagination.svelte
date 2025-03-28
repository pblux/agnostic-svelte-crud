<script lang="ts">
	import { ChevronLeft, ChevronRight } from '@lucide/svelte';
	import * as Pagination from '$lib/components/ui/pagination/index.js';

	let {
		currentPage = $bindable(),
		count,
		perPage
	} = $props<{
		currentPage: number;
		count: number;
		perPage: number;
	}>();
</script>

<Pagination.Root
	{count}
	{perPage}
	page={currentPage}
	onPageChange={(page) => {
		currentPage = page;
	}}
>
	{#snippet children({ pages, currentPage })}
		<Pagination.Content>
			<Pagination.Item>
				<Pagination.PrevButton>
					<ChevronLeft class="size-4" />
					<span class="hidden sm:block">Previous</span>
				</Pagination.PrevButton>
			</Pagination.Item>
			{#each pages as page (page.key)}
				{#if page.type === 'ellipsis'}
					<Pagination.Item>
						<Pagination.Ellipsis />
					</Pagination.Item>
				{:else}
					<Pagination.Item>
						<Pagination.Link {page} isActive={currentPage === page.value}>
							{page.value}
						</Pagination.Link>
					</Pagination.Item>
				{/if}
			{/each}
			<Pagination.Item>
				<Pagination.NextButton>
					<span class="hidden sm:block">Next</span>
					<ChevronRight class="size-4" />
				</Pagination.NextButton>
			</Pagination.Item>
		</Pagination.Content>
	{/snippet}
</Pagination.Root>
