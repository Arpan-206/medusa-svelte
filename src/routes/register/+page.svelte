<script>
	import { medusa } from '../../scripts/medu.js';
	import { Form, Button, Input } from 'spaper';
    import { onMount } from 'svelte';

    let user = null;
    onMount(async () => {
        user = await medusa.auth.getSession();
        if (user) {
            window.location.href = '/';
        }
    });
	function register(e) {
		e.preventDefault();

		const data = new FormData(e.target);
		const email = data.get('email');
		const password = data.get('password');
		const first_name = data.get('first_name');
		const last_name = data.get('last_name');
		medusa.customers
			.create({ email: email, password: password, first_name: first_name, last_name: last_name })
			.then((res) => {
				console.log(res);
			});
        
        window.location.href = '/login';
	}
</script>

<div class="container">
	<h2>Register</h2>
	<Form on:submit={register}>
		<Input type="text" name="first_name" placeholder="First Name" block required />
		<br />
		<Input type="text" name="last_name" placeholder="Last Name" block required />
		<br />
		<Input type="email" name="email" placeholder="Email" block required />
		<br />
		<Input type="password" name="password" placeholder="Password" block required />
		<br />
		<Button>Submit</Button>
	</Form>
</div>
