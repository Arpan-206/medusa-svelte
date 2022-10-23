<script>
	import { medusa } from '../../scripts/medu.js';
	import { Button, Card, Modal, Form, Input } from 'spaper';
	import { onMount } from 'svelte';
	import { PUBLIC_STRIPE_KEY } from '$env/static/public';
	import { loadStripe } from '@stripe/stripe-js';
	import { Elements, CardNumber, CardExpiry, CardCvc } from 'svelte-stripe';

	let stripe = null;
    let cardElement = null;
	let user = null;
	let cart = null;
	let cartID = null;
	let addrModalOpen = false;
	let paymentSession = null;
    
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			user = user.customer;
		}

		cartID = localStorage.getItem('cartID');
		cart = await medusa.carts.retrieve(cartID);
		cart = cart.cart;
        console.log(cart);
		paymentSession = cart.payment_session;

		stripe = await loadStripe(PUBLIC_STRIPE_KEY);
	});

	async function checkout() {
		let hi = await medusa.carts.complete(cartID);
		console.log(hi);
	}
	function openAddrModal() {
		addrModalOpen = true;
	}

	async function addNewAddress(event) {
		event.preventDefault();
		let formData = new FormData(event.target);
		let data = Object.fromEntries(formData);
		await medusa.carts.update(cartID, {
			shipping_address: {
				company: data.company,
				first_name: data.first_name,
				last_name: data.last_name,
				address_1: data.address_1,
				address_2: data.address_2,
				city: data.city,
				country_code: data.country_code,
				province: data.province,
				postal_code: data.postal_code,
				phone: data.phone
			}
		});
		window.location.reload();
	}

    async function submit() {
        console.log(cardElement);
    }
</script>

<h2>My Cart</h2>

{#if cart}
	<Button type="primary" on:click={checkout}>Checkout</Button>
	<br />
	{#each cart.items as item}
		<Card>
			<h3>{item.title}</h3>
			<p>{item.description}</p>
			<p>
				Quantity: {item.quantity}
			</p>
			<p>Price: {item.unit_price}</p>
		</Card>
		<br />
	{/each}

	<div class="container">
		<h4>Shipping address</h4>
		{#if !cart.shipping_address.address_1}
			<Button type="primary" on:click={openAddrModal}>Add shipping address</Button>
		{/if}
		<Modal bind:active={addrModalOpen} title="Add shipping address" style="width: 75%;">
			<Form id="shippingAddrForm" on:submit={addNewAddress}>
				<Input block name="first_name" placeholder="First name" required />
				<Input block name="last_name" placeholder="Last name" required />
				<Input block name="address_1" placeholder="Address line 1" required type="textarea" />
				<Input block name="address_2" placeholder="Address line 2" required type="textarea" />
				<Input block name="city" placeholder="City" required />
				<select id="country_code" block name="country_code">
					<option value="">Select country...</option>
					<option value="IN">India</option>
				</select>
				<Input block name="province" placeholder="Province" required />
				<Input block name="company" placeholder="Company name" required />
				<Input block name="postal_code" placeholder="Postal code" required />
				<Input block name="phone" placeholder="Phone" />
				<Button type="submit">Submit</Button>
			</Form>
		</Modal>
		{#if cart.shipping_address.address_1}
			<p>{cart.shipping_address.first_name} {cart.shipping_address.last_name}</p>
			<p>{cart.shipping_address.address_1}</p>
			<p>{cart.shipping_address.address_2}</p>
			<p>{cart.shipping_address.city}</p>
			<p>{cart.shipping_address.country_code}</p>
			<p>{cart.shipping_address.province}</p>
			<p>{cart.shipping_address.postal_code}</p>
			<p>{cart.shipping_address.phone}</p>
		{/if}
	</div>

	<div class="container">
		<h4>Payment methods</h4>
		{#if paymentSession}
				<Card>
					<h3>{paymentSession.provider_id}</h3>
					{#if stripe}
						<Elements {stripe}>
							<form on:submit|preventDefault={submit}>
								<CardNumber bind:element={cardElement} />
								<CardExpiry />
								<CardCvc />

								<Button>Pay</Button>
							</form>
						</Elements>
					{/if}
				</Card>
				<br />
		{/if}
	</div>
{/if}
