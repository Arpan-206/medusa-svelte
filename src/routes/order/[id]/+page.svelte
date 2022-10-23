<script>
    import { medusa } from '../../../scripts/medu.js';
    import { Card } from 'spaper';
    import { page } from '$app/stores';

    let order_id = $page.params.id;
    import { onMount } from 'svelte';

    let user = null;
    let orders = null;
    onMount(async () => {
        user = await medusa.auth.getSession();
        if (user) {
            user = user.customer;
        }

        orders = await medusa.orders.retrieve(order_id).catch((err) => {
            window.location.href = '/404';
        });
        if (orders) {
        console.log(orders);
    }
    });

    
</script>

<h2>Order</h2>
{#if orders}
    <Card>
        <h3>{orders.id}</h3>
        <p>{orders.status}</p>
    </Card>
{/if}