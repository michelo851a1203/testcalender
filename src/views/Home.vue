<template>
  <div class="home">
    <datetimepicker></datetimepicker>
  </div>
</template>

<script>
// @ is an alias to /src
import datetimepicker from "@/components/Datetimepicker.vue";
export default {
  name: "Home",
  components: {
    datetimepicker,
  },
};
</script>

<style lang="postcss">
</style>
