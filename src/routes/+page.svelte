<script lang="ts">
	import data from '$lib/assets/data.json';
	import notes_arrays from '$lib/assets/notes_arrays.json';
	import { onMount } from 'svelte';

	let initialize = $state(false);
	let notasMapeoCheckbox = $state(true);
	let intervalosCheckbox = $state(true);
	let triadasCheckbox = $state(true);
	let escalasCheckbox = $state(true);
	let cagedCheckbox = $state(true);
	let acordesComplejosCheckbox = $state(true);

	let isModalOpen = $state(false);
	let pickedNotes = $state<string[]>([]);

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
		getRandomItem();
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

	function pickNotes() {
		const meta = selectedItem.meta;
		if (!meta || !meta.notes_to_pick) return;

		const type = meta.notes_to_pick as keyof typeof notes_arrays;
		const count = meta.notes_to_pick_count || 1;
		let availableNotes = [...notes_arrays[type]];
		
		// Mapa de enarmonías para evitar repeticiones musicales
		const enharmonics: Record<string, string> = {
			'Do♯': 'Re♭',
			'Re♭': 'Do♯',
			'Re♯': 'Mi♭',
			'Mi♭': 'Re♯',
			'Fa♯': 'Sol♭',
			'Sol♭': 'Fa♯',
			'Sol♯': 'La♭',
			'La♭': 'Sol♯',
			'La♯': 'Si♭',
			'Si♭': 'La♯'
		};

		const selected: string[] = [];
		for (let i = 0; i < count; i++) {
			if (availableNotes.length === 0) break;
			const randomIndex = Math.floor(Math.random() * availableNotes.length);
			const picked = availableNotes.splice(randomIndex, 1)[0];
			selected.push(picked);

			// Eliminar enarmónico si existe para que no se repita musicalmente
			const enharmonic = enharmonics[picked];
			if (enharmonic) {
				availableNotes = availableNotes.filter(n => n !== enharmonic);
			}

			// Eliminar notas con el mismo nombre base (evitar Solb y Sol# a la vez)
			const baseName = picked.substring(0, picked.length > 2 ? 3 : 2); 
			// Nota: En español los nombres son Do, Re, Mi, Fa, Sol, La, Si. 
			// Do, Re, Mi, Fa, La, Si tienen 2 letras. Sol tiene 3.
			// Los accidentes son ♯ (sharp) y ♭ (flat) que ocupan un caracter especial.
			
			// Una forma más segura es extraer la parte que no es el accidente
			const baseNote = picked.replace(/[♯♭]/g, '');
			availableNotes = availableNotes.filter(n => !n.startsWith(baseNote));
		}
		
		pickedNotes = selected;
		isModalOpen = true;
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
<div class="flex flex-col md:flex-row items-center md:items-start justify-center p-3 gap-8">
	<div class="random-form bg-white p-6 rounded-lg shadow-md w-full max-w-md">
		<div class="options flex flex-col justify-center space-y-2">
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (notasMapeoCheckbox = !notasMapeoCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (notasMapeoCheckbox = !notasMapeoCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="notas-mapeo" class="text-sm font-medium text-gray-700 cursor-pointer">Notas y mapeo</label>
				<button
					type="button"
					role="switch"
					aria-checked={notasMapeoCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						notasMapeoCheckbox = !notasMapeoCheckbox;
					}}
					class="{notasMapeoCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{notasMapeoCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (intervalosCheckbox = !intervalosCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (intervalosCheckbox = !intervalosCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="intervalos" class="text-sm font-medium text-gray-700 cursor-pointer">Intervalos</label>
				<button
					type="button"
					role="switch"
					aria-checked={intervalosCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						intervalosCheckbox = !intervalosCheckbox;
					}}
					class="{intervalosCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{intervalosCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (triadasCheckbox = !triadasCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (triadasCheckbox = !triadasCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="triadas" class="text-sm font-medium text-gray-700 cursor-pointer">Triadas</label>
				<button
					type="button"
					role="switch"
					aria-checked={triadasCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						triadasCheckbox = !triadasCheckbox;
					}}
					class="{triadasCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{triadasCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (escalasCheckbox = !escalasCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (escalasCheckbox = !escalasCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="escalas" class="text-sm font-medium text-gray-700 cursor-pointer">Escalas</label>
				<button
					type="button"
					role="switch"
					aria-checked={escalasCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						escalasCheckbox = !escalasCheckbox;
					}}
					class="{escalasCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{escalasCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (cagedCheckbox = !cagedCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (cagedCheckbox = !cagedCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="caged" class="text-sm font-medium text-gray-700 cursor-pointer">CAGED</label>
				<button
					type="button"
					role="switch"
					aria-checked={cagedCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						cagedCheckbox = !cagedCheckbox;
					}}
					class="{cagedCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{cagedCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
			<div 
				class="flex items-center justify-between cursor-pointer"
				onclick={() => (acordesComplejosCheckbox = !acordesComplejosCheckbox)}
				onkeydown={(e) => e.key === 'Enter' && (acordesComplejosCheckbox = !acordesComplejosCheckbox)}
				role="button"
				tabindex="0"
			>
				<label for="acordes-complejos" class="text-sm font-medium text-gray-700 cursor-pointer">Acordes complejos</label>
				<button
					type="button"
					role="switch"
					aria-checked={acordesComplejosCheckbox}
					onclick={(e) => {
						e.stopPropagation();
						acordesComplejosCheckbox = !acordesComplejosCheckbox;
					}}
					class="{acordesComplejosCheckbox ? 'bg-primary' : 'bg-gray-200'} relative inline-flex h-6 w-11 flex-shrink-0 cursor-pointer rounded-full border-2 border-transparent transition-colors duration-200 ease-in-out focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2"
				>
					<span
						aria-hidden="true"
						class="{acordesComplejosCheckbox ? 'translate-x-5' : 'translate-x-0'} pointer-events-none inline-block h-5 w-5 transform rounded-full bg-white shadow ring-0 transition duration-200 ease-in-out"
					></span>
				</button>
			</div>
		</div>
		<button
			onclick={getRandomItem}
			class="mt-6 w-full bg-primary hover:bg-primary-dark text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md cursor-pointer flex items-center justify-center gap-2"
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
				class="w-full bg-emerald-600 hover:bg-emerald-700 disabled:bg-gray-400 disabled:cursor-not-allowed text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md flex items-center justify-center gap-2 text-xs"
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
				Activar todos
			</button>
			<button
				onclick={deselectAll}
				disabled={!algunoSeleccionado}
				class="w-full bg-red-600 hover:bg-red-700 disabled:bg-gray-400 disabled:cursor-not-allowed text-white font-bold py-2 px-4 rounded-lg transition-colors shadow-md flex items-center justify-center gap-2 text-xs"
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
				Desactivar todos
			</button>

		</div>
	</div>

	<div class="card bg-white p-6 rounded-xl shadow-lg border border-gray-200 w-full max-w-md">
		{#if algunoSeleccionado}
			<div class="flex justify-between items-start mb-2">
				<h2 class="text-xl font-bold text-primary">{selectedItem.title}</h2>
				{#if selectedItem.meta?.notes_to_pick}
					<button
						onclick={pickNotes}
						class="bg-secondary hover:bg-opacity-90 text-white p-2 rounded-full transition-all shadow-sm flex items-center justify-center"
						title="Seleccionar notas"
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
							class="lucide lucide-music"
							><path d="M9 18V5l12-2v13" /><circle cx="6" cy="18" r="3" /><circle cx="18" cy="16" r="3" /></svg
						>
					</button>
				{/if}
			</div>
			<p class="text-gray-800 text-lg mb-4">{selectedItem.text}</p>
			{#if selectedItem.comments}
				<div class="bg-gray-50 p-3 rounded border-l-4 border-secondary">
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

	{#if isModalOpen}
		<!-- svelte-ignore a11y_click_events_have_key_events -->
		<!-- svelte-ignore a11y_no_static_element_interactions -->
		<div 
			class="fixed inset-0 backdrop-grayscale-100 bg-opacity-80 flex items-center justify-center p-4 z-50 transition-opacity"
			onclick={() => isModalOpen = false}
		>
			<div 
				class="bg-white rounded-2xl shadow-2xl p-8 max-w-sm w-full transform transition-all scale-100"
				role="dialog"
				aria-modal="true"
				onclick={(e) => e.stopPropagation()}
			>
				<h3 class="text-2xl font-bold text-gray-900 mb-6 text-center">Notas seleccionadas</h3>
				<div class="flex flex-wrap justify-center gap-4 mb-8">
					{#each pickedNotes as nota}
						<div class="bg-primary text-white text-3xl font-bold w-20 h-20 flex items-center justify-center rounded-xl shadow-lg border-2 border-primary-dark">
							{nota}
						</div>
					{/each}
				</div>
				<div class="flex flex-col gap-3">
					<button
						onclick={pickNotes}
						class="w-full bg-secondary hover:bg-opacity-90 text-white font-bold py-3 px-6 rounded-xl transition-colors shadow-md text-lg flex items-center justify-center gap-2"
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
							class="lucide lucide-rotate-cw"
							><path d="M21 12a9 9 0 1 1-9-9c2.52 0 4.93 1 6.74 2.74L21 8" /><path d="M21 3v5h-5" /></svg
						>
						Cambiar notas
					</button>
					<button
						onclick={() => isModalOpen = false}
						class="w-full bg-gray-100 hover:bg-gray-200 text-gray-800 font-bold py-3 px-6 rounded-xl transition-colors shadow-sm text-lg"
					>
						Cerrar
					</button>
				</div>
			</div>
		</div>
	{/if}

</div>
{/if}