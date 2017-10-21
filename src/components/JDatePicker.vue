<template>
  <div class="full-jcalendar" :style="{width:handleWidth+'px'}">

    <p class="input-icon__tip" v-show="isLunar && showLunarIcon">
      <i v-if="isLeap">闰</i>
      <i v-else>农</i>
    </p>
    <p class="input-icon" @mouseover="onInputOver" @mouseout="onInputOut">
      <i class="iconfont icon-time" style="display: none"></i>
      <i class="iconfont icon-richeng" v-show="!clearVisible"></i>
      <i class="iconfont icon-guanbi is-clear" v-show="clearable && clearVisible" @click="onClearInput"></i>
    </p>

    <input type="text" :value="inputText" :class="['input__inner',{'is-disabled':disabled}]" :placeholder="placeholder" @click="onInputFocus" :readonly="!editable || type.toUpperCase() ==='DATERANGE' || (isLunar && showLunarClass.toUpperCase()!='NUMBER')" :disabled="disabled" @input="inputText = $event.target.value" @change="handleInputChange" />

    <!--日期控件弹窗主体-->
    <div class="full-jcalendar__main" :class="{'is-daterange':type.toUpperCase() === 'DATERANGE'}" v-show="calendarVisible">
      <!--单日期模式-->
      <div v-show="type.toUpperCase() === 'DATE'">

        <div class="full-jcalendar-header">
          <span class="title-year" @click="showYearList">{{year}}年</span>
          <span style="width:10px;"></span>
          <span class="title-month" @click="monthVisible = true">{{month}}月</span>
          <span style="width:10px;" v-if="showLunarControl"></span>
          <label v-if="showLunarControl"><input type="checkbox" v-model="isLunar" @change="onLunarChange" />农历</label>
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
                 :class="[{'today': day.isToday,'not-optional':isNotOptional(day),'select':day.isSelect,'not-cur-month': !day.isCurMonth }]"
                 @click="onDateSelect(day)">
              <!-- 日期 节气 农历 -->
              <div class="day-number">
                <p class="solar">
                  <!--<span class="is-leap" v-if="day.lDate.isLeap && day.lDate.lMonth>10 &&day.lDate.IDayCn==='初一'">闰</span>-->
                  <span class="is-leap" v-if="day.lDate.isLeap && day.lDate.IDayCn==='初一'">闰</span>
                  <span :class="['number',{'is-today':day.isToday,'is-empty':day.isToday && !day.lDate.isFestival && !day.lDate.isTerm}]">{{day.isToday?'今天':day.day}}</span>
                </p>
                <p class="lunar" v-show="!day.isToday || day.lDate.isFestival || day.lDate.isTerm">
                  <span class="festival" v-if="day.lDate.isFestival">{{day.lDate.festival[0]}}</span>
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
            <p class="prev-month" @click.stop="goYearPrevList"><i class="icon iconfont icon-xiangzuojiantou"></i></p>
            <p class="next-month" @click.stop="goYearNextList"><i class="icon iconfont icon-xiangyoujiantou"></i></p>
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

      <!--日期段模式-->
      <div class="date-range__month leftMonth" v-if="type.toUpperCase() === 'DATERANGE'">
        <div class="full-jcalendar-header">
          <span class="title-year">{{year}}年</span>
          <span style="width:10px;"></span>
          <span class="title-month">{{month}}月</span>
          <span style="width:10px;" v-if="showLunarControl"></span>
          <label v-if="showLunarControl"><input type="checkbox" v-model="isLunar" @change="onLunarChange" />农历</label>
          <!-- header 切换月份 -->
          <p class="prev-year" @click.stop="goYearPrev"><i class="icon iconfont icon-xiangzuojiantou"></i></p>
          <p class="prev-month" @click.stop="goPrev"><i class="icon iconfont icon-xiangzuojiantou"></i></p>
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
          <div class="week-row" v-for="week in calendarDatas" @mouseout="onMouseOut">
            <!--日期 日  单元格-->
            <div class="day-cell" v-for="day in week"
                 :class="[{'today': day.isToday,'not-optional':isNotOptional(day),'choose':isChoose(day),'select':isSelect(day),'not-cur-month': !day.isCurMonth }]"
                 @click="onDateSelect(day)" @mouseover="onMouseOver(day)">
              <!-- 日期 节气 农历 -->
              <div class="day-number">
                <p class="solar">
                  <!--<span class="is-leap" v-if="day.lDate.isLeap && day.lDate.lMonth>10 &&day.lDate.IDayCn==='初一'">闰</span>-->
                  <span class="is-leap" v-if="day.lDate.isLeap && day.lDate.IDayCn==='初一'">闰</span>
                  <span :class="['number',{'is-today':day.isToday,'is-empty':day.isToday && !day.lDate.isFestival && !day.lDate.isTerm}]">{{day.isToday?'今天':day.day}}</span>
                </p>
                <p class="lunar" v-show="!day.isToday || day.lDate.isFestival || day.lDate.isTerm">
                  <span class="festival" v-if="day.lDate.isFestival">{{day.lDate.festival[0]}}</span>
                  <span class="term" v-else-if="day.lDate.isTerm">{{day.lDate.Term}}</span>
                  <!--<span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.isLeap && day.lDate.lMonth>10?day.lDate.IMonthCn.replace('闰',''):day.lDate.IMonthCn}}</span>-->
                  <span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.IMonthCn.replace('闰','')}}</span>
                  <span v-else>{{day.lDate.IDayCn}}</span>
                </p>
              </div>

            </div>

          </div>
        </div>
      </div>
      <div class="date-range__month" v-if="type.toUpperCase() === 'DATERANGE'">

        <div class="full-jcalendar-header">
          <span class="title-year">{{year2}}年</span>
          <span style="width:10px;"></span>
          <span class="title-month">{{month2}}月</span>
          <!-- header 切换月份 -->
          <p class="next-year" @click.stop="goYearNext"><i class="icon iconfont icon-xiangyoujiantou"></i></p>
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
          <div class="week-row" v-for="week in calendarDatas2" @mouseout="onMouseOut">
            <!--日期 日  单元格-->
            <div class="day-cell" v-for="day in week"
                 :class="[{'today': day.isToday,'not-optional':isNotOptional(day),'choose':isChoose(day),'select':isSelect(day),'not-cur-month': !day.isCurMonth }]"
                 @click="onDateSelect(day)" @mouseover="onMouseOver(day);">
              <!-- 日期 节气 农历 -->
              <div class="day-number">
                <p class="solar">
                  <!--<span class="is-leap" v-if="day.lDate.isLeap && day.lDate.lMonth>10 &&day.lDate.IDayCn==='初一'">闰</span>-->
                  <span class="is-leap" v-if="day.lDate.isLeap && day.lDate.IDayCn==='初一'">闰</span>
                  <span :class="['number',{'is-today':day.isToday,'is-empty':day.isToday && !day.lDate.isFestival && !day.lDate.isTerm}]">{{day.isToday?'今天':day.day}}</span>
                </p>
                <p class="lunar" v-show="!day.isToday || day.lDate.isFestival || day.lDate.isTerm">
                  <span class="festival" v-if="day.lDate.isFestival">{{day.lDate.festival[0]}}</span>
                  <span class="term" v-else-if="day.lDate.isTerm">{{day.lDate.Term}}</span>
                  <!--<span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.isLeap && day.lDate.lMonth>10?day.lDate.IMonthCn.replace('闰',''):day.lDate.IMonthCn}}</span>-->
                  <span v-else-if="day.lDate.IDayCn==='初一'">{{day.lDate.IMonthCn.replace('闰','')}}</span>
                  <span v-else>{{day.lDate.IDayCn}}</span>
                </p>
              </div>

            </div>

          </div>
        </div>

      </div>

      <div class="clearfix"></div>
    </div>

  </div>
</template>
<script>
  import moment from 'moment';
  import '../theme/public.css'
  import JDatePickerScript from './jDatePicker.js'

  export default {
    props: {
      width: {
        type: String,
        default: '200',
      },
      value: {
        type:[String,Date,Array],
        default: ''
      },
      placeholder:{
        type: String,
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
      showBackYears:{
        type: Number,
        default: 2,
      },
      showLunarControl:{
        type: Boolean,
        default: true,
      },
      disabled:{
        type: Boolean,
        default: false,
      },
      editable:{
        type: Boolean,
        default: true,
      },
      clearable:{
        type: Boolean,
        default: true,
      },
      type:{
        type: String,
        default: "DATE",//DATE - 日期类型；DATERANGE － 时间段；
      },
      rangeSeparator:{
        type: String,
        default: "-",//时间段分隔符
      },
      pickerOptions:{
        type:Object,
        default:null
      },
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
    watch: {
      'value': function (newValue, oldValue) {
        this.inputText = '';
        this.isLeap = false;
        this.isLunar = false;

        this.handleInputText(newValue);

      },
      'showLunarIcon': function (newValue, oldValue) {
      },
    },
    data () {
      return {
        inputText:'',
        startYear:0,//年份列表的起始年份
        monthDay: '',//月份日期类型，用来比较日期
        selectDay:[],//type=daterange,存放点击选中的时间值；
        chooseDay:[],//type=daterange,存放鼠标移动所选时间段；
        year: 0,
        month: 0,
        year2: 0,
        month2: 0,
        isCurrMonth: true,
        calendarDatas: [],
        calendarDatas2: [],
        calendarVisible: false,//日历显示开关
        yearVisible: false,//年份选择开关
        monthVisible: false,//月份选择开关
        clearVisible:false,//清除按钮开关
        isLunar: false,//是否启用农历
        isLeap: false,//是否闰月
      }
    },
    created () {
      let month = moment()
      this.monthDay = month.toISOString();
      this.todayYear = month.year();
      this.loadCalendar();

      document.addEventListener('click', (e) => {
        let icon = this.$el.querySelector('.input-icon');
        let input = this.$el.querySelector('.input__inner');
        let main = this.$el.querySelector('.full-jcalendar__main');
        if (!icon.contains(e.target) && !input.contains(e.target) && !main.contains(e.target)) {
          this.calendarVisible = false;
          if (this.selectDay.length > 0) {
            this.monthDay = this.selectDay[0];
          }
        }
      });

      this.handleInputText(this.value,true);
    },
    methods: {
      handleInputText(newValue,isNeedEmit){
        if (newValue == undefined || newValue == null || !newValue.toString().length) return;

        let valueDay = '', valueDay2 = '', result = '';

        if (this.type.toUpperCase() == 'DATE') {
          //单日期模式
          newValue = newValue.toString();
          let isLunar  = newValue.indexOf('L') > -1;
          this.isLunar = isLunar;

          if (isLunar) {
            //农历模式
            let showLunarClass = this.showLunarClass.toUpperCase();
            let lunarDate      = JDatePickerScript.calendar.getSolar(newValue);

            valueDay = new Date(lunarDate.cYear + '-' + lunarDate.cMonth + '-' + lunarDate.cDay).toISOString();

            if (showLunarClass == 'NUMBER') {
              result = moment(new Date(lunarDate.lYear + '-' + lunarDate.lMonth + '-' + lunarDate.lDay)).format(this.format.toUpperCase());
            }
            else if (showLunarClass == 'MIX') {
              result = lunarDate.gzYear + '(' + lunarDate.lYear + ')[' + lunarDate.Animal + ']年 ' + lunarDate.IMonthCn + lunarDate.IDayCn;
            }
            else if (showLunarClass == 'FULLLUNAR') {
              result = lunarDate.gzYear + '[' + lunarDate.Animal + ']年 ' + lunarDate.gzMonth + '月 ' + lunarDate.gzDay + '日';
            }
            else if (showLunarClass == 'LUNAR') {
              result = lunarDate.gzYear + '[' + lunarDate.Animal + ']年 ' + lunarDate.IMonthCn + lunarDate.IDayCn;
            }

          } else {
            //公历模式
            let date = new Date(newValue);
            valueDay = date.toISOString();
            result   = moment(date).format(this.format.toUpperCase());
            this.$emit('input',moment(date).format('YYYY-MM-DD'))
          }
          this.monthDay  = valueDay;
          this.selectDay = [valueDay];
          this.inputText = result;
          this.loadCalendar();
          return;
        }
        if (this.type.toUpperCase() == 'DATERANGE') {
          //时间段模式
          //判断newValue是否为数组；ES2005以上支持isArray
          if (Array.isArray(newValue) && newValue.length == 2) {
            let beginDt = newValue[0];
            let endDt = newValue[1];
            //判断value是否为日期类型
            if(beginDt == undefined || beginDt == null || !beginDt.toString().length) return;
            if(endDt == undefined || endDt == null || !endDt.toString().length) return;
            beginDt = beginDt.toString();
            endDt = endDt.toString();

            let isLunar  = beginDt.indexOf('L') > -1;
            this.isLunar = isLunar;
            if (isLunar) {
              //农历模式
              let beginDateResult = '', endDateResult = '';
              let showLunarClass  = this.showLunarClass.toUpperCase();

              let beginLunarDate = JDatePickerScript.calendar.getSolar(beginDt);
              let endLunarDate  = JDatePickerScript.calendar.getSolar(endDt);

              valueDay  = new Date(beginLunarDate.cYear + '-' + beginLunarDate.cMonth + '-' + beginLunarDate.cDay).toISOString();
              valueDay2 = new Date(endLunarDate.cYear + '-' + endLunarDate.cMonth + '-' + endLunarDate.cDay).toISOString();

              if (showLunarClass == 'NUMBER') {
                beginDateResult = moment(new Date(beginLunarDate.lYear + '-' + beginLunarDate.lMonth + '-' + beginLunarDate.lDay)).format(this.format.toUpperCase());
                endDateResult   = moment(new Date(endLunarDate.lYear + '-' + endLunarDate.lMonth + '-' + endLunarDate.lDay)).format(this.format.toUpperCase());
              }
              else if (showLunarClass == 'MIX') {
                beginDateResult = beginLunarDate.gzYear + '(' + beginLunarDate.lYear + ')[' + beginLunarDate.Animal + ']年 ' + beginLunarDate.IMonthCn + beginLunarDate.IDayCn;
                endDateResult   = endLunarDate.gzYear + '(' + endLunarDate.lYear + ')[' + endLunarDate.Animal + ']年 ' + endLunarDate.IMonthCn + endLunarDate.IDayCn;
              }
              else if (showLunarClass == 'FULLLUNAR') {
                beginDateResult = beginLunarDate.gzYear + '[' + beginLunarDate.Animal + ']年 ' + beginLunarDate.gzMonth + '月 ' + beginLunarDate.gzDay + '日';
                endDateResult   = endLunarDate.gzYear + '[' + endLunarDate.Animal + ']年 ' + endLunarDate.gzMonth + '月 ' + endLunarDate.gzDay + '日';
              }
              else if (showLunarClass == 'LUNAR') {
                beginDateResult = beginLunarDate.gzYear + '[' + beginLunarDate.Animal + ']年 ' + beginLunarDate.IMonthCn + beginLunarDate.IDayCn;
                endDateResult   = endLunarDate.gzYear + '[' + endLunarDate.Animal + ']年 ' + endLunarDate.IMonthCn + endLunarDate.IDayCn;
              }

              result = beginDateResult + ' ' + this.rangeSeparator + ' ' + endDateResult;

            } else {
              //公历模式
              if(isNeedEmit){
                this.$emit('input',[moment(new Date(beginDt)).format('YYYY-MM-DD'),moment(new Date(endDt)).format('YYYY-MM-DD')]);
                return;
              }

              let beginDate = new Date(beginDt);
              let endDate   = new Date(endDt);
              valueDay      = beginDate.toISOString();
              valueDay2     = endDate.toISOString();
              result        = moment(beginDate).format(this.format.toUpperCase()) + ' ' + this.rangeSeparator + ' ' + moment(endDate).format(this.format.toUpperCase());
            }
            this.monthDay   = valueDay;
            this.selectDay  = [valueDay, valueDay2];
            this.chooseDay  = [valueDay, valueDay2];
            this.inputText  = result;
            this.loadCalendar();
          }
        }
      },
      handleInputChange(e){
        let result = e.target.value;
        if(this.type.toUpperCase() == 'DATE'){
          //单日期
          if(this.isLunar){
            //农历 NUMBER 模式
            if(this.showLunarClass.toUpperCase() == 'NUMBER') {
              let regex = /\d{4}[-\.\/]\d{1,2}[-\.\/]\d{1,2}/;
              if (!regex.test(result)) return;
              result = result.replace(/\./g, '-').replace(/\//g, '-');
              result = 'L' + moment(new Date(result)).format('YYYY-MM-DD');
            }
          }else{
            let regex = /\d{4}[-\.\/]\d{1,2}[-\.\/]\d{1,2}/;
            if (!regex.test(result)) return;
            result = result.replace(/\./g, '-').replace(/\//g, '-');
            result = moment(new Date(result)).format('YYYY-MM-DD');
          }
        }
        this.$emit('change',result);
        this.$emit('input',result);

      },
      onClearInput(){
        if(this.type.toUpperCase() == 'DATERANGE'){
          this.$emit('change',[]);
          this.$emit('input', []);
        }else {
          this.$emit('change', '');
          this.$emit('input',  '');
        }
        this.selectDay = [];
        this.chooseDay = [];
        this.monthDay = moment().toISOString();
        this.clearVisible = false;
      },
      onInputOver(){
        if(this.disabled) return;
        if(this.value ==null || this.value == '')return;
        this.clearVisible = true;
      },
      onInputOut(){
        this.clearVisible = false;
      },
      isNotOptional(day){
        if (this.pickerOptions != null && this.pickerOptions != undefined && 'disabledDate' in this.pickerOptions) {
          return this.pickerOptions.disabledDate(new Date(day.date));
        }
        return false;
      },
      isChoose(day){
        //是否在选中时间段区域内
        if(this.type.toUpperCase() == 'DATERANGE') {
          if(this.chooseDay.length == 2){
            let currDay = moment(day.date);
            return currDay.isSameOrAfter(this.chooseDay[0]) & currDay.isSameOrBefore(this.chooseDay[1]) & day.isCurMonth;
          }
        }
        return false;
      },
      isSelect(day){
        return this.chooseDay.length>0 & moment(day.date).isSame(new Date(this.chooseDay[0]),'day') & day.isCurMonth;
      },
      onInputFocus(){
        if(this.disabled) return;
        this.loadCalendar();
        this.calendarVisible = true;
      },
      onLunarChange(){
        if (this.value != null && this.value != '' && this.value.length > 0) {
          if (this.type.toUpperCase() == 'DATERANGE') {
            //时间段模式
            if (this.isLunar) {
              //由公历到农历
              //判断日期结果集中日期格式是否为农历，若是不作处理直接返回。
              if(this.value[0].indexOf('L')>-1) return;
              let beginDay       = JDatePickerScript.calendar.getLunar(this.value[0].replace(/L/,'').replace(/R/,''));
              let endDay         = JDatePickerScript.calendar.getLunar(this.value[1].replace(/L/,'').replace(/R/,''));
              let beginDayResult = 'L' + (beginDay.isLeap ? 'R' : '') + moment(new Date(beginDay.lYear + '-' + beginDay.lMonth + '-' + beginDay.lDay)).format('YYYY-MM-DD');
              let endDayResult   = 'L' + (endDay.isLeap ? 'R' : '') + moment(new Date(endDay.lYear + '-' + endDay.lMonth + '-' + endDay.lDay)).format('YYYY-MM-DD');

              this.$emit('change',[beginDayResult,endDayResult]);
              this.$emit('input' ,[beginDayResult,endDayResult]);
            } else {
              //由农历到公历
              //判断日期结果集中日期格式是否为农历，若不是不作处理直接返回。
              if(this.value[0].indexOf('L') == -1) return;
              let beginDay       = JDatePickerScript.calendar.getSolar(this.value[0]);
              let endDay         = JDatePickerScript.calendar.getSolar(this.value[1]);
              let beginDayResult = moment(new Date(beginDay.cYear + '-' + beginDay.cMonth + '-' + beginDay.cDay)).format('YYYY-MM-DD');
              let endDayResult   = moment(new Date(endDay.cYear + '-' + endDay.cMonth + '-' + endDay.cDay)).format('YYYY-MM-DD');

              this.$emit('change',[beginDayResult,endDayResult]);
              this.$emit('input' ,[beginDayResult,endDayResult]);
            }
          } else {
            //单日期模式
            let day,result = '';
            if (this.isLunar) {
              //由公历到农历
              //判断日期结果中日期格式是否为农历，若是不作处理直接返回。
              if(this.value.indexOf('L')>-1) return;
              day = JDatePickerScript.calendar.getLunar(this.value.replace(/L/,'').replace(/R/,''));
              result = 'L' + (day.isLeap ? 'R' : '') + moment(new Date(day.lYear + '-' + day.lMonth + '-' + day.lDay)).format('YYYY-MM-DD');
              this.$emit('change', result);
              this.$emit('input' , result);
            } else {
              //由农历到公历
              //判断日期结果中日期格式是否为农历，若不是不作处理直接返回。
              if(this.value.indexOf('L') == -1) return;
              day = JDatePickerScript.calendar.getSolar(this.value);
              result = moment(new Date(day.cYear + '-' + day.cMonth + '-' + day.cDay)).format('YYYY-MM-DD');
              this.$emit('change', new Date(result));
              this.$emit('input' , new Date(result));
            }
          }

        }
      },
      showYearList(){
        //公式：A ＝ 默认当前年所在页面的起始年；S ＝ 选择日期所在年份；Z(pagesize) = 12; Math.ceil : 向上入，有小数就向上＋1；
        // result = A - Math.ceil( (A-S) / Z ) * Z;
        let A = this.todayYear + parseInt(this.showBackYears) - 11;
        this.startYear = A - Math.ceil((A - this.year )/12) * 12;

        this.yearVisible = true;
      },
      goYearPrevList(){
        if(this.startYear<1912) return;
        this.startYear -= 12;
      },
      goYearNextList(){
        if(this.startYear>=this.todayYear + parseInt(this.showBackYears) - 11) return;
        this.startYear += 12;
      },
      goYearPrev(){
        this.monthDay = moment(this.monthDay).subtract(1,'years').toISOString();
        this.loadCalendar();
      },
      goYearNext(){
        this.monthDay = moment(this.monthDay).add(1,'years').toISOString();
        this.loadCalendar();
      },
      goPrev () {
        let newMonth = moment(this.monthDay).subtract(1, 'months');
        this.monthDay = newMonth.toISOString();
        this.loadCalendar()
      },
      goNext () {
        let newMonth = moment(this.monthDay).add(1, 'months');
        this.monthDay = newMonth.toISOString();
        this.loadCalendar()
      },
      onMouseOver(day){
        if (this.selectDay.length == 1) {
          let currDay = moment(day.date);
          let beginDt = this.selectDay[0];
          if (currDay.isAfter(beginDt)) {
            this.chooseDay = [beginDt, currDay.toISOString()];
          }
        }
      },
      onMouseOut(){
        if (this.selectDay.length < 2) {
          this.chooseDay = [];
        }
      },
      onDateSelect(day){
        if(this.isNotOptional(day)) return;
        let dayDate = new Date(day.date).toISOString();
        if(this.type.toUpperCase() == 'DATERANGE') {
          if (this.selectDay.length >= 2) {
            this.selectDay = [];
            this.chooseDay = [];
          }
          //判断日期；1、第一次选中，直接加入结果集为时间段起始日期；
          // 2、小于起始日期，则重置结果集，所选日期为结果集起始日期；
          // 3、大于等于起始日期，加入结果集为时间段结束日期；可返回结果。
          if (this.selectDay.length > 0) {
            if (moment(dayDate).isSameOrAfter(this.selectDay[0])) {
              this.selectDay.push(dayDate);
            }else{
                this.selectDay = [dayDate];
            }
          }else {
            this.selectDay.push(dayDate);
          }
          //校正日历控件展示月份到起始月份或当前月份
          //this.monthDay = new Date(this.selectDay[0]).toISOString();
          //判断结果集是否符合条件，数组selectDay.length＝＝2；返回结果；隐藏控件；
          if (this.selectDay.length == 2) {
            if(this.isLunar) {
              let beginLunarDateResult = '', endLunarDateResult = '';
              let beginLunar = JDatePickerScript.calendar.getLunar(this.selectDay[0]);
              beginLunarDateResult = 'L' + (beginLunar.isLeap ? 'R' : '') + moment(new Date(beginLunar.lYear + '-' + beginLunar.lMonth + '-' + beginLunar.lDay)).format("YYYY-MM-DD");
              endLunarDateResult = 'L' + (day.lDate.isLeap ? 'R' : '') + moment(new Date(day.lDate.lYear + '-' + day.lDate.lMonth + '-' + day.lDate.lDay)).format("YYYY-MM-DD");
              this.$emit('change', [beginLunarDateResult, endLunarDateResult])
              this.$emit('input' , [beginLunarDateResult, endLunarDateResult])
            }else{
              this.$emit('change', [moment(this.selectDay[0]).format('YYYY-MM-DD'), moment(this.selectDay[1]).format('YYYY-MM-DD')])
              this.$emit('input' , [moment(this.selectDay[0]).format('YYYY-MM-DD'), moment(this.selectDay[1]).format('YYYY-MM-DD')])
            }
            this.calendarVisible = false;
          }
        }else {
          let result = day.date;
          if (this.isLunar) {
            let tip = 'L' + (day.lDate.isLeap ? 'R' : '');
            let date = new Date(day.lDate.lYear + '-' + day.lDate.lMonth + '-' + day.lDate.lDay).toISOString();
            result = tip + moment(date).format("YYYY-MM-DD")
          }
          this.monthDay = dayDate;
          this.calendarVisible = false;
          this.$emit('change', result);
          this.$emit('input' , result);
        }
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
        this.calendarDatas = this.handleCalendar(currentMonth);

        if(this.type.toUpperCase() == 'DATERANGE'){
            //时间段模式
          let currentMonth2 = currentMonth.clone().add(1,'months');
          this.year2 = currentMonth2.year();
          this.month2 = currentMonth2.month() + 1;
          this.calendarDatas2 = this.handleCalendar(currentMonth2);
        }
      },
      handleCalendar(currentMonth){

        let start = currentMonth.clone()
        start.subtract(start.day(), 'd')
        let calendars = [];

        for (let iWeek = 0; iWeek < 6; iWeek++) {
          let week = []
          if (iWeek > 0 && !start.isSame(currentMonth, 'month')) {
            break;
          }
          for (let iDay = 0; iDay < 7; iDay++) {
            week.push({
              day: start.date(),
              isToday: start.isSame(new Date(), 'day'),
              isSelect: this.selectDay.length>0 && start.isSame(new Date(this.selectDay[0]),'day'),
              isCurMonth: start.isSame(currentMonth, 'month'),
              weekIndex:iWeek,
              week: iDay,
              date: start.clone().format('YYYY-MM-DD'),
              lDate: JDatePickerScript.calendar.getLunar(start.toISOString()),
            });
            start.add(1, 'd')
          }
          calendars.push(week)
        }

        return calendars;
      }
    },

  }
</script>

