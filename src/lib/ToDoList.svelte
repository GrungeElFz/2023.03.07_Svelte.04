<svelte:options immutable={true} />

<script>
	import Button from './Button.svelte';
	import { createEventDispatcher, afterUpdate } from 'svelte';

	afterUpdate(() => {
		console.log(`Element: Height.afterUpdate -> ${listDiv.offsetHeight}px`);

		if (autoScroll) {
			listDiv.scrollTo(0, listDivScrollHeight);
		}

		autoScroll = false;
	});

	export let toDoLists = [];
	let prevToDoLists = toDoLists;
	let inputText = '';
	let listDiv, listDivScrollHeight, autoScroll;

	const dispatch = createEventDispatcher();

	$: {
		autoScroll = toDoLists.length > prevToDoLists.length;
		prevToDoLists = toDoLists;
	}

	export function clearInput() {
		inputText = '';
	}

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
		<div bind:offsetHeight={listDivScrollHeight}>
			{#if toDoLists.length === 0}
				<p class="no-item-text">Nothing to do 🤷‍♀️</p>
			{:else}
				<ul>
				{#each toDoLists as { id, title, completed } (id)}
					<li class:completed>
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
			{/if}
		</div>
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
