<script>
    import Display from "./display.svelte";
    import Keyboard from "./keyboard.svelte";
    import { displayValue } from "./stores.js";


    let eq = 0;

    function validInput(input){
        if (input.type == "operator"){
            if (this.allowOperator){
                this.allowOperator = false
            }
            else{
                return false
            }
        }

        if (input.type == "number") {
            this.allowOperator = true
        }
        return true
    }
    let validator = {allowOperator:false, validInput:validInput}


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

            const Originalvalidator = {allowOperator:false, validInput:validInput}
            validator = {...validator, ...Originalvalidator}

            $displayValue = ""

            updateUI = false
        }
        else { // meaning it's an operators
            Equation.calcArray.push(
                new ArrayItem(event.detail.type, event.detail.value)
            );
            Equation.renewCurrentWorkingItem()
        }


        if(Equation.calculate()){
            eq = Equation.equationValue;
        }
        else{
            eq = "Error"
        }
        if (updateUI){
            $displayValue += event.detail.value;
        }

    }

    

    class ArrayItem {
        constructor(type, stringValue, numberValue = NaN) {
            this.type = type;
            this.numberValue = numberValue;
            this.hasComma = this.type == "number" ? this.checkComma() : NaN;
            this.stringValue = stringValue;
            this.numbersAfterComma = 0
        }

        checkComma() {
            if (this.numberValue - Math.floor(this.numberValue) != 0) {
                return true;
            }
            return false;
        }
    }

    function calculate() {

        let completedCalculation = true

        if (this.calcArray[this.calcArray.length - 1].type == "operator") {
            completedCalculation = false
            return completedCalculation;
        }

        const parentheses = (theArray)=>{ // todo: make it work for non-nested parentheses. second todo: make it work for nested parentheses

            let beg = 0
            for (let index = 1; index < theArray.length; index++) {
                if (theArray[index].stringValue == "("){
                    beg = index
                }
                if (theArray[index].stringValue == ")"){
                    const afterMul = multiplicationAndDivision(theArray.slice(beg+1, index))
                    const afterAdd = additionAndSubtraction(afterMul)
                    theArray = [...theArray.slice(0, beg), theArray.slice(index+1, theArray.length)]
                    index -= (index-beg)
                    
                }
            }


        }

        function multiplicationAndDivision(theArray){

            for (let index = 0; index < theArray.length; index++) {
                if (theArray[index].stringValue == "*") {
                    let before = theArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = theArray.slice(index + 2);
                    let newNumberValue = theArray[index - 1].numberValue * theArray[index + 1].numberValue;
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
                    let newNumberValue = theArray[index - 1].numberValue / theArray[index + 1].numberValue;


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
            newEquationValue += theArray[0].numberValue;
            for (let index = 1; index < theArray.length; index++) {

                if (theArray[index].stringValue == "+") {
                    newEquationValue += theArray[index + 1].numberValue;
                } else if (theArray[index].stringValue == "-") {
                    newEquationValue -= theArray[index + 1].numberValue;
                }
            }
            //this.equationValue = newEquationValue;
            return newEquationValue

        };

        //const calcArrayCopy = [...this.calcArray]
        
        const afterMul = multiplicationAndDivision(this.calcArray);
        const afterAdd = additionAndSubtraction(afterMul);
        //this.calcArray = calcArrayCopy
        this.equationValue = afterAdd
        return completedCalculation
    }

    function renewCurrentWorkingItem(){
        const newWorkingItem = new ArrayItem("number", "", 0)
        this.calcArray.push(newWorkingItem)
        this.currentWorkingItem = newWorkingItem
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
    let Equation = {
        calcArray: [initialItem],
        calculate: calculate,
        equationValue: 0,
        currentWorkingItem:initialItem,
        updateCurrentWorkingArrayItem:updateCurrentWorkingArrayItem,
        renewCurrentWorkingItem:renewCurrentWorkingItem,
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
