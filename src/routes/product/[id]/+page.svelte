<script>
	import { medusa } from '../../../scripts/medu.js';
	import { Card, Article, Button } from 'spaper';
	import { onMount } from 'svelte';
	import { page } from '$app/stores';

	let id = $page.params.id;
	let user = null;
	let product = null;
	let variants = null;
	let cartID = null;
	let cart = null;
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			user = user.customer;
		}

		product = await medusa.products.retrieve(id).catch((err) => {
			window.location.href = '/404';
		});

		if (product) {
			product = product.product;
		}
		variants = product.variants;
		cartID = localStorage.getItem('cartID');
		cart = await medusa.carts.retrieve(cartID);
        cart = cart.cart;
	});

    async function addToCart(variant){
        await medusa.carts.lineItems.create(cartID, {variant_id: variant.id, quantity: 1});
    }
</script>

<svelte:head>
	<title>{product ? product.title : 'Loading...'}</title>
	<meta name="description" content={product ? product.description : 'Loading...'} />
	<meta property="og:title" content={product ? product.title : 'Loading...'} />
	<meta property="og:description" content={product ? product.description : 'Loading...'} />
	<meta property="og:image" content={product ? product.thumbnail : 'Loading...'} />
</svelte:head>

{#if product}
	<Article title={product.title}>
		<img src={product.thumbnail} alt={product.title} />
		<p>{product.description}</p>
		<p />
		{#each variants as variant}
			<div class="container">
				<h2>Price: {variant.prices[0].amount}{variant.prices[0].currency_code}</h2>
				<Button
					class="button primary"
					on:click={addToCart(variant)}>Add to cart</Button
				>
			</div>
		{/each}
	</Article>
{/if}
