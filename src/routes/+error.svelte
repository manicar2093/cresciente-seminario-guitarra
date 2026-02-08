<script>
	import { resolve } from '$app/paths';
	const base = resolve('/');
</script>

<div class="flex flex-col items-center justify-center min-h-[60vh] text-center px-4">
	<h1 class="text-6xl font-bold text-primary mb-4">404</h1>
	<p class="text-2xl font-semibold text-gray-800 mb-2">¡Ups! Página no encontrada</p>
	<p class="text-gray-600 mb-8 max-w-md">
		Parece que la nota que buscas no está en el mástil. Vuelve al inicio para seguir practicando.
	</p>
	<a
		href="{base}"
		class="bg-primary hover:bg-primary-dark text-white font-bold py-3 px-6 rounded-lg transition-colors shadow-md inline-flex items-center gap-2"
	>
		<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-home"><path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>
		Volver al inicio
	</a>
</div>
