<script>
	import { createEventDispatcher } from 'svelte';

	export let data = [];

	const dispatch = createEventDispatcher();

	function handleClick(crumb) {
		dispatch('click', crumb);
	}
</script>

<div>
	{#if data.length > 0}
		<nav class="inline-flex text-sm font-medium leading-[14px] lg:ml-4">
			{#each data as crumb, index}
				<div class="flex items-center">
					{#if index > 0}
						<img src="/svg/arrow-up.svg" alt="" class="mx-2.5 h-3 w-3" />
					{/if}
					{#if index === 0}
						<img src="/svg/home.svg" alt="" class="me-2.5 h-3 w-3" />
					{/if}
					{#if index === data.length - 1}
						<span class="text-Text-Tartiary cursor-default">{crumb.name}</span>
					{:else}
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<!-- svelte-ignore a11y-no-static-element-interactions -->
						<span class="text-Text-Secondary cursor-pointer no-underline">
							<a href={crumb.href} on:click={() => handleClick(crumb)} class="hover:no-underline">
								{crumb.name}
							</a>
						</span>
					{/if}
				</div>
			{/each}
		</nav>
	{/if}
</div>
