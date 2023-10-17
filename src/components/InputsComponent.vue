<template>
  <div>
    <input v-model.number="localPrice" placeholder="Price" @input="onInput('price')" />
    <input v-model.number="localQuantity" placeholder="Quantity" @input="onInput('quantity')" />
    <input v-model.number="localAmount" placeholder="Amount" @input="onInput('amount')" />
    <button @click="submitData">Submit</button>
  </div>
</template>

<script>
export default {
  props: {
    price: Number,
    quantity: Number,
    amount: Number
  },
  data() {
    return {
      localPrice: this.price,
      localQuantity: this.quantity,
      localAmount: this.amount
    };
  },
  methods: {
    onInput(field) {
      this.$emit('input-changed', { field, value: this[`local${field.charAt(0).toUpperCase() + field.slice(1)}`] });
    },
    submitData() {
      this.$emit('submit-data', {
        price: this.localPrice,
        quantity: this.localQuantity,
        amount: this.localAmount
      });
    }
  }
};
</script>
