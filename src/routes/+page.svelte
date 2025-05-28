<script lang="ts">
	import { ChatOpenAI } from '@langchain/openai';

	export let data;

	let chats = data.chats;
	let sidebarOpen = true;
	let messages = [];
	let currentChatId: number | null = null;

	let newMessages = '';
	let isLoading = false;

	function switchChat(newChatId: number) {
		const chat = chats.find((c) => c.id === newChatId);

		messages = chat.messages;
		currentChatId = newChatId;
	}
	async function handleSubmit() {
		isLoading = true;
		messages = [...messages, { content: newMessages, role: 'human' }];
		const messageContent = newMessages;
		newMessages = '';

		const response = await fetch('/api/chat', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({
				messages: messageContent,
				ChatId: currentChatId
			})
		});

		const data = await response.json();

		messages = [...messages, { content: data.response, role: 'assistant' }];

		if (currentChatId !== data.chatId) {
			chats = [{ id: data.chatId, title: 'New Chat', messages: messages }, ...chats];
			currentChatId = data.chatId;
		} else {
			chats = chats.map((c) => {
				if (c.id === data.chatId) {
					return {
						...c,
						messages: messages
					};
				}
				return c;
			});
		}
		isLoading = false;
	}
</script>

<main class="flex h-screen">
	<div class="w-64 bg-gray-200 flex flex-col {sidebarOpen ? '' : 'hidden'}">
		{#each chats as c}
			<button
				on:click={() => switchChat(c.id)}
				class="w-full p-2 rounded-sm {currentChatId == c.id ? 'bg-slate-300' : 'bg-slate.200'}"
				>{c.title}</button
			>
		{/each}
	</div>
	<div class="flex-1 flex flex-col bg-cyan-700">
		<button on:click={() => (sidebarOpen = !sidebarOpen)}>ß</button>

		<div class="flex-1 overflow-y-auto space-y-4">
			{#each messages as m}
				<div
					class="flex {m.userType == 'human'
						? 'justify-end bg-gray-400 rounded-lg'
						: 'justify-start bg-white rounded-lg'}"
				>
					<div>{m.content}</div>
				</div>
			{/each}
		</div>

		<form class="bg-slate-200 p-4 flex" on:submit={handleSubmit}>
			<input
				class="flex-1 border border-black rounded-md"
				bind:value={newMessage}
				placeholder="Ask anything ...."
			/>
			<button
				class=" text-white bg-blue-600 px-2 py-2 rounded-md"
				type="submit"
				disabled={isLoading}>submit</button
			>
		</form>
	</div>
</main>
