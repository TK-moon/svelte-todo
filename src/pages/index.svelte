<script>
	import { onMount } from 'svelte';
	import { fade, slide } from 'svelte/transition';

	import moment from 'moment';

	import Date from '../components/date.svelte'
	import ListItem from '../components/listItem.svelte'
	import TaskInput from '../components/taskInput.svelte'
	import AddButton from '../components/addButton.svelte'

	let today = moment();
	let todoList = [];
	let inputValue = '';

	$: all = todoList.length;
	$: done = todoList.filter((v) => v.completed).length;

	fetch("https://jsonplaceholder.typicode.com/todos?userId=1")
		.then(response => response.json())
		.then(json => {
			todoList = json;
		});

	onMount(() => {
		// indexedDB
	});

	const addTask = async (event) => {
		event.preventDefault()
		if (inputValue === '') return;
		todoList = [{ title: inputValue, completed: false }, ...todoList];
		inputValue = '';
		document.getElementById("contents").scrollTop = 0;
	}

	const completedTask = (index, value) => {
		todoList[index] = {...todoList[index], completed: value}
		console.log(index, value);
	}

	const deleteTask = (index) => {
		todoList.splice(index, 1);
		todoList = todoList;
	}
</script>

<div id="container">
	<header id="header" transition:fade>
		<Date today={today}></Date>
		<p class="text-right">{all} tasks</p>
		<p class="text-right">{done} completed</p>
		<p class="text-right">{all - done} left</p>
	</header>
	<main id="contents" transition:slide>
		{#if todoList.length}
			{#each todoList as item, index (`list-${index}`)}
				<ListItem title={item.title} completed={item.completed} index={index} completedTask={completedTask} deleteTask={deleteTask}></ListItem>
			{/each}
		{:else}
			No tasks
		{/if}
	</main>
	<footer id="footer">
		<form id="footerWrapper" on:submit={addTask}>
			<TaskInput bind:value={inputValue} placeholder="Task"></TaskInput>
			<AddButton onClick={addTask}></AddButton>
		</form>
	</footer>
</div>

<style lang="scss">
	#container {
		font-size: 16px;
		position: relative;
		margin: 0 auto;
		width: 100vw;
		height: 100vh;
		overflow: hidden;
		display: flex;
		flex-direction: column;
	}
	#header {
		padding: 20px;
		box-shadow: 0 0 30px 0 #ddd;
		z-index: 10;
		p {
			font-size: 14px;
			color: #999;
			font-weight: 300;
		}
	}
	#contents {
		display: flex;
		flex-direction: column;
		flex: 1;
		align-items: center;
		padding: 20px;
		background-color: #eee;
		overflow: scroll;
		padding-bottom: 70px;
	}
	#footer {
		position: fixed;
		bottom: 20px;
		width: 100%;
	}
	#footerWrapper {
		display: flex;
		padding: 0 20px;
	}
	.text-right {
		text-align: right;
	}
</style>
