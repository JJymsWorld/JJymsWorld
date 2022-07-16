---
title: 解决React中修改value不能触发input事件
tags:
  - [JavaScript, React]
categories:
  - [gallery]
date: 2022-07-16 16:55:31
---

解决方法，见如下代码：

原例：

```javascript
let input = someInput; 
let lastValue = input.value;
input.value = 'new value';
let event = new Event('input', { bubbles: true });
// hack React15
event.simulated = true;
// hack React16 内部定义了descriptor拦截value，此处重置状态
let tracker = input._valueTracker;
if (tracker) {
  tracker.setValue(lastValue);
}
input.dispatchEvent(event);
```

js操作示例：

```javascript
var test = document.querySelectorAll("input.m-addr-input ")[0];
test.value = "蒋毅"
var event = new Event('input', {bubbles: true});
var tracker = test._valueTracker;
if(tracker){
    tracker.setValue('');
}
test.dispatchEvent(event);
```

[原始资料地址](https://github.com/facebook/react/issues/11488#issuecomment-347775628)

