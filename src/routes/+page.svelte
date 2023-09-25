<script>
    import Display from "./display.svelte";
    import Keyboard from "./keyboard.svelte";
    import { displayValue } from "./stores.js";


    let eq = NaN;


    function handleClickEvent(event) {

        $displayValue += event.detail.value;

        if (event.detail.type == "number") {            
            Equation.updateCurrentWorkingArrayItem(event.detail.value)
        }
        else if (event.detail.value == "."){
            Equation.currentWorkingItem.hasComma = true
        }
        else {
            Equation.calcArray.push(
                new ArrayItem(event.detail.type, event.detail.value)
            );
            Equation.renewCurrentWorkingItem()
        }

        // if Equation is valid we can perform Equation.calculate()

        if(Equation.calculate()){
            eq = Equation.equationValue;
        }
        else{
            eq = "Error"
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

        const multiplicationAndDivision = () => {

            for (let index = 0; index < this.calcArray.length; index++) {
                if (this.calcArray[index].stringValue == "*") {
                    let before = this.calcArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = this.calcArray.slice(index + 2);
                    let newNumberValue = this.calcArray[index - 1].numberValue * this.calcArray[index + 1].numberValue;
                    let newValue = new ArrayItem(
                        "number",
                        newNumberValue.toString(),
                        newNumberValue
                    );
                    before.push(newValue);
                    this.calcArray = before.concat(after);
                    index -= 2; // since 2 numbers and 1 operation has produced 1 number(3 turned into 1), index needs to be decreased by 2.
                } else if (this.calcArray[index].stringValue == "/") {
                    let before = this.calcArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = this.calcArray.slice(index + 2);
                    let newNumberValue = this.calcArray[index - 1].numberValue / this.calcArray[index + 1].numberValue;


                    let newValue = new ArrayItem(
                        "number",
                        newNumberValue.toString(),
                        newNumberValue
                    );
                    before.push(newValue);
                    this.calcArray = before.concat(after);
                    index -= 2; // since 2 numbers and 1 operation has produced 1 number(3 turned into 1), index needs to be decreased by 2.
                }
            }

        };
        const additionAndSubtraction = () => {
            let newEquationValue = 0;
            newEquationValue += this.calcArray[0].numberValue;
            for (let index = 1; index < this.calcArray.length; index++) {

                if (this.calcArray[index].stringValue == "+") {
                    newEquationValue += this.calcArray[index + 1].numberValue;
                } else if (this.calcArray[index].stringValue == "-") {
                    newEquationValue -= this.calcArray[index + 1].numberValue;
                }
            }
            this.equationValue = newEquationValue;

        };

        const calcArrayCopy = [...this.calcArray]
        if (this.calcArray.length  >1){
            multiplicationAndDivision();
        }
        additionAndSubtraction();
        this.calcArray = calcArrayCopy

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

            // assumes no comma
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
    const Equation = {
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
    <Keyboard
        on:buttonclick={(event) => handleClickEvent(event)}
    />
</div>

<style>
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
</style>
