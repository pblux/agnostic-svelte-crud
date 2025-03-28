<script lang="ts">
	import { Ellipsis } from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button';
	import * as Table from '$lib/components/ui/table/index.js';
	import * as DropdownMenu from '$lib/components/ui/dropdown-menu/index.js';
	import { Toaster } from '$lib/components/ui/sonner';
	import CrudToolbar from './crud-toolbar.svelte';
	import CrudPagination from './crud-pagination.svelte';
	import CrudEditionDialog from './crud-edition-dialog.svelte';
	import { onMount } from 'svelte';

	let {
		collectionRecords = [{ id: '0001', name: 'Example item' }],
		collectionName = 'The collection',
		collectionFields = [
			{ label: 'id', type: 'text' },
			{ label: 'name', type: 'text' }
		],
		updateDatabase = async () => actionsFallback(),
		deleteRecord = async () => actionsFallback()
	}: {
		collectionName?: string;
		collectionRecords?: Record<string, any>[];
		collectionFields?: { label: string; type: string; options?: string[] }[];
		updateDatabase?: (record: any) => Promise<void>;
		deleteRecord?: (id: string) => Promise<void>;
	} = $props();

	let editingRecord = $state<Record<string, any>>({});

	let isOpen = $state(false);

	let currentPage = $state(1);
	const perPage = 50;
	let count = $derived(collectionRecords.length);

	let paginatedRecords = $derived(() => {
		const start = (currentPage - 1) * perPage;
		const end = start + perPage;
		return collectionRecords.slice(start, end);
	});

	let sortColumn = $state<string | null>(
		collectionFields.length > 0 ? collectionFields[0].label : null
	);
	let sortOrder = $state<'asc' | 'desc'>('asc');

	function sortRecords(column: string) {
		if (sortColumn === column) {
			// Toggle sort order if the same column is clicked
			sortOrder = sortOrder === 'asc' ? 'desc' : 'asc';
		} else {
			// Set new column and default to ascending order
			sortColumn = column;
			sortOrder = 'asc';
		}

		collectionRecords = [...collectionRecords].sort((a, b) => {
			const aValue = a[column];
			const bValue = b[column];

			if (aValue < bValue) return sortOrder === 'asc' ? -1 : 1;
			if (aValue > bValue) return sortOrder === 'asc' ? 1 : -1;
			return 0;
		});
	}

	function capitalizeFirstLetter(str: string) {
		return str.charAt(0).toUpperCase() + str.slice(1);
	}

	async function createOrUpdateRecord(editedRecord: any) {
		if (editedRecord.id) {
			collectionRecords = collectionRecords.map((record: any) => {
				if (record.id === editedRecord.id) {
					return { ...record, ...editedRecord };
				}
				return record;
			});
			await updateDatabase(editedRecord);
		} else {
			collectionRecords = [...collectionRecords, editedRecord];
			await updateDatabase(editedRecord);
		}
	}

	function actionsFallback() {
		alert('Actions fallback triggered!');
	}

	onMount(() => {
		if (sortColumn) {
			sortRecords(sortColumn);
		}
	});
</script>

<CrudToolbar
	{collectionName}
	addRecord={() => {
		isOpen = true;
		editingRecord = collectionFields.reduce(
			(acc: Record<string, any>, field: { label: string; type: string; options?: string[] }) => {
				acc[field.label] = field.type === 'boolean' ? false : '';
				return acc;
			},
			{}
		);
	}}
/>

<div class="m-4 rounded-md border">
	<Table.Root>
		<Table.Header>
			<Table.Row>
				{#each collectionFields as field}
					<Table.Head>
						<button
							class="flex items-center space-x-1"
							aria-label={`Sort by ${field.label}`}
							onclick={() => sortRecords(field.label)}
						>
							<span>{capitalizeFirstLetter(field.label)}</span>
							{#if sortColumn === field.label}
								<span>{sortOrder === 'asc' ? '▲' : '▼'}</span>
							{/if}
						</button>
					</Table.Head>
				{/each}
				<Table.Head></Table.Head>
			</Table.Row>
		</Table.Header>
		<Table.Body>
			{#each paginatedRecords() as record}
				<Table.Row>
					{#each collectionFields as field}
						<Table.Cell>{record[field.label] ?? ''}</Table.Cell>
					{/each}
					<Table.Cell class="flex justify-end gap-2">
						<DropdownMenu.Root>
							<DropdownMenu.Trigger>
								<Button variant="ghost" class="flex h-8 w-8 p-0" aria-label="Open Menu">
									<Ellipsis class="h-4 w-4" />
									<span class="sr-only">Open Menu</span>
								</Button>
							</DropdownMenu.Trigger>
							<DropdownMenu.Content class="w-[160px]" align="end">
								<DropdownMenu.Item
									aria-label="Edit item"
									onclick={() => {
										editingRecord = record;
										isOpen = true;
									}}
									>Edit
								</DropdownMenu.Item>
								<DropdownMenu.Separator />
								<DropdownMenu.Item aria-label="Delete item" onclick={() => deleteRecord(record.id)}>
									Delete
								</DropdownMenu.Item>
							</DropdownMenu.Content>
						</DropdownMenu.Root>
					</Table.Cell>
				</Table.Row>
			{/each}
		</Table.Body>
	</Table.Root>
</div>

<CrudPagination bind:currentPage {count} {perPage} />

{#if isOpen}
	<CrudEditionDialog
		bind:isOpen
		bind:editingRecord
		{collectionName}
		{collectionFields}
		{createOrUpdateRecord}
	/>
{/if}

<Toaster richColors />
