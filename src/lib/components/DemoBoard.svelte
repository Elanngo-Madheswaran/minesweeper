<script>
    let { size , startgame } = $props(); // Default size if not provided

    // Generate demo board with hidden cells
    let boxes = $derived(Array.from({length: size * size}, (_, i) => ({
        id: i,message:""
    })));

    function showPrompt() {
        startgame();
    }
</script>
  
<div class="flex flex-col items-center w-full p-4">
    <div class="w-full max-w-md mx-auto mb-4">
        <div 
            class="grid gap-1 sm:gap-2" 
            style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
            {#each boxes as box}
                <button 
                    class="aspect-square bg-gray-300 hover:bg-gray-400 border-2 border-gray-500 text-lg sm:text-xl md:text-2xl font-bold flex items-center justify-center transition-colors"
                    onclick={showPrompt}>{box.message}
                </button>
            {/each}
        </div>
    </div>
</div>