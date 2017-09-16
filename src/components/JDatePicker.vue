<template>
  <div class="full-jcalendar">
    <div class="full-jcalendar__input" :class="{'is-hover':clearVisible}" @click="onInputFocus" :style="{width:(handleWidth-20)+'px'}">
      <p class="input-text" :style="{width:(handleWidth-20-20)+'px'}">{{ inputText }}</p>
      <p class="icon-tip" v-show="isLunar && showLunarIcon">
        <i v-if="isLeap">闰</i>
        <i v-else>农</i>
      </p>
      <p class="input-icon" @mouseover="onInputOver" @mouseout="onInputOut">
        <i class="iconfont icon-time" style="display: none"></i>
        <i class="iconfont icon-richeng" v-show="!clearVisible"></i>
        <i class="iconfont icon-guanbi is-clear" v-show="clearVisible" @click="onClearInput"></i>
      </p>

      <input type="text" :value="value" style="display: none" @input="value = $event.target.value" />
    </div>
    <div class="full-jcalendar__main" v-show="calendarVisible">

      <div class="full-jcalendar-header">
        <span class="title-year" @click="showYearList">{{year}}年</span>
        <span style="width:10px;"></span>
        <span class="title-month" @click="monthVisible = true">{{month}}月</span>
        <span style="width:10px;"></span>
        <label><input type="checkbox" v-model="isLunar" @change="onLunarChange" />农历</label>
        <!-- header 切换月份 -->
        <p class="prev-month" @click.stop="goPrev"><i class="icon iconfont icon-xiangzuojiantou"></i></p>
        <p class="next-month" @click.stop="goNext"><i class="icon iconfont icon-xiangyoujiantou"></i></p>
      </div>
      <!-- body  日期和事件 -->
      <div class="full-jcalendar__body">
        <div class="weeks">
          <strong class="week">日</strong>
          <strong class="week">一</strong>
          <strong class="week">二</strong>
          <strong class="week">三</strong>
          <strong class="week">四</strong>
          <strong class="week">五</strong>
          <strong class="week">六</strong>
        </div>
          <!--日期 周  行-->
          <div class="week-row" v-for="week in calendarDatas">
            <!--日期 日  单元格-->
            <div class="day-cell" v-for="day in week"
                 :class="[{'today': day.isToday,'select':isSelect(day),'not-cur-month': !day.isCurMonth }]"
                 @click="onDateSelect(day)">
              <!-- 日期 节气 农历 -->
              <div class="day-number">
                <p class="solar">
                  <!--<span class="is-leap" v-if="day.lDate.isLeap && day.lDate.lMonth>10 &&day.lDate.IDayCn==='初一'">闰</span>-->
                  <span class="is-leap" v-if="day.lDate.isLeap && day.lDate.IDayCn==='初一'">闰</span>
                  <span :class="['number',{'is-today':day.isToday,'is-empty':day.isToday && !day.lDate.isFestival && !day.lDate.isTerm}]">{{day.isToday?'今天':day.day}}</span>
                </p>
                <p class="lunar" v-show="!day.isToday || day.lDate.isFestival || day.lDate.isTerm">
                  <span class="festival" v-if="day.lDate.isFestival">{{day.lDate.festival}}</span>
                  <span class="term" v-else-if="day.lDate.isTerm">{{day.lDate.Term}}</span>
                  <!--<span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.isLeap && day.lDate.lMonth>10?day.lDate.IMonthCn.replace('闰',''):day.lDate.IMonthCn}}</span>-->
                  <span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.IMonthCn.replace('闰','')}}</span>
                  <span v-else>{{day.lDate.IDayCn}}</span>
                </p>
              </div>

            </div>

        </div>
      </div>

      <!-- 月份切换 -->
      <div class="full-jcalendar__month" v-show="monthVisible">

        <div class="full-jcalendar-header">
          <span>{{month}}月</span>
        </div>

        <div class="full-jcalendar__body">
          <ul class="data-list">
            <li @click="onMonthSelect(m-1)" v-for="m in 12" :key="m">{{m}}月</li>
          </ul>
          <p class="clearfix"></p>
          <p class="close" @click="monthVisible=false">取 消</p>
        </div>

      </div>
      <!-- 年份切换 -->
      <div class="full-jcalendar__year" v-show="yearVisible">

        <div class="full-jcalendar-header">
          <span>{{year}}年</span>
          <!-- header 年份翻页 -->
          <p class="prev-month" @click.stop="goYearPrev"><i class="icon iconfont icon-xiangzuojiantou"></i></p>
          <p class="next-month" @click.stop="goYearNext"><i class="icon iconfont icon-xiangyoujiantou"></i></p>
        </div>

        <div class="full-jcalendar__body">
          <ul class="data-list">
            <li :class="{'select-year':(m + startYear -1)==year,'curr-year':(m + startYear -1)==todayYear}" @click="onYearSelect(m + startYear -1)" v-for="m in 12" :key="m">{{m + startYear -1}}</li>
          </ul>
          <p class="clearfix"></p>
          <p class="close" @click="yearVisible=false">取 消</p>
        </div>
      </div>

    </div>
  </div>
</template>
<script>
  import moment from 'moment';
  import JDatePickerScript from './jDatePicker.js'


  export default {
    props: {
      width: {
        type: String,
        default: '200',
      },
      value: {
//        type:String,
        default: ''
      },
      format: {
        type: String,
        default: 'YYYY-MM-DD'
      },
      showLunarClass: {
        type: String,
        default: 'NUMBER',//共四种，FULLLUNAR－全农历格式：丁酉[鸡]年己酉月乙巳日；LUNAR－中文格式：丁酉[鸡]年七月廿三；NUMBER－数字格式：2017-09-13；MIX格式：丁酉(2017)[鸡]年七月廿三；
      },
      showLunarIcon:{
        type: Boolean,
        default: false,
      },
      laterCurrentYears:{
        type: Number,
        default: 2,
      },
      lunarDefault:{
        type: Boolean,
        default: false,
      },
    },
    watch: {
      'value': function (value, oldValue) {
          this.inputText = '';
          this.isLeap = false;
          if (value == null || value == '') {
            let day = moment();
            this.monthDay = day.toISOString();
            this.valueDay = day.toISOString();
            return;
          }
          this.isLunar = false;
          let isLunar = false;
          if (value.indexOf('L') > -1) {
            isLunar = true;
            this.isLunar = true;
          }
          if (value.indexOf('LR') > -1) {
            this.isLeap = true;
          }

          let val = value.replace(/LR/, '').replace(/L/, '');
          val = new Date(val);
          let valueDay = val.toISOString();
          let result = moment(val).format(this.format.toUpperCase());
          let showLunarClass = this.showLunarClass.toUpperCase();
          if (isLunar) {
            if (showLunarClass != 'NUMBER') {

              this.calendarDatas2.map(d => {

                let tip = 'L' + (d.lDate.isLeap ? 'R' : '');
                let date = new Date(d.lDate.lYear + '-' + d.lDate.lMonth + '-' + d.lDate.lDay).toISOString();
                let ldate = tip + moment(date).format("YYYY-MM-DD")

                if (ldate == value) {
                  valueDay = new Date(d.date).toISOString();
                  if (showLunarClass == 'MIX') {
                    result = d.lDate.gzYear + '(' + d.lDate.lYear + ')[' + d.lDate.Animal + ']年 ' + d.lDate.IMonthCn + d.lDate.IDayCn;
                    return;
                  }
                  if (showLunarClass == 'FULLLUNAR') {
                    result = d.lDate.gzYear + '[' + d.lDate.Animal + ']年 ' + d.lDate.gzMonth + '月 ' + d.lDate.gzDay + '日';
                    return;
                  }
                  if (showLunarClass == 'LUNAR') {
                    result = d.lDate.gzYear + '[' + d.lDate.Animal + ']年 ' + d.lDate.IMonthCn + d.lDate.IDayCn;
                    return;
                  }
                }
              });
            }
          }
          console.log('valueDay', valueDay)
          this.valueDay = valueDay;
          this.inputText = result;

      },
      'lunarDefault': function (value, oldValue) {
        let lunar = false;
        if(value != null && value != '') lunar = value;
        this.isLunar = lunar;
      },
      'showLunarIcon': function (value, oldValue) {
      },
    },
    data () {
      return {
        inputText:'',
        todayYear:0,
        startYear:0,//年份列表的起始年份
        monthDay: '',//月份日期类型，用来比较日期
        valueDay:'',//选择农历后，相对应的公历日期值
        year: 0,
        month: 0,
        tempDay: '',//选择年份活月份切换时，临时存储原值
        isCurrMonth: true,
        calendarDatas: [],
        calendarDatas2: [],//一维化
        calendarVisible: false,//日历显示开关
        yearVisible: false,//年份选择开关
        monthVisible: false,//月份选择开关
        clearVisible:false,//清除按钮开关
        isLunar: false,//是否取农历值
        isLeap: false,//是否闰月
      }
    },
    computed: {
      handleWidth: function () {
        let w = this.width.toString();
        if (w == null || w == '') {
          return 200;
        }
        if (w.indexOf('px') > -1) w = w.replace(/px/, '');
        let w0 = parseInt(w) || 0;
        if (w0 == 0) {
          return 200;
        }
        return w0;
      },
    },
    created () {
      let month = moment()
      this.monthDay = month.toISOString();
      this.todayYear = month.year();
      this.loadCalendar();

      document.addEventListener('click', (e) => {
        let fjMain = this.$el.querySelector('.full-jcalendar__main');
        let fjInput = this.$el.querySelector('.full-jcalendar__input');
        if (!fjMain.contains(e.target) && !fjInput.contains(e.target)) {
          this.calendarVisible = false;
          if(this.value!=null && this.value!='') {

            let isYear = moment(this.monthDay).isSame(this.valueDay, 'year');   //this.value.indexOf('-'+(this.month<10?'0'+this.month:this.month)+'-')==-1;
            let isMonth = moment(this.monthDay).isSame(this.valueDay, 'month'); //this.value.indexOf('-'+(this.month<10?'0'+this.month:this.month)+'-')==-1;
            if (!isYear || !isMonth) {
              this.monthDay = this.valueDay;
            }
          }
        }
      });

    },
    methods: {
      onClearInput(){
        this.$emit('input','');
        this.clearVisible = false;
      },
      onInputOver(){
        if(this.value ==null || this.value == '')return;
        this.clearVisible = true;
      },
      onInputOut(){
        this.clearVisible = false;
      },
      onLunarChange(){
//        var ddd = 'L2017-12-1'
//        var ccc = ddd.replace(/L/,'')
//        console.log(ddd,ccc)
        if(this.value == null || this.value == '') return false;
        this.calendarDatas2.map(d => {
          let tip = 'L' + (d.lDate.isLeap ? 'R' : '');
          let date = new Date(d.lDate.lYear + '-' + d.lDate.lMonth + '-' + d.lDate.lDay).toISOString();
          let result = tip + moment(date).format("YYYY-MM-DD")
          if (this.isLunar) {
            //由公历到农历
            if (moment(d.date).isSame(this.value)) {
              this.$emit('input', result);
            }
          } else {
            //由农历到公历
            if (result == this.value) {
              this.$emit('input', d.date);
            }
          }
        });
      },
      isSelect(day){
        if (this.value == null || this.value == '') return false;
        if (this.isLunar) {
          let tip = 'L' + (day.lDate.isLeap ? 'R' : '');
          let date = new Date(day.lDate.lYear + '-' + day.lDate.lMonth + '-' + day.lDate.lDay).toISOString();
          let result = tip + moment(date).format("YYYY-MM-DD")
          if (result == this.value) {
            return true;
          }
          return false;
        }
        return this.value.replace(/L/, '').replace(/R/, '') == day.date;//moment(day.date).isSame(this.value.replace(/L/,'').replace(/R/,''));
      },
      onInputFocus(){
        this.loadCalendar();
        this.calendarVisible = true;
      },
      showYearList(){
        //公式：A ＝ 默认当前年所在页面的起始年；S ＝ 选择日期所在年份；Z(pagesize) = 12; Math.ceil : 向上入，有小数就向上＋1；
        // result = A - Math.ceil( (A-S) / Z ) * Z;
        let A = this.todayYear + parseInt(this.laterCurrentYears) - 11;
        this.startYear = A - Math.ceil((A - this.year )/12) * 12;

        this.yearVisible = true;
      },
      goYearPrev(){
        if(this.startYear<1912) return;
        this.startYear -= 12;
      },
      goYearNext(){
        if(this.startYear>=this.todayYear + parseInt(this.laterCurrentYears) - 11) return;
        this.startYear += 12;
      },
      goPrev () {
        var newMonth = moment(this.monthDay).subtract(1, 'months');
        this.monthDay = newMonth.toISOString();
        this.loadCalendar()
      },
      goNext () {
        var newMonth = moment(this.monthDay).add(1, 'months');
        this.monthDay = newMonth.toISOString();
        this.loadCalendar()
      },
      onDateSelect(day){
        let result = day.date;
        if (this.isLunar) {
          let tip = 'L' + (day.lDate.isLeap ? 'R' : '');
          let date = new Date(day.lDate.lYear + '-' + day.lDate.lMonth + '-' + day.lDate.lDay).toISOString();
          result = tip + moment(date).format("YYYY-MM-DD")
        }
        if(!day.isCurrMonth){
            this.monthDay = new Date(day.date).toISOString();
            this.loadCalendar();
        }
        this.calendarVisible = false;
        this.$emit('input', result);
      },
      onMonthSelect(command){
        let newMonth = moment(this.monthDay).month(command)
        this.monthDay = newMonth.toISOString();
        this.loadCalendar();
        this.monthVisible = false;
      },
      onYearSelect(command){
        let newMonth = moment(this.monthDay).year(command)
        this.monthDay = newMonth.toISOString();
        this.yearVisible = false;
        this.monthVisible = true;
      },
      loadCalendar () {
        if(this.monthDay==null || this.monthDay == ''){
            this.monthDay = moment().toISOString();
        }

        let startDate = moment(this.monthDay).startOf('month').format('YYYY-MM-DD');
        let currentMonth = moment(startDate);

        this.year = currentMonth.year();
        this.month = currentMonth.month() + 1;


        let start = currentMonth.clone()
        start.subtract(start.day(), 'd')
        let calendars = [], calendars2 = [];

        for (let iWeek = 0; iWeek < 6; iWeek++) {
          let week = []
          if (iWeek > 0 && !start.isSame(currentMonth, 'month')) {
            break;
          }
          for (let iDay = 0; iDay < 7; iDay++) {
            let d = {
              day: start.date(),
              isToday: start.isSame(new Date(), 'day'),
              isCurMonth: start.isSame(currentMonth, 'month'),
              week: iDay,
              date: start.clone().format('YYYY-MM-DD'),
              lDate: JDatePickerScript.calendar.getLunar(start.toISOString()),
            };

            week.push(d);
            calendars2.push(d);
            start.add(1, 'd')
          }

          calendars.push(week)
        }
        this.calendarDatas = calendars;
        this.calendarDatas2 = calendars2;
      },
    },

  }
</script>
