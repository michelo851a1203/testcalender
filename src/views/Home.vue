<template>
  <div class="home">
    <!-- <button @click="removeFunc" class="bg-blue-500 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded">測試內容</button> -->
    <datetimepicker
      @isinner="testClick"
      v-model:isShowCalender="isShowCalenderRef"
      v-model:maintext="mainDataRef"
    ></datetimepicker>
  </div>
</template>

<script>
// @ is an alias to /src
import datetimepicker from "@/components/Datetimepicker.vue";
import { ref } from "vue";
export default {
  name: "Home",
  components: {
    datetimepicker,
  },
  setup() {
    const mainDataRef = ref("");
    const isShowCalenderRef = ref(false);
    const testClick = (inner) => {
      console.log(inner);
    };

    const removeFunc = () => {
      isShowCalenderRef.value = false;
    };
    return { mainDataRef, isShowCalenderRef, removeFunc, testClick };
  },
};
</script>

<style lang="postcss">
</style>
