# vue-jLunar-datePicker
@JinWen

> Lunar-Date-Picker component,lightWeight,powerful，easy use,with festival and solar terms.


![demo](./static/DEMO.png)


### Getting Start

---


**New Update for 2.0**
1、add a new Property : 
  type : DATE/DATETANGE (you can pick a period of time by use  DATETANGE option)
2、fixed bugs  

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
	<j-date-picker v-model="value1":width="width1" :type="type" :showLunarClass="showLunarClass" :lunarDefault="lunarDefault" :laterCurrentYears="laterCurrentYears" :showLunarIcon="showLunarIcon" :format="format"></j-date-picker>
</template>
<script>
export default{
  data(){
    return{
      data:{
        value1:'',
        type:'DATE',
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

| Properties      | Description                      | Type      |  Optional value       | Default value         |
| :---------------- | :--------------------------------------- | :------      | :------------ | :------------ |
| value             | bind-value(v-model)                      | String,Date  | －－          | －－            |
| width             | width                                      | String      | －－            | 200px/200         |
| type              |you can pick a day or pick a period of time | String     | DATE/DATETANGE      | DATE         |
| showLunarClass    | The display type of a lunar date; case insensitive;      | String  | FULLLUNAR/LUNAR/NUMBER/MIX|  NUMBER|
| showLunarIcon       | whether to show lunar icon           | Boolean       | true/false     | false        |
| laterCurrentYears   | the years number after now  based on the current year | Number     | －－            | 2            |
| format           | format Date | String       | －－            | YYYY-MM-DD   |
| lunarDefault      | whether to show the lunar calendar by default  | Boolean     | true/false      | false         |

**GitHub**
https://github.com/tuhe32/vue-jLunar-datePicker

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
