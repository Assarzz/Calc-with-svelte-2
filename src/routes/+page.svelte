<script>
    import Display from "./display.svelte";
import Keyboard from "./keyboard.svelte";

let clicks = 0
//let myObject

function updateClicks(){
    clicks ++
}
// function merge(mergeObj){
//     myObject = [...myObject, ...mergeObj]
// }

// let accessorObj = {
//     updateClicks: updateClicks,
//     merge: merge
// }
</script>

<div>

    <Display/>
    <Keyboard {clicks} {updateClicks} />

</div>
<h1>{clicks}</h1>
<style>

    div{
        display: inline;
        margin: 100px;
    }
</style>