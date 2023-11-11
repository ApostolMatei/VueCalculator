<template>
  <div class="background">
    <div class="calculator">
      <div class="mathtable">
        <div class="clock">
          <span class="hour">12</span>:<span class="minute">30</span>
        </div>
        <div class="status">
          <img src="@/assets/status.png" height="18px" alt="Status" />
        </div>
      </div>
      <div class="value">{{ displayValue }}</div>
      <div class="keyboard">
        <button
          class="button"
          @click="handleClick(key)"
          :class="getClass(index)"
          :key="key"
          v-for="(key, index) in keys"
        >
          {{ key }}
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.background {
  height: 100vh;
  display: grid;
  place-items: center;
  background-color: rgb(255, 255, 255);
}
.clock {
  font-size: 20px;
}

.calculator {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 752px;
  width: 393px;
  font-family: sans-serif;

  background-color: rgb(0, 0, 0);
  border-radius: 40px;
}
.mathtable {
  display: flex;
  height: 100px;
  justify-content: space-between;
  margin: 0 20px 0 20px;
  padding: 15px;
  overflow: hidden;
  color: aliceblue;
}
.value {
  color: aliceblue;
  text-align: end;
  overflow: auto;
  scroll-behavior: smooth;
  padding: 40px 35px 0 20px;
  font-size: min(80px, 10vw);
}
.value::-webkit-scrollbar {
  width: 12px; /* width of the scrollbar */
  height: 7px;
}

.value::-webkit-scrollbar-track {
  background: #000000; /* color of the track */
  border-radius: 10px;
}

.value::-webkit-scrollbar-thumb {
  background: #494848; /* color of the thumb */
  border-radius: 10px;
}

.value::-webkit-scrollbar-thumb:hover {
  background: #727272; /* color of the thumb on hover */
}

.keyboard {
  padding: 0px 20px 20px 20px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 15px;
  grid-auto-rows: minmax(70px, auto);
}
.button {
  height: 77px;

  display: flex;
  justify-content: center;
  align-items: center;
  font-variant-numeric: tabular-nums;
  font-size: 30px;
  font-weight: bold;
  border-radius: 50%;
  cursor: pointer;
  background: #333;
  color: white;
  border: 2px solid black;
}
.button:active {
  filter: brightness(120%);
}
.big-btn {
  border-radius: 55px;
  grid-column: 1/3;
}
.gray-btn {
  background: #a5a5a5;
  color: black;
}
.orange-btn:focus {
  filter: brightness(120%);
}
.gray-btn:focus {
  filter: brightness(120%);
}
.orange-btn {
  background: #f1a33c;
}
.keyboard .button:nth-child(19) {
  background-color: #f1a33c;
}
</style>

<script>
export default {
  data() {
    return {
      displayValue: "0",
      firstOperand: null,
      waitingForSecondOperand: false,
      operator: null,
      firstOperator: false,
      keys: [
        "C",
        "+/-",
        "%",
        "÷",
        "7",
        "8",
        "9",
        "x",
        "4",
        "5",
        "6",
        "-",
        "1",
        "2",
        "3",
        "+",
        "0",
        ".",
        "=",
      ],
    };
  },
  methods: {
    // Method to determine the CSS class for a button based on its index
    getClass(index) {
      const specialButtons = [4, 8, 12, 16];
      if (index < 3) {
        return "gray-btn";
      } else if (specialButtons.includes(index + 1)) {
        return "orange-btn";
      } else if (index == 16) {
        return "big-btn";
      } else {
        return;
      }
    },
    // Method to handle button clicks
    handleClick(button) {
      if (button === "=") {
        this.calculate();
      } else if (button === "C") {
        this.clearAll();
      } else if (
        button === "+" ||
        button === "-" ||
        button === "x" ||
        button === "÷" ||
        button === "%" ||
        button === "." ||
        button === "+/-"
      ) {
        this.handleOperator(button);
      } else {
        this.handleNumber(button);
      }
    },

    // Method to handle number buttons
    handleNumber(button) {
      if (this.waitingForSecondOperand === true) {
        this.displayValue = button;
        this.waitingForSecondOperand = false;
      } else {
        this.displayValue += button;
      }

      const numericValue = parseFloat(this.displayValue.replace(/,/g, ""));
      this.displayValue = numericValue.toLocaleString();
    },

    // Method to handle operator buttons
    handleOperator(button) {
      if (button === "." && !this.displayValue.includes(".")) {
        this.displayValue = this.displayValue + ".";

        return;
      } else if (button === "+/-") {
        this.displayValue = -this.displayValue;
        this.firstOperand = this.displayValue;
        return;
      } else if (this.firstOperand === null) {
        this.firstOperand = parseFloat(this.displayValue.replace(/,/g, ""));
      } else if (this.operator) {
        this.calculate();
      }

      this.operator = button;
      this.waitingForSecondOperand = true;
    },

    // Method to perform the calculation based on the operator
    calculate() {
      let result;

      let secondOperand = parseFloat(this.displayValue.replace(/,/g, ""));

      if (this.operator === "+") {
        result = this.firstOperand + secondOperand;
      } else if (this.operator === "-") {
        result = this.firstOperand - secondOperand;
      } else if (this.operator === "x") {
        result = this.firstOperand * secondOperand;
      } else if (this.operator === "÷") {
        result = this.firstOperand / secondOperand;
      } else if (this.operator === "%") {
        result = (this.firstOperand * secondOperand) / 100;
      } else {
        result = this.displayValue;
      }

      this.operator = null;
      this.displayValue = parseFloat(result).toLocaleString();
      this.firstOperand = result;

      console.log(secondOperand, this.firstOperand, this.operator);
    },

    // Method to clear all calculator values
    clearAll() {
      this.firstOperand = null;
      this.waitingForSecondOperand = false;
      this.operator = 0;
      this.firstOperator === false;
      this.displayValue = "0";
    },
  },
};
</script>
