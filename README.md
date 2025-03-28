# Svelte CRUD Component

A reusable and customizable CRUD (Create, Read, Update, Delete) component builtwith Svelte. This component provides a table with sorting, pagination, and editing capabilities making it easy to manage collections of data in your Svelte applications.

## Features

- **Dynamic Table**: Automatically generates a table based on the provided fields and records.
- **Sorting**: Sort records by clicking on column headers.
- **Pagination**: Navigate through large datasets with customizable pagination.
- **Editing**: Edit or create new records using a modal dialog.
- **Customizable**: Pass your own styles, actions, and configurations.
- **Accessible**: Built with accessibility in mind.

## Installation

To use this component in your project, first install it via npm:

`npm install svelte-crud`

## Usage

Here’s an example of how to use the `Crud` component in your Svelte application:

    <script lang="ts">
    	import { Crud } from 'svelte-crud';
    	let collectionRecords = [
    		{ id: '0001', name: 'Example Item 1' },
    		{ id: '0002', name: 'Example Item 2' }
    	];
    	let collectionFields = [
    		{ label: 'id', type: 'text' },
    		{ label: 'name', type: 'text' }
    	];

    	async function updateDatabase(record) {
    		console.log('Updating record:', record);
    		// Add your update logic here
    	}

    	async function deleteRecord(id) {
    		console.log('Deleting record with id:', id);
    		// Add your delete logic here
    	}
    </script>

    <Crud {collectionRecords} {collectionFields} collectionName="My Collection" {updateDatabase} {deleteRecord} />

## Props

| Prop                | Type                                                    | Default     | Description                                                           |
| ------------------- | ------------------------------------------------------- | ----------- | --------------------------------------------------------------------- |
| `collectionName`    | `string`                                                | `''`        | The name of the collection displayed in the toolbar.                  |
| `collectionRecords` | `Record<string, any>[]`                                 | `[]`        | The array of records to display in the table.                         |
| `collectionFields`  | `{ label: string; type: string; options?: string[] }[]` | `[]`        | The fields to display in the table, including their labels and types. |
| `updateDatabase`    | `(record: any) => Promise<void>`                        | `undefined` | A function to handle record updates.                                  |
| `deleteRecord`      | `(id: string) => Promise<void>`                         | `undefined` | A function to handle record deletion.                                 |

## Events

The component emits the following custom events:

| Event    | Payload           | Description                                 |
| -------- | ----------------- | ------------------------------------------- |
| `create` | `{ record: any }` | Emitted when a new record is created.       |
| `update` | `{ record: any }` | Emitted when an existing record is updated. |
| `delete` | `{ id: string }`  | Emitted when a record is deleted.           |

## Styling

You can customize the appearance of the component by passing custom classes or overriding the default styles. For example:

    Crud tableClass="custom-table-class" />

## Development

To contribute to this project, clone the repository and install dependencies:

    git clone https://github.com/your-username/svelte-crud.git
    cd svelte-crud
    npm install

Run the development server:

    npm run dev

Build the package:

    npm run build

## License

This project is licensed under the MIT License.

## Acknowledgments

This component uses the following libraries:

- [SvelteKit](https://svelte.dev/docs/kit/introduction)
- [shadcn-svelte](https://next.shadcn-svelte.com/)
- [lucide-svelte](https://lucide.dev/guide/packages/lucide-svelte)
