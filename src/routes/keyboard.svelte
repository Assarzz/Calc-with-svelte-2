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
        { value: "(", clicked: false, type: "para"},
        { value: ")", clicked: false, type: "para"},
    ]
    let col5 = [
        { value: "c", clicked: false, type: "speciall"},
        { value: "*", clicked: false, type: "operator"},
        { value: "/", clicked: false, type: "operator"},
        { value: ".", clicked: false, type: "operator"},

    ]


    const keyboard = [...col1, ...col2, ...col3, ...col4, ...col5];

    for (let index = 0; index < keyboard.length; index++) {
        const alt = ["red", "green", "blue"]
        keyboard[index]["color"] = (alt[Math.floor(Math.random()*3)]) 
        
    }

    
    function click(col, index) {

        switch (col) {
            case col1:
                col1[index].clicked = true
                break
            case col2:
                col2[index].clicked = true
                break
            case col3:
                col3[index].clicked = true
                break
            case col4:
                col4[index].clicked = true
                break
            case col5:
                col5[index].clicked = true
                break
            default:
                break;
        }

    setTimeout(() => {
        switch (col) {
            case col1:
                col1[index].clicked = false
                break
            case col2:
                col2[index].clicked = false
                break
            case col3:
                col3[index].clicked = false
                break
            case col4:
                col4[index].clicked = false
                break
            case col5:
                col5[index].clicked = false
                break
            default:
                break;
        }

    }, 500);

    dispatch("buttonclick", col[index]);
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
        display: flex;
        
        div{
            display: flex;
            flex-direction: column;
            justify-content: center;
            flex: 1;

            button{
                width: auto;
                height: auto;
                margin: 5px;
                padding: 10px;
                margin-left: 5px;
                margin-right: 5px;
                color: white;
                font-size: 8rem;
                display: flex;
                justify-content: center;
                font-family: "calcFont";
                transition: transform 0.1s;

            }

        }

    }
    .clicked{
        transform: scale(0.9);
    }
    .red {
        background-color: rgba(103, 23, 23, 0.6);
    }
    .green {
        background-color: rgba(42, 129, 42, 0.6);
    }
    .blue {
        background-color: rgba(24, 24, 81, 0.6);
    }
</style>
