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

    let remaining_count = $derived.by(()=>{
        let total = boxes.length;
        let revealed = boxes.filter( (box) => box.isRevealed || box.isbomb ).length;
        return total - revealed;
    });



  
  
  </script>
  
  <div class="flex w-full justify-center">
    {#if remaining_count !=0}
      <div class="grid gap-1 max-w-3/4s elf-center" style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
          {#each boxes as box }
              <button class="bg-gray-500 border-gray-900 w-15 h-15" onclick={() =>{reveal(box)}}>{box.message}</button>
          {/each}
      </div>
      <p>Boxes to reveal:{remaining_count}</p>
    {:else}
      <p class="text-9xl text-green-600 text-center">You won</p>
    {/if}
  </div>