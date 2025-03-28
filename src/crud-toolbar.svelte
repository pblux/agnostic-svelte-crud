<script lang="ts">
	import { Button } from '$lib/components/ui/button';

	let { collectionName, addRecord } = $props<{
		collectionName: string;
		addRecord: any;
	}>();
</script>

<div class="m-4 flex justify-between">
	<div>
		<h4 class="text-xl font-semibold">{collectionName}</h4>
	</div>
	<div>
		<Button aria-label="Add item" variant="outline" onclick={addRecord}>Add</Button>
	</div>
</div>
