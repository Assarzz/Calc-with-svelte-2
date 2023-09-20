<script>
    let keyboard = [];

    let col1 = [
        { value: "1", clicked: false, type: "number"},
        { value: "4", clicked: false, type: "number"},
        { value: "7", clicked: false, type: "number"},
    ]
    let col2 = [
        { value: "2", clicked: false, type: "number"},
        { value: "5", clicked: false, type: "number"},
        { value: "8", clicked: false, type: "number"},
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
    ]

    for (let index = 0; index < 10; index++) {
    }
    function getKey(getID){

    }
    keyboard.push({ value: "+", clicked: false, type: "operator", id:"+" });
    keyboard.push({ value: "-", clicked: false, type: "operator", id:"-" });
    keyboard.push({ value: "*", clicked: false, type: "operator", id:"*" });
    keyboard.push({ value: "/", clicked: false, type: "operator", id:"/" });
    for (let index = 0; index < keyboard.length; index++) {
        const alt = ["red", "green", "blue"]
        keyboard[index]["color"] = (alt[Math.floor(Math.random()*3)]) 
        
    }

    // function determine(index){
    //     if (keyboard[index].type != "operator"){

    //         if (index%3 == 0){
    //             return "col2"
    //         }
    //         else if (index%3 == 1){
    //             return "col1"
    //         }
    //         else{
    //             return "col3"
    //         }
    //     }
    //     else{
    //         return "op"
    //     }
    // }

    function click(col, index){
        console.log("clicked!")

        col[index].clicked = true


        setTimeout(() => {
            col[index].clicked = false
        }, 500);

    }
</script>

<!--
<div id="keyboard">
    <div id="col1">
        <div class="key {keyboard[1].color}">
            {keyboard[1].value}
        </div>
        <div class="key {keyboard[4].color}">
            {keyboard[4].value}
        </div>
        <div class="key {keyboard[7].color}">
            {keyboard[7].value}
        </div>
    </div>

    <div id="col2">
        <div class="key {keyboard[2].color}">
            {keyboard[2].value}
        </div>
        <div class="key {keyboard[5].color}">
            {keyboard[5].value}
        </div>
        <div class="key {keyboard[8].color}">
            {keyboard[8].value}
        </div>
        <div class="key {keyboard[0].color}">
            {keyboard[0].value}
        </div>
    </div>

    <div id="col3">
        <div class="key {keyboard[3].color}">
            {keyboard[3].value}
        </div>
        <div class="key {keyboard[6].color}">
            {keyboard[6].value}
        </div>
        <div class="key {keyboard[9].color}">
            {keyboard[9].value}
        </div>
    </div>

    <div id="col4">

        <div class="key {keyboard[10].color} op">
            {keyboard[10].value}
        </div>
        <div class="key {keyboard[11].color} op">
            {keyboard[11].value}
        </div>
    </div>
    <div id="col5">
        <div class="key {keyboard[12].color} op">
            {keyboard[12].value}
        </div>
        <div class="key {keyboard[13].color} op">
            {keyboard[13].value}
        </div>
    </div>
</div>
-->
<div id="keyboard">

    <!-- {#each keyboard as key, index }
        <button class="key {key.color} {() =>determine(index)} {(key.clicked? "clicked": "")}" on:click={()=> click(index)}>
            {key.value}
        </button>
    {/each} -->

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



    .red {
        background-color: rgba(200, 0, 0, 0.6);
    }
    .green {
        background-color: rgba(0, 200, 0, 0.6);
    }
    .blue {
        background-color: rgba(0, 0, 200, 0.6);
    }
</style>
