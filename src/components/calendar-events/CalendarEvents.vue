<template>
  <div class="calendar">
    <header class="calendar-header">
      <button type="button" @click="setPrevMonth">
        <svg v-svg symbol="prev" />
      </button>

      <div class="title">{{ currentDate }}</div>

      <button type="button" @click="setNextMonth">
        <svg v-svg symbol="next" />
      </button>
    </header>

    <div class="calendar-events">
      <div class="week-days">
        <div v-for="(day, i) in weekDays" :key="i" class="item">
          {{ day }}
        </div>
      </div>

      <DaySquare
        :prevMonthDaySquare="prevMonthDaySquare"
        :currentMonthDaySquare="currentMonthDaySquare"
        :nextMonthDaySquare="nextMonthDaySquare"
        :selectedDate="selectedDate"
      />
    </div>
  </div>
</template>

<script>
import moment from "moment";
import { fiveRowCell, sixRowCell } from "@/constants/all-cell.constants";
import DaySquare from "@/components/calendar-events/components/DaySquare.vue";

moment.locale("ru");

export default {
  components: { DaySquare },
  name: "CalendarEvents",
  props: {
    events: {
      type: Array,
      default: () => {
        return [];
      },
    },
  },
  data() {
    return {
      selectedDate: moment().format(),
      monthCount: 0,
      prevMonthDaySquare: [],
      currentMonthDaySquare: [],
      nextMonthDaySquare: [],
      weekDays: [
        "понедельник",
        "вторник",
        "среда",
        "четверг",
        "пятница",
        "суббота",
        "воскресенье",
      ],
    };
  },
  computed: {
    firstDayOfMonth() {
      return moment(this.selectedDate).startOf("month").format("dddd");
    },
    daysInMonth() {
      return moment(this.selectedDate).daysInMonth();
    },
    paddingDays() {
      return (
        this.firstDayOfMonth && this.weekDays.indexOf(this.firstDayOfMonth)
      );
    },
    currentDate() {
      return moment(this.selectedDate).format("YYYY") ===
        moment().format("YYYY")
        ? moment(this.selectedDate).format("MMMM")
        : moment(this.selectedDate).format("MMMM YYYY");
    },
    daysInPrevMonth() {
      return moment(this.selectedDate).subtract(1, "M").daysInMonth();
    },
  },
  mounted() {
    this.initDaySquare();
  },
  methods: {
    initDaySquare() {
      if (this.prevMonthDaySquare) {
        this.prevMonthDaySquare = [];
      }

      if (this.currentMonthDaySquare) {
        this.currentMonthDaySquare = [];
      }

      if (this.nextMonthDaySquare) {
        this.nextMonthDaySquare = [];
      }

      for (
        let i = this.daysInPrevMonth - this.paddingDays;
        i < this.daysInPrevMonth;
        i++
      ) {
        this.prevMonthDaySquare.push({
          number: i + 1,
        });
      }

      for (let i = 0; i < this.daysInMonth; i++) {
        this.currentMonthDaySquare.push({
          number: i + 1,
          events: this.events
            .filter(
              (el) =>
                moment(el.date).format("D-MM-YYYY") ===
                `${i + 1}-${moment(this.selectedDate).format("MM-YYYY")}`
            )
            .sort((a, b) => moment(a.date) - moment(b.date)),
        });
      }

      const allCell = this.paddingDays > 4 ? sixRowCell : fiveRowCell;

      for (
        let i = 0;
        i < allCell - (this.daysInMonth + this.paddingDays);
        i++
      ) {
        this.nextMonthDaySquare.push({
          number: i + 1,
        });
      }
    },
    setSelectDate(count) {
      this.selectedDate = moment().add(count, "M").startOf("month").format();
      this.initDaySquare();
    },
    setPrevMonth() {
      this.monthCount--;
      this.setSelectDate(this.monthCount);
    },
    setNextMonth() {
      this.monthCount++;
      this.setSelectDate(this.monthCount);
    },
  },
};
</script>

<style lang="scss">
@import "@/styles/calendar.scss";
</style>
