<template>
  <div class="home">
    <calender-tip :dataList="dataList" :dateSet="dateSet"></calender-tip>
  </div>
</template>

<script>
// @ is an alias to /src
import CalenderTip from "@/components/CalenderTip.vue";

export default {
  name: "HomeView",
  components: {
    CalenderTip,
  },
  data() {
    return {
      dataList: [],
      dateSet: ''
    }
  },
  created() {
    this.dateSet = new Date();
    this.dataList = this.getDateList();
  },
  methods: {
    getDateList() {
      var arr = [
        {
          label: "2022-07-22",
          eventList: [
            {
              event_id: 1,
              event_color: "#864ad7",
              event_label: "zzz_test: aa11",
              event_tag: "第一",
              event_time: "08:00"
            },
            {
              event_id: 2,
              event_color: "#0ab8bc",
              event_label: "zzz_test: aa22+++++++++",
              event_tag: "第二",
              event_time: "10:00"
            },
            {
              event_id: 3,
              event_color: "#fa8c16",
              event_label: "zzz_test: aa33",
              event_time: "10:00",
              event_tag: "第三",
            },
            {
              event_id: 4,
              event_color: "#ff4d4f",
              event_label: "zzz_test: aa44",
              event_time: "10:00",
              event_tag: "第四",
            },
            {
              event_id: 5,
              event_color: "#fa8c16",
              event_label: "zzz_test: aa55",
              event_tag: "第五",
              event_time: "10:00"
            },
            {
              event_id: 6,
              event_color: "#ff4d4f",
              event_label: "zzz_test: aa66",
              event_tag: "第六",
              event_time: "10:00"
            },
          ],
        },
      ];
      return arr;
    },
  }
};
</script>
