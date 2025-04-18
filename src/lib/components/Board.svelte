<script>
    import { passive } from "svelte/legacy";
  
      let { size } = $props();
      let game_over = $state(false);
      let mode = $state("reveal");

      let longPressTimer;

        function onTouchStart(box, e) {
            longPressTimer = setTimeout(() => {
                flag(box, e);
            }, 600); // 600ms for long press
        }

        function onTouchEnd() {
            clearTimeout(longPressTimer);
        }



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
              return {id:i,
                    isbomb:bombs.includes(i) , 
                    isSurrounded:checksurround(i) , 
                    essage:"" , 
                    isRevealed : false,
                    isFlaged : false
                }
                  }));

    function flag(box, e) {
            e.preventDefault(); // prevent right-click context menu
            if (box.isRevealed) return;
            box.isFlagged = !box.isFlagged;
            box.message = box.isFlagged ? "🚩" : "";
    }
      function reveal(box , e){
        if (mode === "flag") flag(box ,e)
        else if(box.isFlaged|| box.isRevealed) return
        else if(box.isbomb) reveal_bombs(box)
        else {
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
      }

    async function reveal_bombs(){
        for (let i = 0; i < boxes.length; i++) {
        if (boxes[i].isbomb) {
            boxes[i].message = "💣";
            boxes[i].isRevealed = true;
            await new Promise(resolve => setTimeout(resolve, 1000)); // delay between each bomb reveal
        }
    }
        await new Promise(resolve => setTimeout(resolve, 100));
        game_over = true;
    }

    let remaining_count = $derived.by(()=>{
        let total = boxes.length;
        let revealed = boxes.filter( (box) => box.isRevealed || box.isbomb ).length;
        return total - revealed;
    });



  
  
  </script>
  
<div class="flex flex-col items-center w-full p-4">
    {#if remaining_count !== 0 && !game_over}
        <div class="mb-6 text-center">
            <button 
                class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg transition-colors mb-2"
                onclick={() => mode = (mode === "reveal") ? "flag" : "reveal"}>
                Toggle mode
            </button>
            <p class="text-lg font-medium">Current mode: <span class="capitalize font-bold">{mode}</span></p>
        </div>
        
        <div class="w-full max-w-md mx-auto mb-4">
            <div 
                class="grid gap-1 sm:gap-2" 
                style="grid-template-columns: repeat({size}, minmax(0, 1fr));">
                {#each boxes as box}
                    <button 
                        class="aspect-square bg-gray-300 hover:bg-gray-400 border-2 border-gray-500 text-lg sm:text-xl md:text-2xl font-bold flex items-center justify-center transition-colors {box.isRevealed ? 'bg-gray-100' : ''}"
                        onclick={(e) => reveal(box, e)}
                        oncontextmenu={(e) => flag(box, e)}>
                        {@html box.message}
                    </button>
                {/each}
            </div>
        </div>
        
        <p class="text-lg font-semibold">Remaining: {remaining_count}</p>
    {:else if remaining_count === 0 && !game_over}
        <div class="flex flex-col items-center justify-center h-60 w-full">
            <p class="text-4xl sm:text-6xl md:text-9xl text-green-600 font-bold animate-pulse">You won!</p>
        </div>
    {:else if game_over}
        <div class="flex flex-col items-center justify-center h-60 w-full">
            <p class="text-4xl sm:text-6xl md:text-9xl text-red-600 font-bold animate-bounce">Game over</p>
        </div>
    {/if}
</div>