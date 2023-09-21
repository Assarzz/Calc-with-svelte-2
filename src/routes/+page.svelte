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
        console.log(event.detail);

        $displayValue += event.detail.value;

        if (event.detail.type == "number") {
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

        Equation.calculate();

        eq = Equation.equationValue;
    }

    // function checkRules(calcArray, valueToVerify){
    //     // I should go thorugh math rules of which symbol is allowed in the current context
    //     let followsRules = true

    //     if(valueToVerify.type == "operator" && lastClickedWasAnOperation){
    //         return false
    //     }

    //     return followsRules
    // }
    //----------------------------------------------------------------------------------------------
    //Gammal miniräknare kod.

    //
    class ArrayItem {
        constructor(type, stringValue, intNumberValue = NaN) {
            this.type = type;
            this.intNumberValue = intNumberValue;
            this.hasComma = this.type == "number" ? this.checkComma() : NaN;
            this.stringValue = stringValue;
        }
        checkComma() {
            if (this.intNumberValue - Math.floor(this.intNumberValue) != 0) {
                return true;
            }
            return false;
        }
    }

    function calculate() {
        let completeCalcualtion = true;
        console.log("this");
        console.log(this);

        if (this.calcArray.length == 0) {
            return false;
        }
        if (this.calcArray[this.calcArray.length - 1].type == "operator") {
            return false;
        }
        const multiplicationAndDivision = () => {
            for (let index = 0; index < this.calcArray.length; index++) {
                if (this.calcArray[index].stringValue == "*") {
                    let before = this.calcArray.slice(0, index - 1); // the arry before and after the two numbers should be joined with the new product in the middle.
                    let after = this.calcArray.slice(index + 2);
                    newNumberValue =
                        this.calcArray[index - 1].intNumberValue *
                        this.calcArray[index + 1].intNumberValue;
                    newValue = new ArrayItem(
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
                    newNumberValue =
                        this.calcArray[index - 1].intNumberValue /
                        this.calcArray[index + 1].intNumberValue;
                    newValue = new ArrayItem(
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
            newEquationValue += this.calcArray[0].value;
            for (let index = 1; index < this.calcArray.length; index++) {
                if (this.calcArray[index].value == "+") {
                    newEquationValue += this.calcArray[index + 1].value;
                } else if (this.calcArray[index].value == "-") {
                    newEquationValue -= this.calcArray[index + 1].value;
                }
            }

            this.equationValue = newEquationValue;
        };

        multiplicationAndDivision();
        additionAndSubtraction();


    }

    const Equation = {
        calcArray: [],
        calculate: calculate,
        equationValue: NaN,
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
