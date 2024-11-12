<script lang="ts">
	import Messages from '$lib/Messages.svelte';
	import Login from '$lib/Login.svelte';
	import ThemeToggle from '$lib/ThemeToggle.svelte';
</script>

<div class="p-8">
	<div class="mb-4 flex gap-6">
		<h1 class="text-5xl font-bold">Chat App</h1>
		<ThemeToggle />
	</div>

	<Login />
	<Messages />
</div>
