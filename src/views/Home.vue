<template>
  <div class="home">
    <input class="border px-2 py-1 border-gray-600 rounded" type="text" />
    <div class="calenderBorder">
      <div class="w-full flex item-centers justify-center">
        <div class="mx-1 rounded" v-for="item in week" :key="item">{{ item }}</div>
      </div>
      <div v-for="row in 5" :key="row">

      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { ref } from "vue";
export default {
  name: "Home",
  components: {},
  setup() {
    const week = ref(["Sun.", "Mon.", "Tue.", "Wed.", "Thu.", "Fri.", "Sat."]);
    console.log(cDate);
    
    return { week };
  },
};
</script>

<style lang="postcss">
.calenderBorder {
  @apply px-3 py-2 rounded bg-blue-300 h-64;
  max-width: 18rem;
}
</style>
