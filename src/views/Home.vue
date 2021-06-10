<template>
  <div>
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from "../components/HelloWorld.vue";

export default {
  components: { HelloWorld },

  data() {
    return {};
  },
};
</script>

<style scoped>
.error {
  font-weight: bold;
  color: red;
}
</style>