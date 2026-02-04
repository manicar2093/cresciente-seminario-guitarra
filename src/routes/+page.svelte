<script lang="ts">
	import data from '$lib/assets/data.json';
	import { onMount } from 'svelte';

	let notas_mapeo_checkbox = $state(true);
	let intervalos_checkbox = $state(true);
	let triadas_checkbox = $state(true);
	let escalas_checkbox = $state(true);
	let caged_checkbox = $state(true);
	let acordes_complejos_checkbox = $state(true);
	
	let todos_seleccionados = $derived(
		notas_mapeo_checkbox &&
		intervalos_checkbox &&
		triadas_checkbox &&
		escalas_checkbox &&
		caged_checkbox &&
		acordes_complejos_checkbox
	);

	let selectedItem = $state(data[0]);

	function selectAll() {
		notas_mapeo_checkbox = true;
		intervalos_checkbox = true;
		triadas_checkbox = true;
		escalas_checkbox = true;
		caged_checkbox = true;
		acordes_complejos_checkbox = true;
	}

	function deselectAll() {
		notas_mapeo_checkbox = false;
		intervalos_checkbox = false;
		triadas_checkbox = false;
		escalas_checkbox = false;
		caged_checkbox = false;
		acordes_complejos_checkbox = false;
	}

	function getRandomItem() {

		const selectedTitles: string[] = [];
		if (notas_mapeo_checkbox) selectedTitles.push('NOTAS Y MAPEO');
		if (intervalos_checkbox) selectedTitles.push('INTERVALOS');
		if (triadas_checkbox) selectedTitles.push('TRÍADAS');
		if (escalas_checkbox) selectedTitles.push('ESCALAS');
		if (caged_checkbox) selectedTitles.push('CAGED');
		if (acordes_complejos_checkbox) selectedTitles.push('ACORDES COMPLEJOS');

		const filteredData = data.filter((item) => selectedTitles.includes(item.title));

		if (filteredData.length > 0) {
			const randomIndex = Math.floor(Math.random() * filteredData.length);
			selectedItem = filteredData[randomIndex];
		} else {
			// Si no hay nada seleccionado, seleccionar de todos por defecto o mantener el actual
			const randomIndex = Math.floor(Math.random() * data.length);
			selectedItem = data[randomIndex];
		}
	}

	onMount(() => {
		getRandomItem();
	});
</script>

<div class="flex flex-col md:flex-row items-center md:items-start justify-center p-8 gap-8">
	<div class="random-form bg-white p-6 rounded-lg shadow-md w-full max-w-md">
		<div class="options flex flex-col justify-center space-y-2">
			<div class="flex items-center space-x-2">
				<input
					bind:checked={notas_mapeo_checkbox}
					type="checkbox"
					id="notas-mapeo"
					name="notas-mapeo"
				/>
				<label for="notas-mapeo">Notas y mapeo</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={intervalos_checkbox}
					type="checkbox"
					id="intervalos"
					name="intervalos"
				/>
				<label for="intervalos">Intervalos</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={triadas_checkbox}
					type="checkbox"
					id="triadas"
					name="triadas"
				/>
				<label for="triadas">Triadas</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={escalas_checkbox}
					type="checkbox"
					id="escalas"
					name="escalas"
				/>
				<label for="escalas">Escalas</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={caged_checkbox}
					type="checkbox"
					id="caged"
					name="caged"
				/>
				<label for="caged">CAGED</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={acordes_complejos_checkbox}
					type="checkbox"
					id="acordes-complejos"
					name="acordes-complejos"
				/>
				<label for="acordes-complejos">Acordes complejos</label>
			</div>
		</div>
		<button
			onclick={getRandomItem}
			class="mt-6 w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md cursor-pointer flex items-center justify-center gap-2"
		>
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
				class="lucide lucide-shuffle"
				><path
					d="M2 18h1.4c1.3 0 2.5-.6 3.3-1.7l6.1-8.6c.7-1.1 2-1.7 3.3-1.7H22"
				/><path d="m18 2 4 4-4 4" /><path d="M2 6h1.9c1.5 0 2.9.9 3.6 2.2" /><path
					d="M22 18h-5.9c-1.3 0-2.6-.7-3.3-1.8l-.5-.8"
				/><path d="m18 14 4 4-4 4" /></svg
			>
			Aleatorio
		</button>
		<div class="flex flex-row space-x-1 mt-3">
			<button
				onclick={selectAll}
				disabled={todos_seleccionados}
				class="w-full bg-emerald-600 hover:bg-emerald-700 disabled:bg-gray-400 disabled:cursor-not-allowed text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md flex items-center justify-center gap-2 text-sm"
			>
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
					class="lucide lucide-check-square"
					><polyline points="9 11 12 14 22 4" /><path
						d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"
					/></svg
				>
				Seleccionar Todos
			</button>
			<button
				onclick={deselectAll}
				disabled={!todos_seleccionados}
				class="w-full bg-emerald-600 hover:bg-emerald-700 disabled:bg-gray-400 disabled:cursor-not-allowed text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md flex items-center justify-center gap-2 text-sm"
			>
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
					class="lucide lucide-square"
					><rect width="18" height="18" x="3" y="3" rx="2" /></svg
				>
				Deseleccionar Todos
			</button>

		</div>
	</div>

	<div class="card bg-white p-6 rounded-xl shadow-lg border border-gray-200 w-full max-w-md">
		<h2 class="text-xl font-bold text-indigo-600 mb-2">{selectedItem.title}</h2>
		<p class="text-gray-800 text-lg mb-4">{selectedItem.text}</p>
		{#if selectedItem.comments}
			<div class="bg-gray-50 p-3 rounded border-l-4 border-indigo-400">
				<p class="text-sm text-gray-600 italic">{selectedItem.comments}</p>
			</div>
		{/if}
	</div>

</div>