<script>
    import Display from "./display.svelte";
    import Keyboard from "./keyboard.svelte";
    import { displayValue } from "./stores.js";


    let eq = 0;

    function validInput(input){
        if (input.type == "operator"){
            if (!this.lastInputWasOperator){
                this.lastInputWasOperator = true
            }
            else{
                return false
            }

            
        }
        if (input.value == "(" ){

            if( !this.lastInputWasOperator){
                return false
            }
        }

        if (input.type == "number") {
            this.lastInputWasOperator = false
        }
        return true
    }
    let validator = {lastInputWasOperator:false, validInput:validInput}


    function handleClickEvent(event) {

        let updateUI = true

        if (!validator.validInput(event.detail)){ // all my below code only handels valid input. It is this object's job to make sure we only pass on valid input
            return
        }

        if (event.detail.type == "number") {            
            Equation.updateCurrentWorkingArrayItem(event.detail.value)
        }
        else if (event.detail.value == "."){
            Equation.currentWorkingItem.hasComma = true
        }
        else if (event.detail.value == "c"){

            let initialItem = new ArrayItem("number", "", 0)
            const OriginalEquation = {
                calcArray: [initialItem],
                calculate: calculate,
                equationValue: 0,
                currentWorkingItem:initialItem,
                updateCurrentWorkingArrayItem:updateCurrentWorkingArrayItem,
                renewCurrentWorkingItem:renewCurrentWorkingItem,
            };

            Equation = {...Equation, ...OriginalEquation}

            const Originalvalidator = {lastInputWasOperator:false, validInput:validInput}
            validator = {...validator, ...Originalvalidator}

            $displayValue = ""

            updateUI = false
        }
        else if (event.detail.type == "para"){

            if (event.detail.value == "("){
                const newArrayArray = new ArrayArray(Equation.currentWorkingArray)
                Equation.currentWorkingArray = newArrayArray

                Equation.turnCurrentWorkingItemToArrayArray(newArrayArray)
                Equation.renewCurrentWorkingItem()

            }
            else if (event.detail.value == ")"){
                
                Equation.currentWorkingArray = Equation.currentWorkingArray.parent

            }
        }
        else { // meaning it's an operator
            Equation.currentWorkingArray.array.push(
                new ArrayItem(event.detail.type, event.detail.value)
            );
            Equation.renewCurrentWorkingItem()
        }

        Equation.calculate()
        eq = Equation.equationValue;
        
        if (updateUI){
            $displayValue += event.detail.value;
        }

        console.log(Equation.calcArray)
    }

    

    class ArrayItem {
        constructor(type, stringValue, numberValue = NaN, parent) {
            this.type = type;
            this.numberValue = numberValue;
            this.hasComma = this.type == "number" ? this.checkComma() : NaN;
            this.stringValue = stringValue;
            this.numbersAfterComma = 0
            this.parent = parent
        }

        checkComma() {
            if (this.numberValue - Math.floor(this.numberValue) != 0) {
                return true;
            }
            return false;
        }
    }
    class ArrayArray{
        constructor(parent, array=[]){
            this.array = array
            this.parent = parent
            this.pObject = true
            
        }
    }

    function recursiveCalculate(theArray){

        // this is completely a recursive function that is called in itself for all depths of parenthesis that there are.
        function multiplicationAndDivision(theArray){
            console.log(theArray)
            for (let index = 0; index < theArray.length; index++) {
                if (theArray[index].stringValue == "*") {
                    let before = theArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = theArray.slice(index + 2);
                    // kolla om Arrayitems är Parantes object. OM det är det använder vi recursion :)))

                    const beforeItemValue = (theArray[index - 1].pObject? recursiveCalculate(theArray[index - 1].array): theArray[index - 1])
                    const afterItemValue = (theArray[index + 1].pObject? recursiveCalculate(theArray[index + 1].array): theArray[index + 1])
                    let newNumberValue = beforeItemValue.numberValue * afterItemValue.numberValue;
                    let newValue = new ArrayItem(
                        "number",
                        newNumberValue.toString(),
                        newNumberValue
                    );
                    before.push(newValue);
                    theArray = before.concat(after);
                    index -= 2; // since 2 numbers and 1 operation has produced 1 number(3 turned into 1), index needs to be decreased by 2.
                } else if (theArray[index].stringValue == "/") {
                    let before = theArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = theArray.slice(index + 2);
                    const beforeItemValue = (theArray[index - 1].pObject? recursiveCalculate(theArray[index - 1].array): theArray[index - 1])
                    const afterItemValue = (theArray[index + 1].pObject? recursiveCalculate(theArray[index + 1].array): theArray[index + 1])

                    let newNumberValue = beforeItemValue.numberValue / afterItemValue.numberValue;

                    let newValue = new ArrayItem(
                        "number",
                        newNumberValue.toString(),
                        newNumberValue
                    );
                    before.push(newValue);
                    theArray = before.concat(after);
                    index -= 2; // since 2 numbers and 1 operation has produced 1 number(3 turned into 1), index needs to be decreased by 2.
                }
            }

            return theArray

        };
        function additionAndSubtraction(theArray){
            let newEquationValue = 0;
            
            const initialAdd = (theArray[0].pObject? recursiveCalculate(theArray[0]): theArray[0])
            newEquationValue += initialAdd.numberValue;
            for (let index = 1; index < theArray.length; index++) {

                if (theArray[index].stringValue == "+") {
                    const afterItemValue = (theArray[index + 1].pObject? recursiveCalculate(theArray[index + 1].array): theArray[index + 1])
                    newEquationValue += afterItemValue.numberValue;
                } else if (theArray[index].stringValue == "-") {
                    const afterItemValue = (theArray[index + 1].pObject? recursiveCalculate(theArray[index + 1].array): theArray[index + 1])
                    newEquationValue -= afterItemValue.numberValue;
                }
            }
            //this.equationValue = newEquationValue;
            return newEquationValue

        };

        const afterMul = multiplicationAndDivision(theArray);
        const afterAdd = additionAndSubtraction(afterMul);

        return new ArrayItem("number", toString(afterAdd), afterAdd)

    }
    function calculate() {

        const finalArrayItem = recursiveCalculate([...this.calcArray.array])
        this.equationValue = finalArrayItem.numberValue
    }

    function renewCurrentWorkingItem(){
        const newWorkingItem = new ArrayItem("number", "", 0)
        this.currentWorkingArray.array.push(newWorkingItem)
        this.currentWorkingItem = newWorkingItem
    }
    function turnCurrentWorkingItemToArrayArray(objectToAddPropsFrom) {

        for (const key in this.currentWorkingItem) {
            if (this.currentWorkingItem.hasOwnProperty(key)) {
            delete this.currentWorkingItem[key];
            }
        }

        for (const key in objectToAddPropsFrom) {
            if (objectToAddPropsFrom.hasOwnProperty(key)) {
            this.currentWorkingItem[key] = objectToAddPropsFrom[key];
            }
        }
        
}

    function updateCurrentWorkingArrayItem(newCharToAdd){
            //console.log(JSON.parse(JSON.stringify(this.currentWorkingItem)));
            this.currentWorkingItem.stringValue += newCharToAdd

            // has comma
            if (this.currentWorkingItem.hasComma){
                this.currentWorkingItem.numbersAfterComma ++
                this.currentWorkingItem.numberValue += Math.pow(10, -this.currentWorkingItem.numbersAfterComma)*parseFloat(newCharToAdd)
            }
            else{
                // no comma
                this.currentWorkingItem.numberValue*= 10
                this.currentWorkingItem.numberValue += parseFloat(newCharToAdd)
            }




    }
    let initialItem = new ArrayItem("number", "", 0)
    let initialArray = new ArrayArray(NaN, [initialItem])
    let Equation = {
        calcArray: initialArray,
        calculate: calculate,
        equationValue: 0,
        currentWorkingItem:initialItem,
        currentWorkingArray: initialArray,
        updateCurrentWorkingArrayItem:updateCurrentWorkingArrayItem,
        renewCurrentWorkingItem:renewCurrentWorkingItem,
        turnCurrentWorkingItemToArrayArray:turnCurrentWorkingItemToArrayArray,
    };

    //------------------------------------------------------------------------------------------------------
</script>

<div>
    <Display {eq} />

    <br>
    <Keyboard
        on:buttonclick={(event) => handleClickEvent(event)}
    />

    <br>
    <footer>

    </footer>
</div>

<style lang="scss">

    :global(body){
        margin: 0%;
        background-color: rgb(79, 67, 67);
    }

    footer{
        background-color: rgb(52, 47, 47);
        display: block;
        width: 100%;
        height: auto;
    }
    

/**
    div {
        display: inline;
        margin: 100px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background-color: rgba(0, 0, 0, 0.85);
        border: 5px solid rgba(100, 255, 100, 0.5);
    }

/*/
</style>
