<template>
  <div>
    <input
      :value="maintext"
      @input="inputData"
      @focus="isHoverCalender(true)"
      @blur="isHoverCalender(false,$event)"
      class="border px-2 py-1 border-gray-600 rounded"
      type="text"
    />
    <transition name="fade">
      <div v-if="isShowCalender" class="calenderBorder">
        <div class="flex items-center justify-around mb-2">
          <div class="flex items-center select-none">
            <div class="cursor-pointer" @click="switchYear('prev')">
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
            <div class="select-none rounded px-2 py-1 shadow-lg mx-4">{{ selectYearRef }}</div>
            <div class="cursor-pointer" @click="switchYear('next')">
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
            <select v-model="selectMonthRef" class="border rounded border-gray-500 px-2 py-1">
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
                  @click="dateClick(allSetRef[(col - 1) + (row - 1) * week.length])"
                  class="mx-auto rounded-full w-6 h-6 transition duration-100 ease-in-out hover:bg-blue-300 hover:shadow-2xl cursor-pointer"
                  :class="{ 'bg-red-300': currentDateRef === allSetRef[(col - 1) + (row - 1) * week.length] && currentYearRef === selectYearRef && currentMonthRef === selectMonthRef,
                  'text-gray-600':allSetRef[(col - 1) + (row - 1) * week.length].status !== 'Now'
                  }"
                >{{ allSetRef[(col - 1) + (row - 1) * week.length].mainDate }}</div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </transition>
  </div>
</template>

<script>
import { ref, watch } from "vue";
export default {
  name: "datetimepicker",
  props: {
    maintext: {
      type: String,
      default: "",
    },
  },
  setup(props, { emit }) {
    const week = ref(["Sun.", "Mon.", "Tue.", "Wed.", "Thu.", "Fri.", "Sat."]);
    // get days of month
    const current = new Date();

    const isShowCalender = ref(false);
    const currentYearRef = ref(current.getFullYear());
    const currentMonthRef = ref(current.getMonth() + 1);
    const currentDateRef = ref(current.getDate());

    const selectYearRef = ref(current.getFullYear());
    const selectMonthRef = ref(current.getMonth() + 1);

    const inputData = (e) => {
      emit("update:maintext", e.target.value);
    };

    const isHoverCalender = (hover, event) => {
      isShowCalender.value = hover;
      if (!hover) {
        const expTest = /^\d{4}-\d{2}-\d{2}$/g.test(props.maintext);
        if (!expTest && event.target.value !== "") {
          window.alert("not correspond to dateformat");
          emit("update:maintext", "");
          event.target.value = "";
        }
      }
    };

    const changeContent = (weekLen, chooseYear, chooseMonth) => {
      const numOfPreDate = new Date(chooseYear, chooseMonth - 1, 0).getDate();
      const numOfCurrentDate = new Date(chooseYear, chooseMonth, 0).getDate();
      const currentSet = Array.from({ length: numOfCurrentDate }, (_, i) => {
        return {
          mainDate: i + 1,
          status: "Now",
        };
      });

      // get first day of month is on what week
      const initDate = new Date();
      initDate.setDate(1);
      const firstDayWeek = initDate.getDay();

      // get last day of month is on what week
      const ultDate = new Date();
      ultDate.setDate(numOfCurrentDate);

      // calc the num of row
      const setLen = firstDayWeek + numOfCurrentDate;
      const rowLen = parseInt(setLen / weekLen);
      const isRest = setLen % weekLen;
      const setrow = isRest === 0 ? rowLen : rowLen + 1;

      // get the pastSet
      const pastMonthset = [];
      for (let i = 0; i < firstDayWeek; i++) {
        const pastValue = numOfPreDate - (firstDayWeek - i) + 1;
        pastMonthset.push({
          mainDate: pastValue,
          status: "prev",
        });
      }

      let allSet = [...pastMonthset, ...currentSet];

      if (isRest !== 0) {
        const lastLen = Array.from({ length: isRest }, (_, i) => {
          return {
            mainDate: i + 1,
            status: "next",
          };
        });
        allSet = [...allSet, ...lastLen];
      }

      return {
        firstDayWeek,
        setrow,
        allSet,
      };
    };

    const switchYear = (cmd) => {
      if (cmd === "prev") {
        selectYearRef.value = selectYearRef.value - 1;
        return;
      }
      if (cmd === "next") {
        selectYearRef.value = selectYearRef.value + 1;
        return;
      }
    };

    const dateClick = (iData) => {
      let chooseMonth = selectMonthRef.value;
      if (iData.status === "prev") {
        chooseMonth = chooseMonth === 1 ? 12 : chooseMonth - 1;
      }
      if (iData.status === "next") {
        chooseMonth = chooseMonth === 12 ? 1 : chooseMonth + 1;
      }

      const mainMonth =
        `${chooseMonth}`.length === 1 ? `0${chooseMonth}` : `${chooseMonth}`;

      const mainDate =
        `${iData.mainDate}`.length === 1
          ? `0${iData.mainDate}`
          : `${iData.mainDate}`;
      const dateFmt = `${selectYearRef.value}-${mainMonth}-${mainDate}`;
      emit("update:maintext", dateFmt);
    };

    const { setrow, allSet } = changeContent(
      week.value.length,
      selectYearRef.value,
      selectMonthRef.value
    );

    const setrowRef = ref(setrow);
    const allSetRef = ref(allSet);

    // change changeContent still have problem
    watch(selectYearRef, (val) => {
      const { setrow, allSet } = changeContent(
        week.value.length,
        val,
        selectMonthRef.value
      );
      setrowRef.value = setrow;
      allSetRef.value = allSet;
    });

    watch(selectMonthRef, (val) => {
      const { setrow, allSet } = changeContent(
        week.value.length,
        selectYearRef.value,
        val
      );
      setrowRef.value = setrow;
      allSetRef.value = allSet;
    });

    return {
      inputData,
      isShowCalender,
      isHoverCalender,
      switchYear,
      dateClick,
      selectYearRef,
      selectMonthRef,
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
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease-out;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>