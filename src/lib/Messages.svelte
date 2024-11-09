<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { currentUser, pb } from './pocketbase';
	import { format, formatDistance, formatRelative, parseISO } from 'date-fns';

	let newMessage: string;
	let messages = [];
	let unsubscribe: () => void;

	onMount(async () => {
		const resultList = await pb.collection('messages').getList(1, 50, {
			sort: 'created',
			expand: 'user'
		});
		messages = resultList.items;

		unsubscribe = await pb.collection('messages').subscribe('*', async ({ action, record }) => {
			if (action === 'create') {
				const user = await pb.collection('users').getOne(record.user);
				record.expand = { user };
				messages = [...messages, record];
			}
			if (action === 'delete') {
				messages = messages.filter((m) => m.id !== record.id);
			}
		});
	});

	onDestroy(() => {
		unsubscribe?.();
	});

	async function sendMessage() {
		const data = {
			text: newMessage,
			user: $currentUser.id
		};
		const createdMessage = await pb.collection('messages').create(data);
		newMessage = '';
	}
</script>

<form on:submit|preventDefault={sendMessage}>
	<input placeholder="Message" type="text" bind:value={newMessage} />
	<button class="btn btn-primary" type="submit">Send</button>
</form>

<div class="messages">
	{#each messages as message (message.id)}
		<div class="chat chat-start">
			<div class="chat-image avatar">
				<div class="w-10 rounded-full">
					<img alt="avatar" src={`https://api.dicebear.com/9.x/bottts/svg?seed=Felix`} />
				</div>
			</div>
			<div class="chat-header">
				@{message.expand?.user?.username}
				<time class="text-xs opacity-50">{format(parseISO(message.created), 'PPpp')}</time>
			</div>
			<div class="chat-bubble">{message.text}</div>
		</div>

		<!-- <div class="msg">
			<img
				class="avatar"
				src={`https://avatars.dicebear.com/api/identicon/${message.expand?.user?.username}.svg`}
				alt="avatar"
				width="40px"
			/>
			<div>
				<small>
					Sent by @{message.expand?.user?.username}
				</small>
				<p class="msg-text">{message.text}</p>
			</div>
		</div> -->
	{/each}
</div>
