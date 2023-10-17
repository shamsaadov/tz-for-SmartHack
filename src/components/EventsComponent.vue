<template>
  <div>
    <div v-for="event in events" :key="event.id">
      {{ event.info }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    events: Array
  }
};
</script>
