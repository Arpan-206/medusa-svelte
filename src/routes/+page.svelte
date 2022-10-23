<script>
	import { medusa } from '../scripts/medu.js';
	import { Card } from 'spaper';
	import { onMount } from 'svelte';

	let user = null;
	let products = null;
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			user = user.customer;
		}

		products = await medusa.products.list();
		if (products) {
			products = products.products;
            
		}
	});
</script>

<div class="container">
	<div class="container">
		{#if products}
			<h2>Products</h2>
			{#each products as product}
				<Card class="padding-large">
					<h3>{product.title}</h3>
					<p>{product.description}</p>
					<a href={'/product/' + product.id}>View</a>
				</Card>
				<br />
			{/each}
		{/if}
	</div>
</div>
