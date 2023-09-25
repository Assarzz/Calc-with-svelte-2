<script>
    import { displayValue } from './stores.js';
    export let eq

</script>

<div id="display">
    <h1>{$displayValue}</h1>
    <h1>{eq}</h1>
</div>


<style lang="scss">
    #display{
        display: block;
        width: 1200px;
        height: 200px;
        background-color: rgba(200, 100, 100, 0.3);
        border-radius: 50px;
        border: 1px solid rgb(76, 87, 76);
        display: flex;
        justify-content: right;
        align-items: center;
        
        flex-direction: column;
        h1{
            color: white;

        }
    }
</style>