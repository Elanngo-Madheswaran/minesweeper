<script>
    import { passive } from "svelte/legacy";
  
      let { size } = $props();
      let s_boxes = $derived.by(()=>{
          return Array.from({length :size*size}, (_ ,i) =>{
              return {id:i , isbomb:false , isSurrounded:0}
          });
      });

      // svelte-ignore state_referenced_locally
            let boxes = $state(s_boxes);
  
  $inspect(boxes);
  
      let bombs = $state([]);
  
      function setBombs(boxes) {
          let no = Math.floor((size * size) / 3);
          let bom_num = new Set();
          while (bom_num.size < no) {
              bom_num.add(boxes[Math.floor(Math.random() * boxes.length)].id);
          }
          bombs = [...bom_num];
          boxes = boxes.forEach(box => {
              if(bombs.includes(box.id)){
                box.isbomb = true;
              }
          });
  
      }
  
  
  
  
  </script>
  
  <div class="grid gap-2 w-full" style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
      {#each boxes as box }
          <p class="{box.isbomb ? "text-red-600" : ""}">{box.id}</p>
      {/each}
  
      <button onclick={() => {setBombs(boxes)}}>Set bombs</button>
  </div>