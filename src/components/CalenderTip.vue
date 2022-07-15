<template>
  <div class="calender-tip-component">
    <table v-show="type == 1" class="calender_tip_table">
      <tr class="header-box" :style="{ height: headerHeight + 'px' }">
        <th v-for="item in headerList" :style="{ 'line-height': headerHeight + 'px' }" :key="item" class="header-item">{{ item }}</th>
      </tr>
      <tr v-for="item in calenderList" :key="item.key">
        <td v-for="ele in item.children" :key="ele.date"></td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      headerList: [
        "星期一",
        "星期二",
        "星期三",
        "星期四",
        "星期五",
        "星期六",
        "星期日",
      ],
      calenderList: [],
    };
  },
  props: {
    type: {
      default: 0,
    },
    eventList: {
      type: Array,
    },
    dataList: {
      type: Array,
    },
    headerHeight: {
      default: 30,
    },
    dateTime: {
      default: new Date(),
    }
  },
  created() {
    this.setFirstState();
  },
  methods: {
    // 初始化
    setFirstState() {
      if(this.type == 1) {   // 周

      } else if(this.type == 2) { // 日

      } else {  // 月
        var dateList = this.getMonthList(this.dateTime);
      }
    },


    // 获取月日历
    getMonthList(date) {
      var month_num = date.getMonth() + 1;  // 月
      var year_num = date.getFullYear();
      var day_num = new Date(year_num, month_num, 0).getDate();   // 一个月有多少天
      var week_no = new Date(year_num, month_num - 1, 1).getDay()   // 1号是周几
      
    },
  },
};
</script>

<style lang="scss">
.calender-tip-component {
  width: 100%;
  height: 100%;
  .calender_tip_table {
    width: 100%;
    height: 100%;
    border-spacing: 0;
    th:nth-child(1) {
      border-left: 1px solid #ccc;
    }
    th {
      border-top: 1px solid #ccc;
      border-bottom: 1px solid #ccc;
      border-right: 1px solid #ccc;
    }
  }
}
</style>
