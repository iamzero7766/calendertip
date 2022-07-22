<template>
  <div class="calender-tip-component">
    <!-- 月开始 -->
    <table v-show="type == 0" class="calender_tip_table">
      <tr class="header-box" :style="{ height: headerHeight + 'px' }">
        <th v-for="item in headerList" :style="{ 'line-height': headerHeight + 'px' }" :key="item" class="header-item">{{ item }}</th>
      </tr>
      <tr v-for="item in calenderList" :key="item.key">
        <td v-for="ele in item.children" :key="ele.key">
          <div 
            @click="showDetailDay(ele)"
            :class="['calender_tip_item', today_set == ele.key ? 'color_back_orange' : 'color_back_white']" 
            v-show="ele.isShow">
            <div :class="['calender_tip_date', ele.isWork ? 'color_normal' : 'color_red', ele.islocal ? '' : 'opticay_calender']">{{ ele.day }}</div>
            <div 
              class="calender_tip_event" 
              :style="{ 'height' : (showEventNumber + 1) * 18 + 'px'}"
              v-if="ele.eventList.length > showEventNumber">
              <div class="calender_tip_label" v-for="(event, index) in ele.eventList" :key="event.event_id" v-show="index < showEventNumber">
                <div 
                  class="calender_tip_paper" 
                  :style="{ 'background': event.event_color ? event.event_color : '#ff4d4f'}"
                >{{ event.event_label }}</div>
              </div>
              <div class="calender_tip_label">
                <div class="calender_tip_extra">
                  +{{ ele.eventList.length - showEventNumber }}
                  <span>更多</span>
                </div>
              </div>
            </div>
            <div 
              class="calender_tip_event" 
              v-else 
              :style="{ 'height' : (showEventNumber + 1) * 18 + 'px'}">
              <div class="calender_tip_label" v-for="event in ele.eventList" :key="event.event_id">
                <div class="calender_tip_paper" :style="{ 'background': event.event_color ? event.event_color : '#ff4d4f'}">{{ event.event_label }}</div>
              </div>
            </div>
          </div>
        </td>
      </tr>
    </table>
    <!-- 月结束 -->

    <!-- 日开始 -->
    <table v-show="type == 1" class="calender_tip_table type_day_style">
      <tr class="header-box" :style="{ height: headerHeight + 'px' }">
        <th width="80px"></th>
        <th>{{ week_title }}</th>
      </tr>
      <tr>
        <td>
          <div class="content_day_left">全天</div>
        </td>
        <td>
          <div class="content_day_right">
            <div 
              class="content_tag" 
              v-for="item in totalTags" 
              :style="{ 'background': item.event_color ? item.event_color : '#ff4d4f'}"
              :key="item.event_id">{{ item.event_tag }}</div>
          </div>
        </td>
      </tr>
      <tr v-for="item in calenderDay" :key="item.time">
        <td>
          <div class="content_day_left">{{ item.time_label }}</div>
        </td>
        <td>
          <div class="content_day_right"></div>
        </td>
      </tr>
    </table>
    <!-- 日结束 -->

    <!-- 周开始 -->
    <!-- 周结束 -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      type: 0,
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
      calenderDay: [],
      totalTags: [],
      today_set: "",
      week_title: "",
    };
  },
  props: {
    calender_type: {
      default: 0,
    },
    dataList: {
      type: Array,
      default: () => [],
    },
    headerHeight: {
      default: 30,
    },
    showEventNumber: {
      default: 4
    },
    dateSet: {
      default: "",
    },
    isShowLastMonth: {
      default: true,
    }
  },
  created() {
    console.log(this.dataList);
    this.setFirstState();
  },
  methods: {
    // 初始化
    setFirstState() {
      this.type = this.calender_type;
      this.today_set = this.getTodayDate();
      if(this.type == 1) {   // 周

      } else if(this.type == 2) { // 日

      } else {  // 月
        var dateSet = this.dateSet ? this.dateSet : new Date();
        var dateList = this.getMonthList(dateSet, this.dataList);
        console.log(dateList);
        this.calenderList = this.setCalenderData(dateList);
        console.log(this.calenderList);
      }
    },


    // 获取日表格
    showDetailDay(item) {
      console.log(item);
      this.type = 1;
      this.week_title = this.getWeekName(new Date(item.key));
      var result = [];
      var event_list = JSON.parse(JSON.stringify(item.eventList));
      this.totalTags = event_list;
      event_list.forEach(item => {
        var list = item.event_time.split(":");
        item.key = list[0];
      })
      for(var i = 1; i <= 24; i++) {
        var time_label = i > 12 ? `下午${i-12}时` : `上午${i}时`;
        var children = event_list.filter(x => x.key == i);
        var arr_item = {
          time_label: time_label,
          children: children
        }
        result.push(arr_item);
      }
      console.log('result', result);
      this.calenderDay = result;
    },


    // 设置表格数据
    setCalenderData(list) {
      var result = [];
      var index = Math.ceil(list.length / 7);
      for(var i = 0; i < index; i++) {
        var index_before = i * 7;
        var index_after = (i + 1) * 7;
        var arr = list.filter((item, index) => index >= index_before && index < index_after);
        result.push({
          key: i,
          children: arr
        });
      }
      return result;
    },

    // 获取星期名字
    getWeekName(date) {
      var week_num = date.getDay();
      var week_list = ["日", "一", "二", "三", "四", "五", "六"];
      return `星期${week_list[week_num]}`;
    },


    // 获取月日历
    getMonthList(date, dataList = []) {
      var month_num = date.getMonth() + 1;  // 月
      var year_num = date.getFullYear();
      var day_num = new Date(year_num, month_num, 0).getDate();   // 一个月有多少天
      var week_no = new Date(year_num, month_num - 1, 1).getDay()   // 1号是周几
      week_no = week_no == 0 ? 7 : week_no;
      var week_last = new Date(year_num, month_num - 1, day_num).getDay();  // 最后一天是周几
      week_last = week_last == 0 ? 7 : week_last;
      var week_arr = [];
      var week_set = [1,2,3,4,5]
      for(var i = 1; i <= day_num; i++) {
        var month = month_num < 10 ? "0" + month_num : month_num;
        var day = i < 10 ? "0" + i : i;
        var content = dataList.find(x => x.label == `${year_num}-${month}-${day}`)
        var day_work = new Date(year_num, month_num - 1, i).getDay();
        var week_item = {
          key: `${year_num}-${month}-${day}`,
          day: i,
          month: month_num,
          year: year_num,
          isWork: content ? content.isWork : week_set.indexOf(day_work) > -1,
          isShow: true,
          eventList: content ? content.eventList : [],
          islocal: true,
        }
        week_arr.push(week_item);
      }
      for(var m = 0; m < 7 - week_last; m++) {
        var month_next = month_num + 1 < 10 ? "0" + (month_num + 1) : month_num + 1;
        var content = dataList.find(x => x.label == `${year_num}-${month_next}-${m + 1}`);
        var day_work_late = new Date(year_num, month, m + 1).getDay();
        var day = "0" + (m + 1);
        week_arr.push({
          key: `${year_num}-${month}-${day}`,
          day: m + 1,
          month: month_next,
          year: year_num,
          isWork: content ? content.isWork : week_set.indexOf(day_work_late) > -1,
          isShow: this.isShowLastMonth,
          eventList: content ? content.eventList : [],
          islocal: false,
        })
      }
      for(var j = 1; j < week_no; j++) {
        var day_last = this.setEarlyDay(new Date(year_num, month_num - 1, 1), j);
        var content = dataList.find(x => x.label == `${year_num}-${month_next}-${m + 1}`);
        var day_work_early = day_last.getDay();
        var month_last = day_last.getMonth() + 1;
        month_last = month_last < 10 ? "0" + month_last : month_last;
        week_arr.unshift({
          key: `${day_last.getFullYear()}-${month_last}-${day_last.getDate()}`,
          day: day_last.getDate(),
          month: month_last,
          year: day_last.getFullYear(),
          isWork: content ? content.isWork : week_set.indexOf(day_work_early) > -1,
          isShow: this.isShowLastMonth,
          eventList: content ? content.eventList : [],
          islocal: false,
        });
      }
      return week_arr;
    },


    // 获取当天日期
    getTodayDate() {
      var year = new Date().getFullYear();
      var month = new Date().getMonth() + 1;
      month = month < 10 ? "0" + month : month;
      var day = new Date().getDate();
      day = day < 10 ? "0" + day : day;
      return `${year}-${month}-${day}`;
    },



    // 获取前几天的日期
    setEarlyDay(date, index) {
      var date_set = new Date(date.getTime() - (index * 24 * 60 * 60 * 1000));
      return date_set;
    }
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
    table-layout: fixed;
    // overflow: hidden;
    th:nth-child(1) {
      border-left: 1px solid #ccc;
    }
    th {
      border-top: 1px solid #ccc;
      border-bottom: 1px solid #ccc;
      border-right: 1px solid #ccc;
    }
    td:nth-child(1) {
      border-left: 1px solid #ccc;
    }
    td {
      border-bottom: 1px solid #ccc;
      border-right: 1px solid #ccc;
    }
    .calender_tip_item {
      box-sizing: border-box;
      padding: 3px;
      width: 100%;
      height: 100%;
      cursor: pointer;
    }
    .calender_tip_date {
      width: 100%;
      height: 14px;
      line-height: 14px;
      font-size: 12px;
      text-align: end;
    }
    .calender_tip_event {
      width: 100%;
      overflow: hidden;
      .calender_tip_label {
        width: 100%;
        box-sizing: border-box;
        padding: 2px 0;
      }
      .calender_tip_paper {
        width: 100%;
        height: 100%;
        color: #fff;
        line-height: 14px;
        font-size: 12px;
        border-radius: 2px;
        white-space: nowrap;        //强制不换行
        overflow: hidden;           //超出部分隐藏
        text-overflow: ellipsis;    //显示省略符号来代表被修剪的文本
      }
      .calender_tip_extra {
        width: 100%;
        height: 100%;
        font-weight: 500;
        color: #4691f9;
        line-height: 14px;
        font-size: 12px;
        white-space: nowrap;        //强制不换行
        overflow: hidden;           //超出部分隐藏
        text-overflow: ellipsis;
      }
    }
    .color_back_orange {
      background: #fff3ef;
    }
    .color_back_white {
      background: #fff;
    }
    .color_normal {
      color: #717273;
    }
    .color_red {
      color: #ff7576;
    }
    .opticay_calender {
      opacity: 0.5;
    }
    .color_normal_opcitay {
      color: rgba(108, 109, 110, 0.5);
    }
    .color_red_opcitay {
      color: rgba(255, 119, 120, 0.5);
    }
  }
  .type_day_style {
    td {
      width: 100%;
      .content_day_left {
        width: 100%;
        min-height: 55px;
        display: flex;
        box-sizing: border-box;
        padding: 0 5px;
        align-items: center;
        justify-content: end;
        font-size: 12px;
        color: #717273;
      }
      .content_day_right {
        width: 100%;
        min-height: 55px;
        display: flex;
        box-sizing: border-box;
        padding: 0 5px;
        align-items: center;
        justify-content: center;
        font-size: 12px;
        color: #717273;
      }
      .content_tag {
        padding: 2px 7px;
        border-radius: 2px;
        margin: 0 3px;
        color: #fff;
      }
    }
  }
}
</style>
