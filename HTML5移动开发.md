# 《HTML5移动开发》

## 第1章 学习移动HTML5、CSS3 和JavaScript API 之前的准备工作
东西多且杂，大部分没有记录必要

## 第2章 升级至HTML5
大部分都是很无趣的基础知识，只挑部分：
* meta相关的东西
* \<style>上的scoped属性
* \<style>反模式

## 第3章 HTML5中的新元素
介绍了HTML5里面的元素

## 第4章 HTML5 Web 表单

表单元素自带认证（聊胜于无）：
* elment.validity.valueMissing：是设置了required 属性，没有设置任何值，那么该valueMissing 属性就是true，否则就是false
* element.validity.typeMismatch：如果元素的值不是按照正确的语法就返回true，否则返回false。
* element.validity.patternMismatch：如果一个表单控件要求一个特定的模式，也就是与使用pattern 属性设置的那样，而表单控件的值又不匹配该模式。
* element.validity.tooLong：设置了maxlength 属性的情况下，如果该元素的值超过了规定的最大长度就返回true，否则返回false
* element.validity.rangeUnderflow：设置了min 属性情况下强制执行表单控件的最小值，则rangeUnderflow 将返回true
* element.validity.rangeOverflow：设置了max 属性的情况下强制执行表单控件的最大值。
* element.validity.stepMismatch：如果该元素的值不匹配由step 属性要求的规则时返回true，否则返回false。
* element.validity.valid：如果一个表单控件没有有效性问题，validityState 对象的valid 属性将返回true，否则将返回false。
* element.validity.customError：和setCustomValidity("")这个api有关

错误提醒气泡的伪元素：
* ::-webkit-validation-bubble
* ::-webkit-validation-bubble-arrow-clipper
* ::-webkit-validation-bubble-arrow
* ::-webkit-validation-bubble-message

