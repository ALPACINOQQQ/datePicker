<template>
  <div v-click-show class="datePicker">
    <input type="text" class="inputValue" :value="formatDate">
    <div class="panel" v-if="panelShow">
      <div class="panelHeader">
        <span @click="prevYear">&lt;&lt;</span>
        <span @click="prevMonth">&lt;</span>
        <span>{{time.year}}年{{time.month + 1}}月</span>
        <span @click="nextMonth">&gt;</span>
        <span @click="nextYear">&gt;&gt;</span>
      </div>
      <div class="panelBody">
        <div class="days">
        <div v-for="i in 6" :key=" '_' + i">
          <span v-for="j in 7" :key="j" class="cell" @click="chooseDate(visibleDays[(i - 1) * 7 + (j-1)])" :class="[
          {notCurrentMonth:!isCurrentMonth(visibleDays[(i - 1) * 7 + (j-1)])},
          {isToday: isToday(visibleDays[(i - 1) * 7 + (j-1)])}
          ]">
            {{visibleDays[(i - 1) * 7 + (j-1)].getDate()}}
          </span>
        </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as utils from './utils'
export default {
  // 自定义指令 点击日期组件内不关闭面板  点击其他区域关闭
  directives: {
    clickShow: {
      bind(el, bingings, vnode) {
      let handler = e => {
        // 如果点击的地方是包含e.target元素而且日期面板没显示就显示日期面板 
        // 如果不是点击的日期组件内的元素 并且日期面板处于显示状态就隐藏面板
        if(el.contains(e.target)) {
          if(!vnode.context.panelShow) {
          vnode.context.focus()
          }
        } else {
          if(vnode.context.panelShow) {
          vnode.context.blur()
          }
        }
      }
      el.handler = handler
      document.addEventListener('click', handler)
      },
      unbind(el) {
        document.removeEventListener('click', el.handler)
      }
    }
  },
  data() {
    let {year, month} = utils.getRequiredDate(new Date())
    return {
      panelShow: false,
      time: {year, month},
      formatDate: ''
    }
  },
  methods: {
    // 组件获取焦点打开面板
    focus() {
      this.panelShow = true
    },
    // 失去焦点关闭面板
    blur() {
      this.panelShow = false
    },
    // 是否是当前月
    isCurrentMonth(date) {
      let {year, month} = utils.getRequiredDate(utils.getDate(this.time.year,this.time.month, 1))
      let {year: y, month: m} = utils.getRequiredDate(date)
      return year === y && month === m
    },
    // 是否是当天
    isToday(date) {
      let {year: y, month: m, day: d} = utils.getRequiredDate(date)
      let {year, month, day} = utils.getRequiredDate(new Date())
      return year === y && month === m && day === d
    },
    // 选中日期
    chooseDate(date) {
      let {year, month, day} = utils.getRequiredDate(date)
      this.formatDate = `${year}/${month + 1}/${day}`
      this.time = utils.getRequiredDate(date)
      this.$emit('input', this.formatDate)
      this.blur()
    },
    // 上一个月
    prevMonth() {
      let d = utils.getDate(this.time.year, this.time.month, 1)
      d.setMonth(d.getMonth() - 1)
      this.time = utils.getRequiredDate(d)
    },
    // 下一个月
    nextMonth() {
      let d = utils.getDate(this.time.year, this.time.month, 1)
      d.setMonth(d.getMonth() + 1)
      this.time = utils.getRequiredDate(d)
    },
    // 上一年
    prevYear() {
      let d = utils.getDate(this.time.year, this.time.month, 1)
      d.setYear(d.getFullYear() - 1)
      this.time = utils.getRequiredDate(d)
    },
    // 下一年
    nextYear() {
      let d = utils.getDate(this.time.year, this.time.month, 1)
      d.setYear(d.getFullYear() + 1)
      this.time = utils.getRequiredDate(d)
    }
  },
  computed: {
    // 日期面板数据
    visibleDays() {
      // 先获取今天周几
      let {year, month} = utils.getRequiredDate(utils.getDate(this.time.year, this.time.month, 1))
      // 获取当前月份第一天
      let currentFirstDay = utils.getDate(year,month,1)
      // 先生成一个当前日期
      // 获取当前是周几，需要把天数往前移几天
      let week = currentFirstDay.getDay()
      let startDay = currentFirstDay - week * 60 *60 *1000 * 24
      // 在循环42天
      let arr = []
      for(let i = 0; i<42;i++) {
        arr.push(new Date(startDay + i * 60 *60 *1000* 24))
      }
      return arr
    }
  }
}
</script>

<style lang="less">
.datePicker {
  width: 300px;
}
.inputValue {
  outline: none;
}
.panel {
  width: 220px;
  border: 1px solid #ccc;
  .panelHeader {
    display: flex;
    justify-content: space-around;
    cursor: pointer;
  }
  .cell {
    width: 30px;
    height: 30px;
    display: inline-flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    color: #000000;
    box-sizing: border-box;
  }
  .cell:hover {
    border: 1px solid rgb(77, 51, 51);
  }
  .notCurrentMonth {
    color: gray;
  }
  .isToday {
    background: blue;
  }
}
</style>