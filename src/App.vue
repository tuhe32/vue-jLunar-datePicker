<template>
  <div id="app">
    <label class="tt">请选择日期: </label>
    <j-date-picker v-model="value1"
                   :width="width1"
                   :placeholder="placeholder"
                   :picker-options="pickerOptions"
                   :rangeSeparator="rangeSeparator"
                   @change="onDateChange"
                   :disabled="disabled"
                   :showLunarClass="showLunarClass"
                   :showLunarControl="showLunarControl"
                   :type="type"
                   :showBackYears="showBackYears"
                   :showLunarIcon="showLunarIcon"
                   :format="format"></j-date-picker>
    <p class="tt" style="padding-left:10px;">选择的日期是：{{ value1 }}</p>
    <hr>
    <p>返回结果：公历：2017-01-01；农历：L2017-01-01；农历闰月：LR2017-06-01; 日期段(公历)：['2017-01-02','2017.02.01'];日期段(农历)：['L2017-01-02','L2017.02.01'];</p>
    <hr>

    <input type="button" @click="onBtnClick" value="点击提交查看结果" />

    <div class="param">
      <h4>设置后需要重新选择日期才能看见更新效果</h4>
      <label>宽度：</label><input type="text" v-model="width1"  /><br>

      <label>文本占位：</label><input type="text" v-model="placeholder"  /><br>

      <label>选择范围时的分隔符：</label><input type="text" v-model="rangeSeparator"  /><br>

      <label>类型：</label>
      <select v-model="type">
        <option value="DATE">DATE</option>
        <option value="DATERANGE">DATERANGE</option>
      </select>
      <br>

      <label>农历显示样式：</label>
      <select v-model="showLunarClass">
        <option value="FULLLUNAR">FULLLUNAR</option>
        <option value="LUNAR">LUNAR</option>
        <option value="NUMBER">NUMBER</option>
        <option value="MIX">MIX</option>
      </select>
      <br>

      <label>是否显示农历控制组件：</label><input type="checkbox" v-model="showLunarControl"  /><br>

      <label>是否显示农历标记：</label><input type="checkbox" v-model="showLunarIcon"  /><br>

      <label>禁用：</label><input type="checkbox" v-model="disabled"  /><br>

      <label>文本框可输入：</label><input type="checkbox" v-model="editable"  /><br>

      <label>是否显示清除按钮：</label><input type="checkbox" v-model="clearable"  /><br>

      <label>向后显示年数：</label><input type="number" v-model="showBackYears"  /><br>

      <label>格式化：</label><input type="text" v-model="format"  /> (不区分大小写)<br>

    </div>
    <div class="api">
      <h3>使用说明</h3>
      <p>引入组件并注册：</p>
      <p>Script:</p>
      <p class="js">
        import JDatePicker from '@/components/JDatePicker.vue'
        <br>
        export default{<br>
          <i style="padding-left:10px;"></i>components:{ JDatePicker },<br>
            <i style="padding-left:20px;"></i>data:{<br>
              <i style="padding-left:30px;"></i>return{<br>
                <i style="padding-left:40px;"></i>pickerOptions: {<br>
                  <i style="padding-left:50px;"></i>disabledDate (time) {<br>
                    <i style="padding-left:60px;"></i>return time.getTime() < Date.now() - 8.64e7;<br>
                  <i style="padding-left:50px;"></i>}<br>
                <i style="padding-left:40px;"></i>}<br>
              <i style="padding-left:30px;"></i>}<br>
          <i style="padding-left:20px;"></i>}<br>
        }
      </p>
      <p>HTML:</p>
      <p class="js">
       < j-date-picker v-model="value1" width="300" show-lunar-class="fulllunar" :picker-options="pickerOptions" :show-back-years="5" :show-lunar-control='showLunarControl' show-lunar-icon format="yyyy.mm.dd">< /j-date-picker>
      </p>

      <h3>参数：</h3>
      <table border="1" cellspacing="0" width="100%">
        <tr>
          <th>参数</th>
          <th>说明</th>
          <th>类型</th>
          <th>可选值</th>
          <th>默认值</th>
        </tr>
        <tr>
          <td>value</td>
          <td>绑定值(v-model);<br>时间段模式为字符串数组</td>
          <td>String,Date,Array</td>
          <td>-</td>
          <td>-</td>
        </tr>
        <tr>
          <td>type</td>
          <td>类型</td>
          <td>String</td>
          <td>DATE(日期)<br>DATETANGE(时间段)</td>
          <td>DATE</td>
        </tr>
        <tr>
          <td>editable</td>
          <td>文本框可输入;农历模式且展示类型为非'NUMBER'时,为false</td>
          <td>Boolean</td>
          <td>true<br>false</td>
          <td>true</td>
        </tr>
        <tr>
          <td>clearable</td>
          <td>是否显示清除按钮</td>
          <td>Boolean</td>
          <td>true<br>false</td>
          <td>true</td>
        </tr>
        <tr>
          <td>disabled</td>
          <td>禁用</td>
          <td>String</td>
          <td>true<br>false</td>
          <td>false</td>
        </tr>
        <tr>
          <td>width</td>
          <td>宽度</td>
          <td>String</td>
          <td>-</td>
          <td>200px/200</td>
        </tr>
        <tr>
          <td>placeholder</td>
          <td>占位内容</td>
          <td>String</td>
          <td> - </td>
          <td> - </td>
        </tr>
        <tr>
          <td>show-lunar-class</td>
          <td>农历日期的展示类型</td>
          <td>String</td>
          <td>
            FULLLUNAR(全农历)<br>LUNAR(农历)<br>NUMBER(数字)<br>MIX(混合)<br>
          </td>
          <td>NUMBER</td>
        </tr>
        <tr>
          <td>show-lunar-icon</td>
          <td>是否显示农历标记</td>
          <td>Boolean</td>
          <td>true<br>false</td>
          <td>false</td>
        </tr>
        <tr>
          <td>show-back-years</td>
          <td>向后展示年数;<br>见于年份列表;<br>时间段模式无效</td>
          <td>Number</td>
          <td>－</td>
          <td>2</td>
        </tr>
        <tr>
          <td>format</td>
          <td>时间日期格式化</td>
          <td>String</td>
          <td>年：YYYY ，月：MM ，日：DD ，时：HH  ，分：MM ，秒：SS</td>
          <td>YYYY-MM-DD</td>
        </tr>
        <tr>
          <td>show-lunar-control</td>
          <td>是否显示农历控制组件</td>
          <td>Boolean</td>
          <td>true<br>false</td>
          <td>true</td>
        </tr>
        <tr>
          <td>range-separator</td>
          <td>选择范围时的分隔符</td>
          <td>String</td>
          <td> - </td>
          <td> '-' </td>
        </tr>
        <tr>
          <td>picker-options</td>
          <td>参考下表</td>
          <td>object</td>
          <td> - </td>
          <td> {} </td>
        </tr>
      </table>

      <h3>picker-options：</h3>
      <table border="1" cellspacing="0" width="100%">
        <tr>
          <th>参数</th>
          <th>说明</th>
          <th>类型</th>
          <th>可选值</th>
          <th>默认值</th>
        </tr>
        <tr>
          <td>disabledDate</td>
          <td>设置禁用状态，参数为当前日期，要求返回 Boolean</td>
          <td>Function</td>
          <td>-</td>
          <td>-</td>
        </tr>
      </table>

      <h3>事件：</h3>
      <table border="1" cellspacing="0" width="100%">
        <tr>
          <th>事件名称</th>
          <th>说明</th>
          <th>回调参数</th>
        </tr>
        <tr>
          <td>change</td>
          <td>当input值发生改变时，返回值与文本框一致</td>
          <td>格式化后的值</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>

  import JDatePicker from '@/components/JDatePicker.vue'

  export default{
    name: 'app',
    components:{ JDatePicker },
    data(){
      return {
        value1:new Date(),
        type:'DATE',
        showLunarClass:'NUMBER',
        showBackYears:2,
        showLunarIcon:true,
        showLunarControl:true,
        width1:'600',
        format:'YYYY.MM.DD',
        placeholder:'请输入内容',
        rangeSeparator:'-',
        disabled:false,
        editable:true,
        clearable:true,
        pickerOptions: {
//          disabledDate (time) {
//            return time.getTime() < Date.now() - 8.64e7;
//          }
        },
      }
    },
    methods:{
      onDateChange(e){
          console.log('change=>',e,'value:',this.value1)
      },
      onBtnClick(){
          alert(this.value1)
      }
    },
    created(){
//        this.value1 = [new Date(),new Date()];
    }
  }
</script>

<style>
  #app{
    margin:50px auto;
    padding-top:30px;
    width:70%;
  }
  #app .tt{ vertical-align: top; line-height: 34px; padding:0 5px;}
  .api{
    padding:20px 0;
    text-align: left;
    line-height: 24px;
    font-size: 14px;
    color:#555;
  }
  .param{
    line-height: 44px;
  }
  .js{
    padding:10px;
    border:1px solid #e8edf0;
    background-color: #ECF1F4;
    border-radius: 5px;
  }
  table{ text-align: center}
</style>
