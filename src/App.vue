<template>
  <div id="app">
    <label class="tt">请选择日期: </label>
    <j-date-picker v-model="value1":width="width1" :showLunarClass="showLunarClass" :isLunarDefault="isLunarDefault" :type="type" :laterCurrentYears="laterCurrentYears" :showLunarIcon="showLunarIcon" :format="format"></j-date-picker>
    <p class="tt" style="padding-left:10px;">选择的日期是：{{ value1 }}</p>
    <hr>
    <p>返回结果：公历：2017-01-01；农历：L2017-01-01；农历闰月：LR2017-06-01; 日期段(公历)：['2017-01-02','2017.02.01'];日期段(农历)：['L2017-01-02','L2017.02.01'];</p>
    <hr>
    <div class="param">
      <h4>设置后需要重新选择日期才能看见更新效果</h4>
      <label>宽度：</label><input type="text" v-model="width1"  /><br>

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

      <label>是否默认启用农历：</label><input type="checkbox" v-model="isLunarDefault"  /><br>

      <label>是否显示农历标记：</label><input type="checkbox" v-model="showLunarIcon"  /><br>

      <label>向后显示年数：</label><input type="number" v-model="laterCurrentYears"  /><br>

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
          components:{ JDatePicker },<br>
        }
      </p>
      <p>HTML:</p>
      <p class="js">
       < j-date-picker v-model="value1" width="300" showLunarClass="fulllunar" :laterCurrentYears="5" isLunar showLunarIcon format="yyyy.mm.dd">< /j-date-picker>
      </p>
      <h5>参数：</h5>
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
          <td>width</td>
          <td>宽度</td>
          <td>String</td>
          <td>-</td>
          <td>200px/200</td>
        </tr>
        <tr>
          <td>showLunarClass</td>
          <td>农历日期的展示类型；<br>不区分大小写；</td>
          <td>String</td>
          <td>
            FULLLUNAR(全农历)<br>LUNAR(农历)<br>NUMBER(数字)<br>MIX(混合)<br>
          </td>
          <td>NUMBER</td>
        </tr>
        <tr>
          <td>showLunarIcon</td>
          <td>是否显示农历标记</td>
          <td>Boolean</td>
          <td>true/false</td>
          <td>false</td>
        </tr>
        <tr>
          <td>laterCurrentYears</td>
          <td>当前年向后展示年数;<br>见于年份列表;<br>时间段模式无效</td>
          <td>Number</td>
          <td>－</td>
          <td>2</td>
        </tr>
        <tr>
          <td>format</td>
          <td>显示格式化;<br>农历仅适用于数字模式；<br>不区分大小写；</td>
          <td>String</td>
          <td>－</td>
          <td>YYYY-MM-DD</td>
        </tr>
        <tr>
          <td>isLunarDefault</td>
          <td>是否默认启用农历</td>
          <td>Boolean</td>
          <td>true<br>false</td>
          <td>false</td>
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
        value1:'',
        type:'DATE',
        showLunarClass:'NUMBER',
        laterCurrentYears:2,
        showLunarIcon:true,
        isLunarDefault:false,
        width1:'600',
        format:'YYYY.MM.DD',
      }
    },
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
