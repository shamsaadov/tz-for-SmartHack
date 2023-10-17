<template>
  <div>
    <div>
      Price: {{ price }} | Quantity: {{ quantity }} | Amount: {{ amount }}
    </div>
    <div v-if="localStorageData">
      Local Storage Data: {{ localStorageData }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    price: Number,
    quantity: Number,
    amount: Number,
    localStorageData: String
  }
};
</script>
