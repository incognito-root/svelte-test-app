<!-- Filters.svelte -->
<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	let isAvailable = true;
	let selectedTier = 'any';
	let selectedGender = 'any';
	let selectedEthnicity = 'any';
	let selectedTimeframe = 'within2months';

	function handleClose() {
		dispatch('close');
	}

	function handleReset() {
		isAvailable = true;
		selectedTier = 'any';
		selectedGender = 'any';
		selectedEthnicity = 'any';
		selectedTimeframe = 'within2months';
	}

	function handleShowResults() {
		dispatch('showResults', {
			isAvailable,
			selectedTier,
			selectedGender,
			selectedEthnicity,
			selectedTimeframe
		});
	}
</script>

<div class="filter-card-main rounded-lg bg-white p-6 shadow-lg">
	<div class="mb-6 flex items-center justify-between">
		<h2 class="text-xl font-semibold">Filters</h2>
		<button class="text-gray-500 hover:text-gray-700" on:click={handleClose}>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				width="20"
				height="20"
				viewBox="0 0 24 24"
				fill="none"
				stroke="currentColor"
				stroke-width="2"
				stroke-linecap="round"
				stroke-linejoin="round"
			>
				<line x1="18" y1="6" x2="6" y2="18"></line>
				<line x1="6" y1="6" x2="18" y2="18"></line>
			</svg>
		</button>
	</div>

	<div class="mb-6">
		<h3 class="mb-2 text-sm font-medium">Availability</h3>
		<div class="flex items-center space-x-2">
			<input
				type="checkbox"
				id="available"
				bind:checked={isAvailable}
				class="rounded border-gray-300 text-blue-500 focus:ring-blue-500"
			/>
			<label for="available" class="text-sm font-medium leading-none"> Available </label>
		</div>
	</div>

	<div class="mb-6">
		<h3 class="mb-2 text-sm font-medium">Tier</h3>
		<select
			bind:value={selectedTier}
			class="w-full rounded-md border border-gray-300 bg-white py-2 pl-3 pr-10 text-sm focus:border-blue-500 focus:outline-none focus:ring-1 focus:ring-blue-500"
		>
			<option value="any">Any</option>
			<option value="tier1">Tier 1</option>
			<option value="tier2">Tier 2</option>
			<option value="tier3">Tier 3</option>
		</select>
	</div>

	<div class="mb-6">
		<h3 class="mb-2 text-sm font-medium">Gender</h3>
		<select
			bind:value={selectedGender}
			class="w-full rounded-md border border-gray-300 bg-white py-2 pl-3 pr-10 text-sm focus:border-blue-500 focus:outline-none focus:ring-1 focus:ring-blue-500"
		>
			<option value="any">Any</option>
			<option value="male">Male</option>
			<option value="female">Female</option>
			<option value="other">Other</option>
		</select>
	</div>

	<div class="mb-6">
		<h3 class="mb-2 text-sm font-medium">Ethnicity</h3>
		<select
			bind:value={selectedEthnicity}
			class="w-full rounded-md border border-gray-300 bg-white py-2 pl-3 pr-10 text-sm focus:border-blue-500 focus:outline-none focus:ring-1 focus:ring-blue-500"
		>
			<option value="any">Any</option>
			<option value="asian">Asian</option>
			<option value="black">Black</option>
			<option value="hispanic">Hispanic</option>
			<option value="middleEastern">Middle Eastern</option>
			<option value="white">White</option>
		</select>
	</div>

	<div class="mb-6">
		<h3 class="mb-2 text-sm font-medium">Last booked</h3>
		<select
			bind:value={selectedTimeframe}
			class="w-full rounded-md border border-gray-300 bg-white py-2 pl-3 pr-10 text-sm focus:border-blue-500 focus:outline-none focus:ring-1 focus:ring-blue-500"
		>
			<option value="within2months">Within 2 months</option>
			<option value="within6months">Within 6 months</option>
			<option value="within1year">Within 1 year</option>
		</select>
	</div>

	<div class="flex space-x-4">
		<button
			on:click={handleShowResults}
			class="flex-1 rounded-md bg-blue-500 px-4 py-2 text-sm font-medium text-white hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
		>
			Show 32 results
		</button>
		<button
			on:click={handleReset}
			class="flex-1 rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
		>
			Reset
		</button>
	</div>
</div>

<style>
	.filter-card-main {
		width: 400px;
	}

	@media screen and (max-width: 768px) {
		.filter-card-main {
			width: 350px;
            margin-left: -230px;
		}
	}
</style>
