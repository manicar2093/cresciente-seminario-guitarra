<script lang="ts">
	import data from '$lib/assets/data.json';
	import { onMount } from 'svelte';

	let initialize = $state(false);
	let notasMapeoCheckbox = $state(true);
	let intervalosCheckbox = $state(true);
	let triadasCheckbox = $state(true);
	let escalasCheckbox = $state(true);
	let cagedCheckbox = $state(true);
	let acordesComplejosCheckbox = $state(true);

	$effect(() => {
		if(!initialize) return
		const state = {
			notasMapeoCheckbox,
			intervalosCheckbox,
			triadasCheckbox,
			escalasCheckbox,
			cagedCheckbox,
			acordesComplejosCheckbox
		};
		localStorage.setItem('guitar-seminar-state', JSON.stringify(state));
	});
	
	let todosSeleccionados = $derived(
		notasMapeoCheckbox &&
		intervalosCheckbox &&
		triadasCheckbox &&
		escalasCheckbox &&
		cagedCheckbox &&
		acordesComplejosCheckbox
	);

	let algunoSeleccionado = $derived(
		notasMapeoCheckbox ||
		intervalosCheckbox ||
		triadasCheckbox ||
		escalasCheckbox ||
		cagedCheckbox ||
		acordesComplejosCheckbox
	);

	let selectedItem = $state(data[0]);

	function selectAll() {
		notasMapeoCheckbox = true;
		intervalosCheckbox = true;
		triadasCheckbox = true;
		escalasCheckbox = true;
		cagedCheckbox = true;
		acordesComplejosCheckbox = true;
	}

	function deselectAll() {
		notasMapeoCheckbox = false;
		intervalosCheckbox = false;
		triadasCheckbox = false;
		escalasCheckbox = false;
		cagedCheckbox = false;
		acordesComplejosCheckbox = false;
	}

	function getRandomItem() {

		const selectedTitles: string[] = [];
		if (notasMapeoCheckbox) selectedTitles.push('NOTAS Y MAPEO');
		if (intervalosCheckbox) selectedTitles.push('INTERVALOS');
		if (triadasCheckbox) selectedTitles.push('TRÍADAS');
		if (escalasCheckbox) selectedTitles.push('ESCALAS');
		if (cagedCheckbox) selectedTitles.push('CAGED');
		if (acordesComplejosCheckbox) selectedTitles.push('ACORDES COMPLEJOS');

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
		const fromLocalStorage = localStorage.getItem('guitar-seminar-state');
		if (fromLocalStorage) {
			const initialState = JSON.parse(fromLocalStorage);
			notasMapeoCheckbox = initialState.notasMapeoCheckbox
			intervalosCheckbox = initialState.intervalosCheckbox
			triadasCheckbox = initialState.triadasCheckbox
			escalasCheckbox = initialState.escalasCheckbox
			cagedCheckbox = initialState.cagedCheckbox
			acordesComplejosCheckbox = initialState.acordesComplejosCheckbox
		}
		initialize = true;

		getRandomItem();
	});
</script>

{#if initialize}
<div class="flex flex-col md:flex-row items-center md:items-start justify-center p-8 gap-8">
	<div class="random-form bg-white p-6 rounded-lg shadow-md w-full max-w-md">
		<div class="options flex flex-col justify-center space-y-2">
			<div class="flex items-center space-x-2">
				<input
					bind:checked={notasMapeoCheckbox}
					type="checkbox"
					id="notas-mapeo"
					name="notas-mapeo"
				/>
				<label for="notas-mapeo">Notas y mapeo</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={intervalosCheckbox}
					type="checkbox"
					id="intervalos"
					name="intervalos"
				/>
				<label for="intervalos">Intervalos</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={triadasCheckbox}
					type="checkbox"
					id="triadas"
					name="triadas"
				/>
				<label for="triadas">Triadas</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={escalasCheckbox}
					type="checkbox"
					id="escalas"
					name="escalas"
				/>
				<label for="escalas">Escalas</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={cagedCheckbox}
					type="checkbox"
					id="caged"
					name="caged"
				/>
				<label for="caged">CAGED</label>
			</div>
			<div class="flex items-center space-x-2">
				<input
					bind:checked={acordesComplejosCheckbox}
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
				disabled={todosSeleccionados}
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
				disabled={!todosSeleccionados}
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
		{#if algunoSeleccionado}
			<h2 class="text-xl font-bold text-indigo-600 mb-2">{selectedItem.title}</h2>
			<p class="text-gray-800 text-lg mb-4">{selectedItem.text}</p>
			{#if selectedItem.comments}
				<div class="bg-gray-50 p-3 rounded border-l-4 border-indigo-400">
					<p class="text-sm text-gray-600 italic">{selectedItem.comments}</p>
				</div>
			{/if}
		{:else}
			<div class="flex flex-col items-center justify-center py-8 text-center">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="48"
					height="48"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
					class="lucide lucide-info text-indigo-400 mb-4"
					><circle cx="12" cy="12" r="10" /><path d="M12 16v-4" /><path d="M12 8h.01" /></svg
				>
				<p class="text-gray-500 text-lg font-medium">
					Por favor, selecciona alguna opción para comenzar.
				</p>
			</div>
		{/if}
	</div>

</div>
{/if}