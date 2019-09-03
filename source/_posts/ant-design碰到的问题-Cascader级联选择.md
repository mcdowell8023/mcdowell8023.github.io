---
title: 'ant-design碰到的问题:Cascader级联选择'
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - ant-design
  - 记录
date: 2019-09-03 19:33:33
---

# Cascader 级联选择 遇到的问题

## 交代说明

公司开发后台管理系统，涉及一个地级区域选择的功能，实现效果如下：
![nAmux1.png](https://s2.ax1x.com/2019/09/03/nAmux1.png)

然后，后台给的接口是根据参数查询省，然后市...,如下：
![nAmsIg.png](https://s2.ax1x.com/2019/09/03/nAmsIg.png)

## 查看官网文档

通过官网示例，了解到 采用 动态加载选项 示例的方式，使用 loadData 实现动态加载选项。
按照官网的介绍，结合实际接口，我们需要 每次动态加载 根据返回 数据进行 数据拼接。

## 编码

- 当然后台数据返回的字段与组件预制不同，我们可以借助 fieldNames 属性进行自定义字段。
- 编码 测试组件 时候，你会发现点击第一层的数据就会直接选中。查阅属性说明和示例后发现，数据中 **isLeaf: false** 可以控制是否选中，从而触发 loadData
- 然后我们就愉快的编码

1. 点击非 type 是 3 的一层，进行接口调用

```js
// loadData 事件 函数 查询数据
const cascaderLoadData = selectedOptions => {
  // 点击得到的 一级 或 二级 数据
  const targetOption = selectedOptions[selectedOptions.length - 1]
  const { id, type } = targetOption
  targetOption.loading = true
  const newType = type + 1
  if (type !== 3) {
    // 一级 或 二级 数据
    // 这里是调用 查询地区接口的函数
    getDict({
      [newType === 2 ? 'regionId' : 'cityId']: id,
      webId: id,
      type: newType
    })
  }
}
```

2. 数据拼接,上代码：

```js
  // 这里是 得到后台返回数据 在 reducers 的 数据处理
  saveVPNAddressDict(state, action) {
    const {
      params: { type, webId },
      res,
    } = action.payload;
    const { VPNAddressDict } = state;

    const pushList = (arr, typeNum) => {
      return arr
        ? arr
            .map(item => {
              if (!item) {
                return null;
              }
              return {
                ...item,
                loading: false,
                isLeaf: typeNum === 3,
                children: null,
              };
            })
            .filter(item => item)
        : [];
    };

    let dicts = [];
    const dict = pushList(res, type);
    // 基本思路就是： 根据type 确定层级 当前数据该是第几层  根据 webId 找到应该对应的 数据对象
    switch (type) {
      case 1:
        // 查询省数据
        dicts = [...dict];
        break;
      case 2:
        // 查询市数据
        dicts = VPNAddressDict.map(item => {
          if (item.id === webId) {
            return {
              ...item,
              loading: false,
              children: [...dict],
            };
          }
          return item;
        });
        break;
      case 3:
        // 运营商数据
        dicts = VPNAddressDict.map(items => {
          const list = items && items.children ? items.children : [];
          const children = list.map(item => {
            if (item.id === webId) {
              return {
                ...item,
                loading: false,
                children: [...dict],
              };
            }
            return item;
          });
          return {
            ...items,
            loading: false,
            isLeaf: false,
            children,
          };
        });
        break;
      default:
        dicts = [];
        break;
    }
    return {
      ...state,
      VPNAddressDict: dicts,
    };
```

3.
