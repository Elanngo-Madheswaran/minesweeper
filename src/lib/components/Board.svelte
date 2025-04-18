<script>
    import { passive } from "svelte/legacy";
  
      let { size } = $props();

      let bombs = $derived.by( ()=>{
            let no = Math.floor((size * size) / 4);
            let arr = Array.from({length :size*size}, (_ ,i) => i)
            let bom_num = new Set();
            while (bom_num.size < no) {
                bom_num.add(arr[Math.floor(Math.random() * arr.length)]);
            }
            return [...bom_num];
      }

      );

      function checksurround(n){
        let count = 0;
        if (bombs.includes(n)){
            return 0;
        }else{
            // Calculate row and column of the current cell
            const row = Math.floor(n / size);
            const col = n % size;
            
            // Check all 8 adjacent cells (including diagonals)
            for (let r = Math.max(0, row-1); r <= Math.min(row+1, size-1); r++) {
                for (let c = Math.max(0, col-1); c <= Math.min(col+1, size-1); c++) {
                    const adjacentCell = r * size + c;
                    // Skip the current cell itself
                    if (adjacentCell !== n && bombs.includes(adjacentCell)) {
                        count++;
                    }
                }
            }
            return count;
        }
      }
    
      let boxes = $state( Array.from({length :size*size}, (_ ,i) =>{
              return {id:i , isbomb:bombs.includes(i) , isSurrounded:checksurround(i) , message:"" , isRevealed : false}
                  }));

      function reveal(box){
        if(box.isRevealed || box.isbomb) return
        box.message = box.isSurrounded;
        box.isRevealed = true;
        if (box.isSurrounded == 0){
            const n = box.id;
            const row = Math.floor(n / size);
            const col = n % size;
            for (let r = Math.max(0, row-1); r <= Math.min(row+1, size-1); r++) {
                for (let c = Math.max(0, col-1); c <= Math.min(col+1, size-1); c++) {
                    const adjacentCell = r * size + c;
                    reveal(boxes[adjacentCell]);
                }
            }
        }
      }

  
  
  </script>
  
  <div class="flex w-full justify-center">
      <div class="grid gap-1 max-w-3/4 self-center" style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
          {#each boxes as box }
              <button class="bg-gray-500 border-gray-900 w-10 h-10" onclick={() =>{reveal(box)}}>{box.message}</button>
          {/each}
      </div>
  </div>