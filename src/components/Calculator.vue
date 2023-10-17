<template>
  <div class="centered-container">
    <InputsComponent
        :price="price"
        :quantity="quantity"
        :amount="amount"
        @input-changed="handleInput"
        @submit-data="submitData"
    />
    <DisplayComponent
        :price="price"
        :quantity="quantity"
        :amount="amount"
        :localStorageData="localStorageData"
    />
    <EventsComponent :events="events" />
  </div>
</template>

<script>
import { debounce } from 'lodash';
import InputsComponent from "@/components/InputsComponent.vue";
import DisplayComponent from "@/components/DisplayComponent.vue";
import EventsComponent from "@/components/EventsComponent.vue";

export default {
  name: 'BasicCalculator',
  components: {EventsComponent, DisplayComponent, InputsComponent},
  data() {
    return {
      price: null,
      quantity: null,
      amount: null,
      events: [],
      localStorageData: null,
      lastModified: null,
      nonce: 0
    };
  },
  methods: {
    handleInput(data) {
      this[data.field] = data.value;
      this.debounceCalculate();
    },

    calculate() {
      if (!this.price && !this.quantity && !this.amount) {
        this.price = null;
        this.quantity = null;
        this.amount = null;
        return;
      }

      if (this.lastModified === 'price') {
        if (this.quantity === 0) {
          this.amount = 0;
        } else {
          this.amount = this.price * (this.quantity || 0);
        }
      } else if (this.lastModified === 'quantity') {
        if (this.amount === 0) {
          this.price = 0;
        } else {
          this.price = (this.amount || 0) / (this.quantity || 1);
        }
      } else if (this.lastModified === 'amount') {
        if (this.price === 0) {
          this.quantity = 0;
        } else {
          this.quantity = (this.amount || 0) / (this.price || 1);
        }
      }
      this.events.unshift({ id: Date.now(), info: 'Inputs changed' });
    },

    submitData() {
      const data = { nonce: this.nonce++, price: this.price, quantity: this.quantity, amount: this.amount };
      this.events.unshift({ id: Date.now(), info: `Submitting: ${JSON.stringify(data)} | LocalStorage at submit: ${localStorage.getItem('data')}` });
      setTimeout(() => {
        if (this.amount % 2 === 0) {
          localStorage.setItem('data', JSON.stringify(data));
          this.localStorageData = JSON.stringify(data);
          this.events.unshift({ id: Date.now(), info: `Backend response: {success: true} | Current LocalStorage: ${localStorage.getItem('data')}` });
        } else {
          this.events.unshift({ id: Date.now(), info: `Backend response: {success: false} | Current LocalStorage: ${localStorage.getItem('data')}` });
        }
      }, 1000);
    },

    debounceCalculate: debounce(function() {
      this.calculate();
    }, 300)
  },
  watch: {
    price() {
      this.lastModified = 'price';
    },
    quantity() {
      this.lastModified = 'quantity';
    },
    amount() {
      this.lastModified = 'amount';
    }
  },
  mounted() {
    const storedData = localStorage.getItem('data');
    if (storedData) {
      const parsedData = JSON.parse(storedData);
      this.price = parsedData.price;
      this.quantity = parsedData.quantity;
      this.amount = parsedData.amount;
      this.localStorageData = storedData;
    }
  }
};
</script>

<style>
body {
  font-family: 'Arial', sans-serif;
  background-color: #f5f5f5;
  margin: 0;
  padding: 0;
}

div {
  box-sizing: border-box;
}

.centered-container {
  margin-left: 15rem;
  margin-top: 15rem;
}

input, button {
  padding: 10px;
  margin: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
}

button {
  background-color: #007BFF;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #0056b3;
}

.EventsComponent, .DisplayComponent, .InputsComponent {
  margin: 20px;
  padding: 20px;
  border-radius: 5px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

</style>
