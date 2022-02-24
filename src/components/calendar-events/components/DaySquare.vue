<template>
  <div class="daySquare">
    <div
      v-for="(day, i) in prevMonthDaySquare"
      :key="`prevMonthDaySquare-${i}`"
      class="item"
      :class="{ disabled: isDisabled(day.number, prevMonthName) }"
    >
      <div class="day-number">
        {{ day.number }}
      </div>
    </div>

    <div
      v-for="(day, i) in currentMonthDaySquare"
      :key="`currentMonthDaySquare-${i}`"
      class="item"
      :class="{
        today: isToday(day.number),
        disabled: isDisabled(day.number),
      }"
    >
      <div class="day-number">
        {{ day.number }}
      </div>
      <template v-if="day.events">
        <div v-for="(event, i) in day.events" :key="i" class="event">
          <span :class="event.type">
            {{ eventTime(event) }} {{ event.title }}
          </span>
        </div>
      </template>
    </div>

    <div
      v-for="(day, i) in nextMonthDaySquare"
      :key="`nextMonthDaySquare-${i}`"
      class="item"
      :class="{ disabled: isDisabled(day.number, nextMonthName) }"
    >
      <div class="day-number">
        {{ day.number }}
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import { prevMonth, nextMonth } from "@/constants/month-name.constants";

moment.locale("ru");

export default {
  name: "DaySquare",
  props: {
    prevMonthDaySquare: {
      type: Array,
      default: () => {
        return [];
      },
    },
    currentMonthDaySquare: {
      type: Array,
      default: () => {
        return [];
      },
    },
    nextMonthDaySquare: {
      type: Array,
      default: () => {
        return [];
      },
    },
    selectedDate: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      prevMonthName: prevMonth,
      nextMonthName: nextMonth,
    };
  },
  methods: {
    isDisabled(number, monthDaySquareName) {
      if (monthDaySquareName === this.prevMonthName) {
        return moment(moment().format("YYYY-MM-D")).isAfter(
          `${moment(this.selectedDate)
            .subtract(1, "M")
            .format("YYYY-MM")}-${number}`
        );
      } else if (monthDaySquareName === this.nextMonthName) {
        return moment(moment().format("YYYY-MM-D")).isAfter(
          `${moment(this.selectedDate).add(1, "M").format("YYYY-MM")}-${number}`
        );
      } else {
        return moment(moment().format("YYYY-MM-D")).isAfter(
          `${moment(this.selectedDate).format("YYYY-MM")}-${number}`
        );
      }
    },
    isToday(number) {
      return (
        `${number}-${moment(this.selectedDate).format("MM-YYYY")}` ===
        moment().format("D-MM-YYYY")
      );
    },
    eventTime(event) {
      return moment(event.date).format("HH:mm");
    },
  },
};
</script>
