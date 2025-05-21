<script lang="ts">
	export let data;

	let chats = data.chats;
	let sidebarOpen = true;

	let currentChat = chats[0];
</script>

<main class="flex h-screen">
	<div class="w-64 bg-gray-200 flex flex-col {sidebarOpen ? '' : 'hidden'}">
		{#each chats as c}
			<div>{c.title}</div>
		{/each}
	</div>
	<div class="flex-1 flex flex-col bg-cyan-700">
		<button on:click={() => (sidebarOpen = !sidebarOpen)}>ß</button>

		<div class="flex-1 overflow-y-auto space-y-4">
			{#each currentChat.messages as m}
				<div
					class="flex {m.userType == 'human'
						? 'justify-end bg-gray-400 rounded-lg'
						: 'justify-start bg-white rounded-lg'}"
				>
					<div>{m.content}</div>
				</div>
			{/each}
		</div>
	</div>
</main>
