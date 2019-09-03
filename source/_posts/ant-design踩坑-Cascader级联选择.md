---
title: 'ant-design踩坑：Cascader级联选择'
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - ant-design
  - 踩坑
  - react
date: 2019-09-03 19:33:33
---

# Cascader 级联选择 遇到的问题

## 要点

- 级联选择 数据中 **isLeaf: false** 可以控制是否选中【false 表示不选中】，触发 loadData 函数
- 级联选择 数据中 如果当前层不是最终级【就是你要 id 的那层】，children 一定不能为[],否则会导致组件选中错误层级，不会触发 loadData 函数

## 详细

### 交代说明

#### 公司开发后台管理系统，涉及一个地级区域选择的功能，实现效果如下

![nAmux1.png](https://s2.ax1x.com/2019/09/03/nAmux1.png)

#### 后台给的接口是根据参数查询省，然后市...,如下

![nAmsIg.png](https://s2.ax1x.com/2019/09/03/nAmsIg.png)

### 查看官网文档

通过官网示例，了解到 采用 动态加载选项 示例的方式，使用 loadData 实现动态加载选项。
按照官网的介绍，结合实际接口，我们需要 每次动态修改 根据返回 数据进行 数据拼接。

### 编码

- 当然后台数据返回的字段与组件预制不同，我们可以借助 fieldNames 属性进行自定义字段。
- 编码 测试组件 时候，你会发现点击第一层的数据就会直接选中【我们要的是最后一层，通信商的 id】。查阅属性说明未果。但是通过示例后发现，数据中 **isLeaf: false** 可以控制是否选中，从而触发 loadData。
- 貌似准备就绪。然后，愉快的编码

1. 点击非 type 是 3 的一层，进行接口调用

```js
// Cascader loadData 事件 函数 查询数据
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
  // 这里是 得到后台返回数据 在 reducers 的 数据动态拼接处理
  // 提供 Cascader options
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
                isLeaf: typeNum === 3, // 用于控制非 第三层 不能选
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

3. 然后按照示例使用组件
4. 基本完成后。测试发现，如果你依次点击 【 广东 > 广州 > 移动 】（ 选中最后一层），再次选择 【北京】 组件直接选中了 【北京】（直接选中第一层）。多次测试发现只要不点击最后一层，那么不会出现这个问题。
5. **分析**：根据 bug 出现的情况，可以判断，是执行了 saveVPNAddressDict 动态拼接函数 type= 3 的运营商数据拼接出现的问题。
6. 检查代码未发现明显错误。通过打印数据发现，【北京】数据的 children 是个空数组。 凭借多年开发的直觉[🤦‍ 好吧，我也不知道我是怎么知道的]，推测 children 数据问题。
7. **验证**：自造一条假数据，直接给组件测试使用。数据如下

```js
const options = [
  {
    value: '北京',
    label: '北京',
    isLeaf: false,
    children: []
  },
  {
    value: '广东',
    label: '广东',
    isLeaf: false,
    children: []
  }
]
```

验证结果发现，**children 字段如果是数组，会导致直接选中，不会触发 loadData**。

8.**修正**。那么在函数执行时候，如果没被选中的 children 显示为 null。 修正如下

```js
  // saveVPNAddressDict case 3的代码片段
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
        children:children.length?children:null,
      };
    });
    break;
```

## 总结

- 级联选择 数据中 isLeaf，children 两个字段直接影响 是否被选中，loadData 是否会触发。
