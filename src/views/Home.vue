<template>
  <div class="home">
    <datetimepicker v-model:maintext="mainDataRef"></datetimepicker>
  </div>
</template>

<script>
// @ is an alias to /src
import datetimepicker from "@/components/Datetimepicker.vue";
import { ref } from "vue"
export default {
  name: "Home",
  components: {
    datetimepicker,
  },
  setup() {
    const mainDataRef = ref("");
    return { mainDataRef };
  },
};
</script>

<style lang="postcss">
</style>
