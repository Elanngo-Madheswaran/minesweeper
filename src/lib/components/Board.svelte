<script>
    let { size } = $props();
    let boxes = $derived.by( () =>{
        return Array.from({length: size*size}, (_ , i) =>i)
    }
    );
</script>

<div class="grid gap-2 w-full" style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
    {#each boxes as box }
        <p>{box}</p>
    {/each}
</div>