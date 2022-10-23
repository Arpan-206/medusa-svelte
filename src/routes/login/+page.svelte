<script>
	import { medusa } from '../../scripts/medu.js';
	import { Form, Button, Input, Alert } from 'spaper';
	import { onMount } from 'svelte';

	let user = null;
	let alertMsg = null;
	onMount(async () => {
		user = await medusa.auth.getSession();
		if (user) {
			window.location.href = '/';
		}
	});
	function login(e) {
		e.preventDefault();

		const data = new FormData(e.target);
		const email = data.get('email');
		const password = data.get('password');
		medusa.auth
			.authenticate({ email: email, password: password })
			.catch((err) => {
				alertMsg = 'Invalid email or password';
			})
			.then((res) => {
				if (res) {
					window.location.href = '/';
				}
			});
	}
</script>

<div class="container">
	<h2>Login</h2>
	{#if alertMsg}
		<Alert type="danger" dismissible>{alertMsg}</Alert>
	{/if}
	<Form on:submit={login}>
		<Input type="email" name="email" placeholder="Email" block required />
		<br />
		<Input type="password" name="password" placeholder="Password" block required />
		<br />
		<Button>Submit</Button>
	</Form>
</div>
