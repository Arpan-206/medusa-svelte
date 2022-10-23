<script>
	import 'papercss/dist/paper.min.css';
	import { Navbar, Button } from 'spaper';
	import { medusa } from '../scripts/medu.js';
	import { onMount } from 'svelte';
	let user = null;

	let cart = null;
	let cartID = null;
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			user = user.customer;
		}

		cartID = localStorage.getItem('cartID');
        console.log(cartID);
		if (cartID) {
			cart = await medusa.carts.retrieve(cartID);
            cart = cart.cart;
			cartID = cart.id;
		} else {
			cart = await medusa.carts.create();
            cart = cart.cart;
			cartID = cart.id;
			localStorage.setItem('cartID', cartID);
		}
	});

	async function logout() {
		await medusa.auth.deleteSession();
		window.location.href = '/';
	}
</script>

<Navbar border={false}>
	<h3 slot="brand">
		<a href="/">Medusvelte</a>
	</h3>
	<ul class="inline">
		{#if user}
			<li><a href="/account">Hello, {user.first_name}</a></li>
			<li><a href="/cart"><i class="fa-solid fa-cart-shopping" /></a></li>
			<li><Button type="danger" on:click={logout}>Logout</Button></li>
		{:else}
			<li><a href="/login">Login</a></li>
			<li><a href="/register">Register</a></li>
		{/if}
	</ul>
</Navbar>

<main class="container">
	<slot />
</main>

<footer class="container">
	<div class="row">
		<div class="col-4 col" />
		<div class="col-4 col">
			<p>Powered by Medusa</p>
		</div>
		<div class="col-4 col" />
	</div>
</footer>
