<script lang="ts">
	import '../app.css';

	let { children } = $props();
</script>

<div class="sticky top-0 z-50 bg-primary text-white py-2 px-4 text-center text-sm shadow-md flex items-center justify-center gap-2">
	<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-info"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/></svg>
	<span>
		¿Quieres saber más? 
		<a href="https://cresciente.net/" target="_blank" rel="noopener noreferrer" class="underline font-bold hover:text-orange-100 transition-colors">
			Conoce Cresciente
		</a>
	</span>
</div>

<div class="m-5">
	{@render children?.()}
</div>
