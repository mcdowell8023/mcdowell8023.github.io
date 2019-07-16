---
title: 关于
description: 个人简介
layout: about
comments: false
sidebar: custom
---

```js
// init
let me = null
const timeAxis = ['1991', '2013', '2016', '2017', 'today']
const person = function(name, sex) {
  return {
    name: name,
    sex: sex
  }
}
// then
timeAxis.map(year => {
  if (year === '1991') {
    me = new person('mcdowell', 'male')
  }
  if (year === '2013') {
    me.college = '邯郸学院'
  }
  if (year === '2016') {
    me.job = 'coder'
    me.skills = ['html', 'css', 'js', 'JQ']
  }
  if (year === '2017') {
    me.skills = [...me.skills, 'react', 'vue']
  }
})
// check
console.log(me, '个人简介')
```
