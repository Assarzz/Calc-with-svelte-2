<script>
    import { displayValue } from './stores.js';
    export let eq

</script>

<div id="display">
    <h1>{$displayValue}</h1>
    <h1>{eq}</h1>
</div>


<style lang="scss">

    @font-face {
    font-family: "calcFont";
    src: url("../fonts/dotty/dotty.ttf") format('truetype');
    }
    
    #display{
        display: block;
        width: 100%;
        height: 200px;
        background-color: rgb(68, 56, 56);
        border: 1px solid rgb(76, 87, 76);
        display: flex;
        justify-content: center;
        flex-direction: column;
        h1{
            margin: 0%;
            margin-right: 20px;
            padding: 5px;
            display: flex;
            justify-content: right;
            color: white;
            font-size: 6rem;
            font-family: "calcFont";
        }
    }
</style>