<template>
  <div>
    <input class="border px-2 py-1 border-gray-600 rounded" type="text" />
    <div class="calenderBorder">
      <div class="flex items-center justify-around mb-2">
        <div class="flex items-center">
          <div>
            <svg class="w-4" viewBox="0 0 100 100">
              <polyline
                points="65 20 30 50 65 80"
                stroke-width="10"
                stroke="black"
                fill="transparent"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </div>
          <div class="rounded px-2 py-1 shadow-lg mx-4">{{ currentYearRef }}</div>
          <div>
            <svg class="w-3" viewBox="0 0 100 100">
              <polyline
                points="25,10 80,50 25,90"
                stroke="black"
                stroke-width="16"
                stroke-linecap="round"
                stroke-linejoin="round"
                fill="transparent"
              />
            </svg>
          </div>
        </div>
        <div>
          <label class="mr-2" for>month</label>
          <select v-model="currentMonthRef" class="border rounded border-gray-500 px-2 py-1">
            <option v-for="item in 12" :key="item" class="bg-white" :value="item">{{ item }}</option>
          </select>
        </div>
      </div>
      <table class="w-full">
        <thead>
          <tr>
            <th class="px-1" v-for="item in week" :key="item">{{ item }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in setrowRef" :key="row">
            <td class="text-center" v-for="col in week.length" :key="col">
              <div
                class="mx-auto rounded-full w-6 h-6 transition duration-300 ease-in-out hover:bg-blue-300 hover:shadow-2xl cursor-pointer"
                :class="{ 'bg-red-300': currentDateRef === allSetRef[(col - 1) + (row - 1) * week.length] }"
              >{{ allSetRef[(col - 1) + (row - 1) * week.length] }}</div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  name: "datetimepicker",
  setup() {
    const week = ref(["Sun.", "Mon.", "Tue.", "Wed.", "Thu.", "Fri.", "Sat."]);

    // get days of month
    const current = new Date();
    const currentYear = current.getFullYear();
    const currentMonth = current.getMonth() + 1;
    const currentDate = current.getDate();

    const currentYearRef = ref(currentYear);
    const currentMonthRef = ref(currentMonth);
    const currentDateRef = ref(currentDate);

    // get num of month
    const numOfPreDate = new Date(currentMonth, currentYear - 1, 0).getDate();
    const numOfCurrentDate = new Date(currentMonth, currentYear, 0).getDate();
    numOfPreDate, numOfCurrentDate;
    const currentSet = Array.from(
      { length: numOfCurrentDate },
      (_, i) => i + 1
    );

    // get first day of month is on what week
    const initDate = new Date();
    initDate.setDate(1);
    const firstDayWeek = ref(initDate.getDay());

    // get last day of month is on what week
    const ultDate = new Date();
    ultDate.setDate(numOfCurrentDate);

    // calc the num of row
    const setLen = firstDayWeek.value + numOfCurrentDate;
    const rowLen = parseInt(setLen / week.value.length);
    const isRest = setLen % week.value.length;
    const setrow = isRest === 0 ? rowLen : rowLen + 1;
    const setrowRef = ref(setrow);

    // get the pastSet
    const pastMonthset = [];
    for (let i = 0; i < firstDayWeek.value; i++) {
      const pastValue = numOfPreDate - (firstDayWeek.value - i) + 1;
      pastMonthset.push(pastValue);
    }

    // get future set
    let allSet = [...pastMonthset, ...currentSet];
    if (isRest !== 0) {
      const lastLen = Array.from({ length: isRest }, (_, i) => i + 1);
      allSet = [...allSet, ...lastLen];
    }
    const allSetRef = ref(allSet);

    return {
      currentYearRef,
      currentMonthRef,
      currentDateRef,
      week,
      setrowRef,
      allSetRef,
    };
  },
};
</script>

<style scoped lang="postcss">
.calenderBorder {
  @apply py-4 rounded bg-gray-300 shadow-xl;
  max-width: 20rem;
}
</style>