<script>
	import GithubAwait from './GithubAwait.svelte';
	// import Github from './Github.svelte';
	import {setContext, onMount, afterUpdate} from 'svelte';

	import Navbar from './Navbar.svelte';
	import ExpensesList from './ExpensesList.svelte';
	// import expensesData from './expenses';
	import Totals from './Totals.svelte';
	import ExpenseForm from './ExpenseForm.svelte';
	import Modal from './Modal.svelte';

	let expenses = [];

	let setName = "";
	let setAmount = null;
	let setId = null;

	let isFormOpen = false;

	// reactive
	$: isEditing = setId ? true : false;
	$: total = expenses.reduce((acc, curr) => {
		return (acc += curr.amount)
	}, 0)
	let total = 0;

	/** Functions */

	function showForm() {
		isFormOpen = true;
	}

	function hideForm() {
		isFormOpen = false;
		setName = "";
		setAmount =  null;
		setId = null;
	}

	function removeExpense(id) {
		expenses = expenses.filter(item => item.id !== id);
	}

	function clearExpenses() {
		expenses=[];
	}

	function addExpense({name, amount }) {
		let expense = {id:Math.random() * Date.now(), name, amount};
		expenses = [expense, ...expenses];
	}

	function setModifiedexpense(id) {
		let expense = expenses.find(item => item.id === id);

		setId = expense.id;
		setName = expense.name;
		setAmount = expense.amount;

		showForm();
	}

	setContext('remove', removeExpense);
	setContext('modify', setModifiedexpense);

	// Local storage
	function setLocalStorage() {
		localStorage.setItem("expenses", JSON.stringify(expenses));
	}

	function deleteExpense(event) {
		const {id, name} = event.detail
		console.log(name);

		removeExpense(id);
	}

	function editExpense({name, amount}) {
		expenses = expenses.map(item => {
			return item.id === setId ? {...item,name, amount } : {...item}
		});

		setId = null;
		setAmount = null;
		setName = "";
	}

	onMount(() => {
		expenses = localStorage.getItem('expenses')?
			JSON.parse(localStorage.getItem('expenses'))
		: [];
	});

	afterUpdate(() => {
		console.log("after update");
		setLocalStorage();
	})
</script>

<Navbar {showForm} />
<main class="content">
	<GithubAwait />
	<!-- {#if isFormOpen}
		<Modal> 
			<ExpenseForm {addExpense} name={setName} amount={setAmount} {isEditing} {editExpense} {hideForm} />
		</Modal>
		
	{/if}
	
	<Totals title="total expenses" {total} />
	<ExpensesList {expenses}/>

	<button type="button" class="btn btn-primary btn-block" on:click={clearExpenses}>clear expenses</button> -->
</main>

