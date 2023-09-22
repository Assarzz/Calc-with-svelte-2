<script>
    import Display from "./display.svelte";
    import Keyboard from "./keyboard.svelte";
    import { displayValue } from "./stores.js";

    let clicks = 0;

    let eq = NaN;

    function updateClicks() {
        clicks++;
    }

    function handleClickEvent(event) {

        $displayValue += event.detail.value;

        if (event.detail.type == "number") {

            if (this.calcArray[this.calcArray.length-1].type == "number"){
                // last element was number
                this.calcArray[this.calcArray.length-1].updateValue(event.detail.value)
            }

            Equation.calcArray.push(
                new ArrayItem(
                    event.detail.type,
                    event.detail.value,
                    parseFloat(event.detail.value)

                )
            );
        } else {
            Equation.calcArray.push(
                new ArrayItem(event.detail.type, event.detail.value)
            );
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
        constructor(type, stringValue, intNumberValue = NaN) {
            this.type = type;
            this.intNumberValue = intNumberValue;
            this.hasComma = this.type == "number" ? this.checkComma() : NaN;
            this.stringValue = stringValue;
        }
        updateValue(newStringValue){
            // update arrayItem value
        }

        checkComma() {
            if (this.intNumberValue - Math.floor(this.intNumberValue) != 0) {
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
                    let newNumberValue = this.calcArray[index - 1].intNumberValue * this.calcArray[index + 1].intNumberValue;
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
                    let newNumberValue = this.calcArray[index - 1].intNumberValue / this.calcArray[index + 1].intNumberValue;


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
            newEquationValue += this.calcArray[0].intNumberValue;
            for (let index = 1; index < this.calcArray.length; index++) {
                console.log("here")

                if (this.calcArray[index].stringValue == "+") {
                    newEquationValue += this.calcArray[index + 1].intNumberValue;
                } else if (this.calcArray[index].stringValue == "-") {
                    newEquationValue -= this.calcArray[index + 1].intNumberValue;
                }
            }
            console.log(this.equationValue)
            this.equationValue = newEquationValue;
            console.log(this.equationValue)

        };

        if (this.calcArray.length  >1){
            multiplicationAndDivision();
        }
        additionAndSubtraction();


        return completedCalculation
    }


    const Equation = {
        calcArray: [],
        calculate: calculate,
        equationValue: 0,
    };

    //------------------------------------------------------------------------------------------------------
</script>

<div>
    <Display />
    <Keyboard
        {clicks}
        {updateClicks}
        on:buttonclick={(event) => handleClickEvent(event)}
    />
</div>

<h1>{clicks}</h1>

<h1>{eq}</h1>

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
