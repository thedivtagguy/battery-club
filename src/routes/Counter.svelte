<script>
	import { spring } from 'svelte/motion';
	import data from '$lib/data/battery.json';
	console.log(data);
	let  wattage = 0;
	$: battery_type = 'Mahabali';

	let batteryDataKey = 'Company';

	// Get all unique battery company names
	let companies = data.map((item) => item.Company);
	let uniqueCompanies = [...new Set(companies)];
	console.log(uniqueCompanies);



	// Data is an array of objects
	// Each object has a key of Company

	// Display the object where Company = 'Mahabali'

	$: batteryData = data.filter((item) => item.Company === battery_type);
	$: console.log(batteryData);

	// Calculate the wattage
	// Formula is ((10*"Amp Hours")/wattage)*60
	$: batteryLife = ((10 * batteryData[0]["Amp Hours"]) / wattage) * 60;

    // Function to 
	const displayed_count = spring();
	$: displayed_count.set(wattage);
	$: offset = modulo($displayed_count, 1);

	function modulo(n, m) {
		// handle negative numbers
		return ((n % m) + m) % m;
	}
</script>


<h2>Enter your battery's company</h2>

<!-- Dropdown to select display company names from data -->
<select bind:value={battery_type}>
	{#each uniqueCompanies as company}
		<option value={company}>{company}</option>
	{/each}
</select>

<h2> Enter your wattage</h2> 
<div class="counter">
	<button on:click={() => (wattage -= 1)} aria-label="Decrease the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5" />
		</svg>
	</button>

	

	<div class="counter-viewport">
		<div class="counter-digits" style="transform: translate(0, {100 * offset}%)">
			<strong class="hidden" aria-hidden="true">{Math.floor($displayed_count + 1)}</strong>
			<strong>{Math.floor($displayed_count)}</strong>
		</div>
	</div>

	<button on:click={() => (wattage += 1)} aria-label="Increase the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5 M0.5,0 L0.5,1" />
		</svg>
	</button>
</div>

<h2> Your battery life is: </h2>
<!-- Display battery life -->
<div class="counter">
	<div class="counter-viewport">
		<div class="counter-digits" >
			<p>
				<!-- Display if battery life is not INFINITY -->
				{#if batteryLife !== Infinity}
					{Math.floor(batteryLife)}
				{:else}
					Enter a wattage
				{/if}
			</p>
		</div>
	</div>
</div>

<style>
	.counter {
		display: flex;
		border-top: 1px solid rgba(0, 0, 0, 0.1);
		border-bottom: 1px solid rgba(0, 0, 0, 0.1);
		margin: 1rem 0;
	}

	.counter button {
		width: 2em;
		padding: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 0;
		background-color: transparent;
		touch-action: manipulation;
		font-size: 2rem;
	}

	.counter button:hover {
		background-color: var(--color-bg-1);
	}

	svg {
		width: 25%;
		height: 25%;
	}

	path {
		vector-effect: non-scaling-stroke;
		stroke-width: 2px;
		stroke: #444;
	}

	.counter-viewport {
		width: 8em;
		height: 4em;
		overflow: hidden;
		text-align: center;
		position: relative;
	}

	.counter-viewport strong {
		position: absolute;
		display: flex;
		width: 100%;
		height: 100%;
		font-weight: 400;
		color: var(--color-theme-1);
		font-size: 4rem;
		align-items: center;
		justify-content: center;
	}

	.counter-digits {
		position: absolute;
		width: 100%;
		height: 100%;
	}

	.hidden {
		top: -100%;
		user-select: none;
	}

	select {
		width: 40%;
		padding: 0.5rem;
		border: 1px solid #ccc;
		border-radius: 4px;
		box-sizing: border-box;
	}
</style>
