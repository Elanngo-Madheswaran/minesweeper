<script>
    import { Board } from "$components";
    let  size = $state();
    let started = $state(false);
    let Time = $state({minu: 0 , sec:0});

    function startgame(){
        started = true;
        timer();
    }

    function timer(){
        setInterval(()=>{
            Time.sec += 1;
            if (Time.sec == 60){
                Time.sec=0;
                Time.minu +=1;
            }
        },1000 )
    }
    
</script>


{#if started == false}
    <select bind:value={size}>
        <option value="4">4</option>
        <option value="5">5</option>
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
        <option value="9">9</option>
        <option value="10">10</option>
    </select>
    <button onclick={startgame}>Start</button>

    {#key {size}}
    <Board {size} />
    {/key}
{:else if Time.minu < 2}
    {#if Time.minu < 1}
        <p>Game started </p>
        <p>Time : {Time.minu} : {Time.sec}</p>
    {:else}
        <p class="text-3xl text-orange-600">Game is going to end</p>
        <p>Time: {Time.minu} : <span class="text-red-600 animate-pulse">{Time.sec}</span></p>
    {/if}
    {#key {size}}
    <Board {size} />
    {/key}
{:else}
    <p class="text-red-800 text-9xl text-center">Game Over</p>
{/if}

