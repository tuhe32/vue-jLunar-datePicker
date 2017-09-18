# vue-jLunar-datePicker
@金文

> 农历日期Picker组件，功能强大,带有节气，节日功能，易于使用


![demo](./static/DEMO.png)


### Getting Start

---

**Install**

`npm install vue-jlunar-datepicker --save`

**Usage**

main.js

```vue
import Vue from 'vue';
import JDatePicker from 'vue-jlunar-datepicker';

Vue.component("j-date-picker",JDatePicker);
```

test.vue

```vue
<template>
	<j-date-picker v-model="value1":width="width1" :showLunarClass="showLunarClass" :lunarDefault="lunarDefault" :laterCurrentYears="laterCurrentYears" :showLunarIcon="showLunarIcon" :format="format"></j-date-picker>
</template>
<script>
export default{
  data(){
    return{
      data:{
        value1:'',
        showLunarClass:'MIX',
        laterCurrentYears:2,
        showLunarIcon:true,
        lunarDefault:false,
        width1:'300',
        format:'YYYY.MM.DD',
      }
    }
  }
}
</script>
```


### API

---

**Attributes**

| 参数              | 说明                                      | 类型         |  可选值       | 默认值         |
| :---------------- | :--------------------------------------- | :------      | :------------ | :------------ |
| value             | 绑定值(v-model)                           | String,Date  | －－          | －－            |
| width             | 宽度                                      | String      | －－            | 200px/200         |
| showLunarClass    | 农历日期的展示类型；不区分大小写；          | String  | FULLLUNAR(全农历)/LUNAR(农历)/NUMBER(数字)/MIX(混合)|  NUMBER|
| showLunarIcon       | 是否显示农历标记                        | Boolean       | true/false     | false        |
| laterCurrentYears   | 年数；基于当前年向后展示多少年，见于年份列表 | Number     | －－            | 2            |
| format           | 显示格式化；农历仅适用于数字模式；不区分大小写；| String       | －－            | YYYY-MM-DD   |
| lunarDefault      | 是否默认显示农历                            | Boolean     | true/false      | false         |

**GitHub**
https://github.com/tuhe32/vue-jLunar-datePicker

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
