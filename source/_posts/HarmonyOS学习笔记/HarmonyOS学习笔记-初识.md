---
title: HarmonyOS学习笔记-初识
comments: true
mathjax: true
categories:
  - 客户端
tags:
  - 笔记
  - HarmonyOS
  - 拓展
date: 2025-01-16 08:26:17
---

## 1.1 准备

### 1.1.1 前置了解

#### 1.1.1.1 开发套件

> 一套开发多端部署

1. devEcoStudio  统一开发编辑器
2. ArkTs 编程语言 类TypeScript
3. ArkUI： 官方提供的  声明式UI开发框架

> 编辑器汉化 [ 不推荐]
>
> 	settings > plugins > “ installed ” tab 下搜索 chinese , 然后勾选重启 变为 中文

#### 1.1.1.2 UI框架

HarmonyOS提供了一套UI开发框架，即方舟开发框架（ArkUI框架），提供了两种开发范式，分别是基于

**ArkTS的声明式开发范式**（简称“声明式开发范式”）

* 数据驱动更新，使用 **ArkTS语言** 适用于 复杂度较大、系统应用（基于ArkUI进行页面构造布局，并用封装属性来进行页面渲染和布局优化）

**兼容JS的类Web开发范式**（简称“类Web开发范式”）

* 数据驱动更新， 使用 **JS语言** 来开发 界面较为简单的程序应用和卡片，适用于Web前端开发人员 （对于web开发人员只是一个新的框架）

更多UI框架的开发内容及指导，详见[UI开发](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/arkui-overview-V5)。

#### 1.1.1.3 应用模型

‍

> 应用模型是HarmonyOS为开发者提供的应用程序所需能力的抽象提炼，它提供了应用程序必备的组件和运行机制。有了应用模型，开发者可以基于一套统一的模型进行应用开发，使应用开发更简单、高效。

‍

* **Stage模型：**  HarmonyOS API 9开始新增的模型，是目前主推且会长期演进的模型。在该模型中，由于提供了AbilityStage、WindowStage等类作为应用组件和Window窗口的“舞台”，因此称这种应用模型为Stage模型。Stage模型开发可见[Stage模型开发概述](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/stage-model-development-overview-V5)。**快速入门以此为例提供开发指导。**
* **FA（Feature Ability）模型：**  HarmonyOS API 7开始支持的模型，已经不再主推。FA模型开发可见[FA模型开发概述](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/fa-model-development-overview-V5)。**快速入门章节不再对此展开提供开发指导。**

FA模型和Stage模型的整体架构和设计思想等更多区别，请见[应用模型](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/application-models-V5)。

## 1.2 开始开发

### 1.2.1 下载安装开发工具

	请[安装最新版DevEco Studio](https://developer.huawei.com/consumer/cn/download/)。

> 注意：
>
> * 5.X以后 不要配置 配置nodejs 【 建议本地开发使用18.x , 镜像源使用 华为，避免后面编译、调试报错 。
>
>   * 如报错：Only files in a module can be previewed. 】
>
>     * 修改npm 镜像源 `npm config set registry https://repo.huaweicloud.com/repository/npm/`​
> * 一键安装； mac X86 系统 新版已经不再支持 模拟器

‍

### 1.2.2 创建开发

1. 若首次打开**DevEco Studio**，请点击**Create Project**创建工程。如果已经打开了一个工程，请在菜单栏选择**File** \> **New** \> **Create Project**来创建一个新工程。
2. 选择**Application**应用开发（本文以应用开发为例，Atomic Service对应为元服务开发），选择模板“**Empty Ability**”，点击**Next**进行下一步配置。若开发者需要进行Native相关工程的开发，请选择“**Native C++** ”模板，更多模板的使用和说明请见[工程模板介绍](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-template-V5)。

​![image](/images/assets/harmonyos_1/image-20241224084305-audzv9o.png)​

3. 进入配置工程界面，**Compatible SDK**表示兼容的最低API Version，此处以选择“**5.0.0(12)** ”为例，其他参数保持默认设置即可。

​![image](/images/assets/harmonyos_1/image-20241224084620-0rhzen3.png)​

4. 点击**Finish**，工具会自动生成示例代码和相关资源，等待工程创建完成。

#### 目录介绍

* **AppScope**  **&gt;**  **app.json5**：应用的全局配置信息，**打包编译阶段使用的配置**。详见[app.json5配置文件](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/app-configuration-file-V5)。
* **entry**：HarmonyOS工程模块，编译构建生成一个HAP包。

  * **src**  **&gt;**  **main**  **&gt;**  **ets**：【类似原来项目的src】 用于存放ArkTS源码 ,可以存在多个。

    * **src**  **&gt;**  **main**  **&gt;**  **ets**  **&gt;**  **entryability**：应用/服务的入口, 一个 **entryability 对应一个程序的基本单元（窗口），承载用户信息**，可以存在多个。
    * **src**  **&gt;**  **main**  **&gt;**  **ets**  **&gt;**  **entrybackupability**：应用提供扩展的备份恢复能力。
    * **src**  **&gt;**  **main**  **&gt;**  **ets**  **&gt;**  **pages**：应用页面。
    * **src**  **&gt;**  **main**  **&gt;**  **resources**： **[ 相当于 assest ]** 存放应用/服务所用到的**资源文件**，如图形、多媒体、字符串、布局文件等。关于资源文件，详见[资源分类与访问](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/resource-categories-and-access-V5)。

      * **src**  **&gt;**  **main**  **&gt;**  **resources &gt;**  **base**  **&gt;**  **profile &gt; main_pages.json :  路由配置 【通过 ide 增加 页面，会自动增加页面路由】**
    * **src**  **&gt;**  **main**  **&gt;**  **module.json5**：模块配置文件。主要包含HAP包的配置信息、应用/服务在具体设备上的配置信息以及应用/服务的全局配置信息。具体的配置文件说明，详见[module.json5配置文件](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/module-configuration-file-V5)。
    * **build-profile.json5**：当前的模块信息 、编译信息配置项，包括buildOption、targets配置等。
    * **hvigorfile.ts**：模块级编译构建任务脚本。
    * **obfuscation-rules.txt**：混淆规则文件。混淆开启后，在使用Release模式进行编译时，会对代码进行编译、混淆及压缩处理，保护代码资产。详见[开启代码混淆](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-build-obfuscation-V5)。
    * **oh-package.json5**：用来描述包名、版本、入口文件（类型声明文件）和依赖项等信息。
* **oh_modules**：用于存放三方库依赖信息。
* **build-profile.json5**：工程级配置信息，包括签名signingConfigs、产品配置products等。其中products中可配置当前运行环境，默认为HarmonyOS。
* **hvigorfile.ts**：工程级编译构建任务脚本。
* **oh-package.json5**：【类似node项目的 **package.json**】主要用来描述全局配置，如：依赖覆盖（overrides）、依赖关系重写（overrideDependencyMap）和参数化配置（parameterFile）等。

‍

#### 常用开发目录创建

* **entry &gt; src &gt; main &gt; ets:**

  * apis :  用于接口请求开发 声明
  * utils :  工具函数目录
  * view : 组件目录
  * viewModel :  存放数据和约束
  * pages：页面开发目录
  * lib： 存放三方插件

‍

#### 创建页面

1. 在 pages 页面开发目录 , 右键 新建 > page > Empty Page (  ide **会自动增加页面路由**)  **;**

2. 或者 ArkTS File， 但是此种方式**需要 手动增加路由**

‍

#### 创建组件

在 view 组件目录 , 右键 新建 > ArkTS File

```ts
@Preview // 预览组件
// @Entry // 组件不该使用@Entry; 页面才使用
@Compoent
export struct Header {
	build(){
		Column(){
			Text('这是一个Header组件'	)
		}
		.width('100%')
		.height('100%')
	}
}

```

#### 创建工具类

在 utils 组件目录 , 右键 新建 > ArkTS File

```ts
// 示例创建一个名称为 dataFormat.est 文件
// 用于数据格式化
// 公用函数 需要使用class 方式呈现
export class DataFormat {
	// 日期格式化函数
	dateFormat(value:Date){
		const year = value.getFullyer();
		const month = value.gerMonth() + 1;
		const day = value.getDate();
		return `${year}-${month}-${day}`
	}
}

export default new DataFormat();

//******** 使用 ***********
// 引入
import DataFormat from "@../utils/dataFormat"
// 调用
const dateString = DataFormat.dateFormat(new Date());
```

‍

#### 预览器运行

 	选择pages 下的页面 ets 页面，选择右侧 **Previewer  预览 按钮，进行预览「 支持热更新」;

##### 预览器工具说明一览：

​![img_v3_02hu_8ea8c61a-66a6-4fb5-8d10-bb509c9892ag](/images/assets/harmonyos_1/img_v3_02hu_8ea8c61a-66a6-4fb5-8d10-bb509c9892ag-20241226085740-o97eo01.jpg)​

* **调试**： 顶部 ![img_v3_02hu_50e10af6-0c55-4ffd-a928-db65cb4f77dg](/images/assets/harmonyos_1/img_v3_02hu_50e10af6-0c55-4ffd-a928-db65cb4f77dg-20241226085859-dckd3n2.jpg) 检查按钮点开调试 类似浏览器，可以进行页面调试
* **刷新**： 顶部![image](/images/assets/harmonyos_1/image-20241225082658-255vv7p.png)刷新按钮点击，可以更新页面预览
*  ![img_v3_02hu_031c9b94-cca4-4cb1-9d31-884a3b4d3bcg](/images/assets/harmonyos_1/img_v3_02hu_031c9b94-cca4-4cb1-9d31-884a3b4d3bcg-20241226090321-but83dj.jpg) 预览页面
*  ![img_v3_02hu_9b205015-9f66-4a00-b746-42d673d8253g](/images/assets/harmonyos_1/img_v3_02hu_9b205015-9f66-4a00-b746-42d673d8253g-20241226090353-mjyg0mc.jpg) 预览组件

* ​![img_v3_02hu_46e3d815-f494-44e2-bc2f-61498821c6eg](/images/assets/harmonyos_1/img_v3_02hu_46e3d815-f494-44e2-bc2f-61498821c6eg-20241226090406-0wiz64p.jpg)​ 可以切换设备尺寸 进行预览
* ​![img_v3_02hu_342a3218-aa1e-49f6-936d-e7f4bef58b7g](/images/assets/harmonyos_1/img_v3_02hu_342a3218-aa1e-49f6-936d-e7f4bef58b7g-20241226090705-1pdfctj.jpg)​设备模拟窗口上面 三个按钮依次 是 **返回 、横屏、 更多**

  * **更多 ：中可以 切换语言、设置分辨率、显示安全区**

##### 预览器debugger 调试：


增加代码断点后，在编辑窗口选择设备为 previewer ，点击 ![截屏2024-12-27 14.20.58](/images/assets/harmonyos_1/截屏2024-12-27%2014.20.58-20241227062306-rehmkf6.png)    进行 debugger 调试。

#### 使用真机运行应用

1. 需要 搭载HarmonyOS 5.X 系统的手机
2. 手机端点击系统版本，开启开发者模式。
3. 将真机与电脑连接。

    * 使用无线方式需要开启服务，需要安装 hdc 工具包，且官方文档 提到tcp 连接方式存在不稳定情况。推荐线连
    *  具体指导及要求，可查看[使用本地真机运行应用/服务](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-run-device-V5)。
4. ide 自动签名： 点击**File** \> **Project Structure...**  \> **Project** \> **SigningConfigs**界面勾选“**Support HarmonyOS**”和“**Automatically generate signature**”，点击界面提示的“**Sign In**”，使用华为账号登录。 【此处需要 完成签名部分，具体看 [应用/元服务签名](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-signing-V5)】 等待自动签名完成后，点击“**OK**”即可。如下图所示：

	![image](/images/assets/harmonyos_1/image-20241225083043-t9sjx0v.png)​

‍

‍

在不签名情况，运行手机在报错日志中的链接也可跳转 如下图所示：![image](/images/assets/harmonyos_1/image-20250104065040-axvh17p.png)​

3. 在编辑窗口右上角的工具栏，点击 ![image](/images/assets/harmonyos_1/image-20241225084759-8wkq7bw.png) 按钮运行。
4. debugger 调试： 增加代码断点后，在编辑窗口右上角的工具栏，点击 ![截屏2024-12-27 14.20.58](/images/assets/harmonyos_1/截屏2024-12-27%2014.20.58-20241227062306-rehmkf6.png)    进行 debugger 调试。



debugger 调试 说明

F8：![截屏2024-12-27 14.36.10](/images/assets/harmonyos_1/截屏2024-12-27%2014.36.10-20241227063807-uvc4frk.png)单步跳过。代表 进入下一段代码。 如果当前代码是函数调用，默认不会关注函数内部，只会得到结果

F7:   ![截屏2024-12-27 14.36.39](/images/assets/harmonyos_1/截屏2024-12-27%2014.36.39-20241227063929-2ckymbg.png)单步调试。代表进行 下一段代码调试。 如果当前代码是函数调用，默认进入函数内部调试

‍
