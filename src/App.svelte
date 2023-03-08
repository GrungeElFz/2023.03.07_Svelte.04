<script>
	import ToDoList from './lib/ToDoList.svelte';
	import { v4 as uuid } from 'uuid';

	let showList = true;
	let toDoListBox;
	let toDoLists = [
		{
			id: uuid(),
			title: 'First task',
			completed: true
		},
		{
			id: uuid(),
			title: 'Second task',
			completed: false
		},
		{
			id: uuid(),
			title: 'Third task',
			completed: false
		}
	];

	function handleAddToDoLists(event) {
		event.preventDefault();

		toDoLists = [
			...toDoLists,
			{
				id: uuid(),
				title: event.detail.title,
				completed: false
			}
		];

		toDoListBox.clearInput();
	}

	function handleRemoveToDoLists(event) {
		toDoLists = toDoLists.filter((toDoItem) => toDoItem.id !== event.detail.id);
	}

	function handleToggleToDoLists(event) {
		toDoLists = toDoLists.map((toDoItem) => {
			if (toDoItem.id === event.detail.id) {
				return { ...toDoItem, completed: event.detail.value };
			}
			return { ...toDoItem };
		});
	}
</script>

<h2>{toDoLists.length} Tasks</h2>

<label>
	<input type="checkbox" bind:checked={showList} />
	Show/Hide Tasks
</label>

{#if showList}
	<div style:max-width="20rem">
		<ToDoList
			{toDoLists}
			bind:this={toDoListBox}
			on:addtodo={handleAddToDoLists}
			on:removetodo={handleRemoveToDoLists}
			on:toggletodo={handleToggleToDoLists}
		/>
	</div>
{/if}

<style>
</style>
