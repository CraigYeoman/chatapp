<script lang="ts">
	import { currentUser, pb } from './pocketbase';
	import { ClientResponseError } from 'pocketbase';

	let loginToggle: boolean = false;
	let username: string;
	let password: string;
	let error: string;

	function toggleLogin() {
		loginToggle = !loginToggle;
	}

	async function login() {
		error = '';
		try {
			await pb.collection('users').authWithPassword(username, password);
		} catch (err) {
			if (err instanceof ClientResponseError) {
				error = 'Invalid username or password';
			} else {
				error = 'An unexpected error occurred';
			}
		}
	}

	async function signUp() {
		try {
			const result = await pb.health.check();
			console.log('Connected to PocketBase:', result);
		} catch (err) {
			console.error('PocketBase Error:', err);
		}

		try {
			const data = {
				username,
				password,
				passwordConfirm: password
			};
			const createdUser = await pb.collection('users').create(data);
			await login();
		} catch (err) {
			if (err instanceof ClientResponseError) {
				console.log(err.data);
				if (err.data?.data?.username?.code === 'validation_invalid_username') {
					error = 'The username is invalid or already in use';
				} else if (err.data?.data?.password?.code === 'validation_length_out_of_range') {
					error = 'The length must be between 8 and 72.';
				} else {
					error = err.message;
				}
			} else {
				error = 'An unexpected error occurred';
			}
		}
	}

	function signOut() {
		pb.authStore.clear();
		username = '';
		password = '';
		error = '';
	}
</script>

<div class="mb-4">
	{#if $currentUser}
		<p>
			Signed in as {$currentUser.username}
			<button class="btn btn-accent" on:click={signOut}>Sign Out</button>
		</p>
	{:else}
		<form class="flex w-32 flex-col gap-4" on:submit|preventDefault>
			<input placeholder="Username" type="text" bind:value={username} />

			<input placeholder="Password" type="password" bind:value={password} />
			{#if !loginToggle}
				<button class="btn btn-accent" on:click={signUp}>Sign Up</button>
				<button class="underline" on:click={toggleLogin}>Login</button>
			{:else}
				<button class="btn btn-accent" on:click={login}>Login</button>
				<button class="underline" on:click={toggleLogin}>Create an account</button>
			{/if}
		</form>
	{/if}

	{#if error}
		<p class="text-red-500">{error}</p>
	{/if}
</div>
