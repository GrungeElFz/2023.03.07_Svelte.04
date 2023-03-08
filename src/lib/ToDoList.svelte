<svelte:options immutable={true} />

<script>
	import Button from './Button.svelte';
	import { createEventDispatcher, onDestroy, onMount, afterUpdate, beforeUpdate } from 'svelte';

	onMount(() => {
		console.log('Component: Mounted');
	});

	onDestroy(() => {
		console.log('Component: Destroyed');
	});

	beforeUpdate(() => {
		if (listDiv) {
			console.log(`Element: Height.beforeUpdate -> ${listDiv.offsetHeight}px`);
		}
	});

	afterUpdate(() => {
		console.log(`Element: Height.afterUpdate -> ${listDiv.offsetHeight}px`);

		if (autoScroll) {
			listDiv.scrollTo(0, listDiv.scrollHeight);
		};

		autoScroll = false;
	});

	export let toDoLists = [];
	let prevToDoLists = toDoLists;

	$: {
		autoScroll = toDoLists.length > prevToDoLists.length;
		prevToDoLists = toDoLists;
	}

	export function clearInput() {
		inputText = '';
	}

	let listDiv, autoScroll;
	let inputText = '';
	const dispatch = createEventDispatcher();

	function handleAddToDoLists() {
		const isNotCancelled = dispatch(
			'addtodo',
			{
				title: inputText
			},
			{
				cancelable: true
			}
		);

		if (isNotCancelled) {
			inputText = '';
		}
	}

	function handleRemoveToDoLists(id) {
		dispatch('removetodo', {
			id
		});
	}

	function handleToggleToDoLists(id, value) {
		dispatch('toggletodo', {
			id,
			value
		});
	}
</script>

<div class="toDoLists-wrapper">
	<div class="toDoList" bind:this={listDiv}>
		<ul>
			{#each toDoLists as { id, title, completed } (id)}
				<li>
					<label>
						<input
							on:input={(event) => {
								event.currentTarget.checked = completed;
								handleToggleToDoLists(id, !completed);
							}}
							type="checkbox"
							checked={completed}
						/>
						{title}
					</label>
					<button on:click={() => handleRemoveToDoLists(id)}>Remove</button>
				</li>
			{/each}
		</ul>
	</div>

	<form class="toDoLists-form" on:submit|preventDefault={handleAddToDoLists}>
		<input bind:value={inputText} />
		<Button type="submit" disabled={!inputText}>Add</Button>
	</form>
</div>

<style>
	.toDoList {
		max-height: 15rem;
		overflow: auto;
	}
</style>