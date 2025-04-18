<script>
    import { Board, DemoBoard } from "$components";
  import { passive } from "svelte/legacy";
    let size = $state(); // Default to 5x5 grid
    let started = $state(false);
    let gameover = $state(false);
    let iswon = $state(false);
    let timerInterval = $state(null);
    let timeLimit = $derived(size * 2);
    let timeRemaining = $state({ minutes: 0, seconds: 0 });
    function startgame() {
        started = true;
        gameover = false;
        iswon = false;
        // Initialize time remaining from time limit (convert to minutes and seconds)
        timeRemaining.minutes = Math.floor(timeLimit);
        timeRemaining.seconds = Math.round((timeLimit - timeRemaining.minutes) * 60);
        if (timeRemaining.seconds === 0 && timeRemaining.minutes > 0) {
            timeRemaining.seconds = 0;
        }
        timer();
    }

    function timer() {
        clearInterval(timerInterval);
        timerInterval = setInterval(() => {
            if (gameover || iswon){
                clearInterval(timerInterval);
                return;
            }
            else if (timeRemaining.seconds > 0) {
                timeRemaining.seconds -= 1;
            } else if (timeRemaining.minutes > 0) {
                timeRemaining.minutes -= 1;
                timeRemaining.seconds = 59;
            } else {
                // Time is up, game over
                clearInterval(timerInterval);
                gameover = true;
            }
        }, 1000);
    }
</script>

<div class="w-full max-w-6xl mx-auto p-4 min-h-screen">
    <div class="lg:flex lg:flex-row lg:items-start lg:gap-8">
        <div class="lg:w-1/3">
            <h1 class="text-3xl md:text-4xl font-bold mb-6 text-center lg:text-left">Minesweeper</h1>
            
            {#if !started}
                <div class="w-full max-w-md mx-auto lg:mx-0 mb-6 flex flex-col items-center lg:items-start">
                    <label for="grid-size" class="text-lg font-medium mb-2">Select grid size:</label>
                    <select 
                        id="grid-size"
                        bind:value={size} 
                        class="mb-4 py-2 px-4 bg-gray-100 border-2 border-gray-300 rounded-lg text-lg">
                        <option value="4">4 x 4</option>
                        <option value="5">5 x 5</option>
                        <option value="6">6 x 6</option>
                        <option value="7">7 x 7</option>
                        <option value="8">8 x 8</option>
                        <option value="9">9 x 9</option>
                        <option value="10">10 x 10</option>
                    </select>
                    
                    <button 
                        onclick={startgame}
                        class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-8 rounded-lg transition-colors text-xl mb-6">
                        Start Game
                    </button>
                </div>
            {:else if started}
                <div class="w-full max-w-md mx-auto lg:mx-0 mb-6 bg-white p-4 rounded-lg shadow-md">
                    {#if timeRemaining.minutes > 1}
                        <p class="text-xl font-medium text-center lg:text-left">Game in progress</p>
                        <p class="text-2xl font-bold text-center lg:text-left">Time Remaining: {timeRemaining.minutes}:{timeRemaining.seconds < 10 ? '0' : ''}{timeRemaining.seconds}</p>
                    {:else if !gameover || !iswon}
                        <p class="text-2xl text-orange-600 font-bold text-center lg:text-left">Game is ending soon!</p>
                        <p class="text-2xl font-bold text-center lg:text-left">Time: {timeRemaining.minutes}:<span class="text-red-600 animate-pulse">{timeRemaining.seconds < 10 ? '0' : ''}{timeRemaining.seconds}</span></p>
                    {:else}
                        <p class="text-2xl font-bold text-center lg:text-left">Time Remaining: {timeRemaining.minutes}:{timeRemaining.seconds < 10 ? '0' : ''}{timeRemaining.seconds}</p>
                    {/if}
                </div>
            {/if}
        </div>
        
        <div class="lg:w-2/3">
            {#if !started}
                <div class="w-full">
                    <DemoBoard {size} {startgame} />
                </div>
            {:else if !gameover && !iswon}
                <div class="w-full max-w-md mx-auto">
                    <Board {size} bind:gameover={gameover} bind:iswon={iswon}/>
                </div>
            {:else if gameover}
                <div class="w-full flex flex-col items-center justify-center min-h-[60vh]">
                    <p class="text-4xl sm:text-6xl md:text-9xl text-red-800 font-bold text-center animate-pulse">Game Over</p>
                    <button 
                        onclick={() => {
                            clearInterval(timerInterval);
                            started = false;
                        }}
                        class="mt-8 bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-6 rounded-lg transition-colors">
                        Play Again
                    </button>
                </div>
            {:else if iswon}
            <div class="w-full flex flex-col items-center justify-center min-h-[60vh]">
                <p class="text-4xl sm:text-6xl md:text-9xl text-green-800 font-bold text-center animate-pulse">You won</p>
                <button 
                    onclick={() => {
                        clearInterval(timerInterval);
                        started = false;
                    }}
                    class="mt-8 bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-6 rounded-lg transition-colors">
                    Play Again
                </button>
            </div>
            {/if}
        </div>
    </div>
</div>
