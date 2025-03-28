<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label/index.js';
	import { Switch } from '$lib/components/ui/switch/index.js';
	import * as Dialog from '$lib/components/ui/dialog/index.js';
	import * as Select from '$lib/components/ui/select/index.js';

	let {
		isOpen = $bindable(),
		collectionName,
		editingRecord = $bindable(),
		createOrUpdateRecord,
		collectionFields
	} = $props<{
		isOpen: boolean;
		collectionName: string;
		editingRecord: Record<string, any>;
		createOrUpdateRecord: (record: Record<string, any>) => void;
		collectionFields: { label: string; type: string; options?: string[] }[];
	}>();

	let errors = $state<Record<string, string>>({});

	function capitalizeFirstLetter(str: string): string {
		return str.charAt(0).toUpperCase() + str.slice(1);
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Enter') {
			submitDialog(); // Call the button's action
		}
	}

	function validateFields() {
		errors = {}; // Reset errors
		collectionFields.forEach(
			(field: { label: string; type: string; options?: string[]; required?: boolean }) => {
				if (field.required && !editingRecord[field.label]) {
					errors[field.label] = `${field.label} is required.`;
				}
			}
		);
		return Object.keys(errors).length === 0; // Return true if no errors
	}

	function validateField(field: {
		label: string;
		type: string;
		options?: string[];
		required?: boolean;
	}) {
		if (field.required && !editingRecord[field.label]) {
			errors[field.label] = `${field.label} is required.`;
		} else {
			delete errors[field.label]; // Remove error if field is valid
		}
	}

	function submitDialog() {
		if (validateFields()) {
			createOrUpdateRecord(editingRecord);
			isOpen = false;
		}
	}
</script>

<Dialog.Root bind:open={isOpen}>
	<Dialog.Content class="sm:max-w-[425px]">
		<Dialog.Header>
			<Dialog.Title>
				{editingRecord.id ? 'Update ' : 'Add '}
			</Dialog.Title>
		</Dialog.Header>
		<div class="grid gap-4 py-4">
			{#each collectionFields as field}
				<div class="grid grid-cols-4 items-center gap-4">
					<Label for={field.label} class="text-right">{capitalizeFirstLetter(field.label)}</Label>
					{#if field.type === 'text'}
						<div class="col-span-3">
							<Input
								id={field.label}
								type="text"
								placeholder={capitalizeFirstLetter(field.label)}
								onchange={() => validateField(field)}
								onkeydown={handleKeydown}
								bind:value={editingRecord[field.label]}
							/>
							{#if errors[field.label]}
								<span class="ml-3 text-sm text-red-500">
									{errors[field.label]}
								</span>
							{/if}
						</div>
					{:else if field.type === 'boolean'}
						<div class="col-span-3">
							<Switch
								id={field.label}
								onkeydown={handleKeydown}
								bind:checked={editingRecord[field.label]}
							/>
						</div>
					{:else if field.type === 'select'}
						<div class="col-span-3">
							<Select.Root
								type="single"
								onValueChange={() => validateField(field)}
								bind:value={editingRecord[field.label]}
							>
								<Select.Trigger id={field.label} onkeydown={handleKeydown}>
									{editingRecord[field.label]
										? editingRecord[field.label]
										: `Select a ${field.label}`}
								</Select.Trigger>
								<Select.Content>
									{#if field.options}
										{#each field.options as option}
											<Select.Item value={option} />
										{/each}
									{/if}
								</Select.Content>
							</Select.Root>
							{#if errors[field.label]}
								<span class="ml-3 text-sm text-red-500">
									{errors[field.label]}
								</span>
							{/if}
						</div>
					{/if}
				</div>
			{/each}
		</div>
		<Dialog.Footer>
			<div class="flex w-full justify-between">
				<Button variant="outline" aria-label="Cancel" onclick={() => (isOpen = false)}
					>Cancel
				</Button>
				<Button aria-label="Update/Add item" onclick={submitDialog}>
					{editingRecord.id ? 'Update' : 'Add'}
				</Button>
			</div>
		</Dialog.Footer>
	</Dialog.Content>
</Dialog.Root>
