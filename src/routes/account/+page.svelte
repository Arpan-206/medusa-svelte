<script>
	import { medusa } from '../../scripts/medu.js';
	import { Tabs, Tab, Button, Modal, Input, Form } from 'spaper';
	import { onMount } from 'svelte';

	let user = null;
	let orders = null;
	let addresses = null;
	let paymentMethods = null;
	let addrModalOpen = false;
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			user = user.customer;
		} else {
			window.location.href = '/login';
		}

		orders = await user.orders;

		addresses = await user.addresses;

		paymentMethods = await user.paymentMethods.list();
	});

	function openAddrModal() {
		addrModalOpen = true;
	}
</script>

<div class="container">
	{#if user}
		<Tabs>
			<Tab label="My Details">
				<div class="container">
					<h2>Account</h2>
					<p>First Name: {user.first_name}</p>
					<p>Last Name: {user.last_name}</p>
					<p>Email: {user.email}</p>
				</div>
			</Tab>
			<Tab label="My Orders">
				<div class="container">
					<h2>Orders</h2>

					{#if orders}
						{#each orders as order}
							<p>{order.id}</p>
						{/each}
					{:else}
						<p>Orders will be listed here</p>
					{/if}
				</div>
			</Tab>
			<Tab label="My Addresses">
				<div class="container">
					<h2>Addresses</h2>
					<Modal bind:active={addrModalOpen} title="Add shipping address" style="width: 75%;">
						<Form>
                            <Input block name="first_name" placeholder="First name" required />
                            <Input block name="last_name" placeholder="Last name" required />
                            <Input block name="address_1" placeholder="Address line 1" required type="textarea" />
                            <Input block name="address_2" placeholder="Address line 2" required type="textarea" />
                            <Input block name="city" placeholder="City" required />
                            <select id="country_code" block name="country_code">
                                <option value="">Select country...</option>
                                <option value="NO">Norway</option>
                                <option value="FI">Finland</option>
                                <option value="SE">Sweden</option>
                                <option value="DK">Denmark</option>
                            </select>
                            <Input block name="region" placeholder="Region" required />
                            <Input block name="company" placeholder="Company name" required />
                            <Input block name="postal_code" placeholder="Postal code" required />
                            <Input block name="phone" placeholder="Phone" />
                            <Button type="submit">Submit</Button>
                        </Form>
                            
                              
					</Modal>
					<Button type="primary" on:click={openAddrModal}>Create Address</Button>
					{#if addresses}
						{#each addresses as address}
							<p>{address.first_name}</p>
							<p>{address.last_name}</p>
						{/each}
					{:else}
						<p>Addresses will be listed here</p>
					{/if}
				</div>
			</Tab>
			<Tab label="Payment Methods">
				<div class="container">
					<h2>Payment Methods</h2>
					{#if paymentMethods}
						<!-- {#each paymentMethods as paymentMethod}
							<p>{paymentMethod.id}</p>
						{/each} -->
					{:else}
						<p>Payment Methods will be listed here</p>
					{/if}
				</div>
			</Tab>
		</Tabs>
	{/if}
</div>
