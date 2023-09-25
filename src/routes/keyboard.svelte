<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher()

    let col1 = [
        { value: "1", clicked: false, type: "number"},
        { value: "4", clicked: false, type: "number"},
        { value: "7", clicked: false, type: "number"},
    ]
    let col2 = [
        { value: "2", clicked: false, type: "number"},
        { value: "5", clicked: false, type: "number"},
        { value: "8", clicked: false, type: "number"},
        { value: "0", clicked: false, type: "number"},

    ]
    let col3 = [
        { value: "3", clicked: false, type: "number"},
        { value: "6", clicked: false, type: "number"},
        { value: "9", clicked: false, type: "number"},
    ]
    let col4 = [
        { value: "+", clicked: false, type: "operator"},
        { value: "-", clicked: false, type: "operator"},
    ]
    let col5 = [
        { value: "*", clicked: false, type: "operator"},
        { value: "/", clicked: false, type: "operator"},
        { value: ".", clicked: false, type: "operator"},

    ]

    const keyboard = [...col1, ...col2, ...col3, ...col4, ...col5];

    for (let index = 0; index < keyboard.length; index++) {
        const alt = ["red", "green", "blue"]
        keyboard[index]["color"] = (alt[Math.floor(Math.random()*3)]) 
        
    }


    function click(col, index){

        dispatch("buttonclick", col[index])

        col[index].clicked = true


        setTimeout(() => {
            col[index].clicked = false
        }, 500);

    }
</script>


<div id="keyboard">

    <div class="col1">
        {#each col1 as key, index}
            <button class="key {key.color}  {(key.clicked? "clicked": "")}" on:click={()=> click(col1, index)}>
                {key.value}
            </button>
        {/each}
    </div>
    <div class="col2">
        {#each col2 as key, index}
            <button class="key {key.color}  {(key.clicked? "clicked": "")}" on:click={()=> click(col2, index)}>
                {key.value}
            </button>
        {/each}
    </div>
    <div class="col3">
        {#each col3 as key, index}
            <button class="key {key.color}  {(key.clicked? "clicked": "")}" on:click={()=> click(col3, index)}>
                {key.value}
            </button>
        {/each}
    </div>
    <div class="col4">
        {#each col4 as key, index}
            <button class="key {key.color}  {(key.clicked? "clicked": "")}" on:click={()=> click(col4, index)}>
                {key.value}
            </button>
        {/each}
    </div>
    <div class="col5">
        {#each col5 as key, index}
            <button class="key {key.color}  {(key.clicked? "clicked": "")}" on:click={()=> click(col5, index)}>
                {key.value}
            </button>
        {/each}
    </div>
</div>

<style lang="scss">
    $def-border-radious: 10px;

    #keyboard{
        display: inline-flex;
        background-color: rgba(0, 0, 0, 0.1);
        div{
            display: flex;
            flex-direction: column;
            justify-content: center;

            button{
                border-radius: 100px;
                width: 100px;
                height: 100px;
                margin: 5px;
                color: white;
                font-size: 5rem;
                display: flex;
                justify-content: center;

                .clicked{
                    transform: scale(0.9);
                }

            }

        }

    }



    .red {
        background-color: rgba(200, 100, 1000, 0.6);
    }
    .green {
        background-color: rgba(100, 200, 100, 0.6);
    }
    .blue {
        background-color: rgba(100, 100, 200, 0.6);
    }
</style>
