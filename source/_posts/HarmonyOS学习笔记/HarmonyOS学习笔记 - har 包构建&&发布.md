---
title: HarmonyOS学习笔记 - har 包构建&&发布
comments: true
mathjax: true
categories:
  - 客户端
tags:
  - 笔记
  - HarmonyOS
  - 拓展
date: 2025-03-02 18:26:47
---

‍

## HAR 的介绍

> HAR（Harmony Archive）是静态共享包，可以包含代码、C++ 库、资源和配置文件。通过 HAR 可以实现多个模块或多个工程共享 ArkUI 组件、资源等相关代码。

‍

### 使用用场景

* 作为二方库，发布到 [OHPM](https://ohpm.openharmony.cn/#/cn/home) 私仓，供公司内部其他应用使用。
* 作为三方库，发布到 [OHPM](https://links.jianshu.com/go?to=https%3A%2F%2Fohpm.openharmony.cn%2F) 中心仓，供其他应用使用。

### HAR 约束限制

‍

* 不支持在**设备上单独安装/运行**，**只能作为应用模块的依赖项被引用**。
* **不支持在配置文件中声明**​**[UIAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/uiability-overview-V5)**​**组件与**​**[ExtensionAbility](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/extensionability-overview-V5)**​**组件**。
* **不支持在配置文件中声明**​**[pages](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/module-configuration-file-V5#pages%E6%A0%87%E7%AD%BE)**​**页面**，但是可以包含 pages 页面，并通过[命名路由](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/arkts-routing-V5#%E5%91%BD%E5%90%8D%E8%B7%AF%E7%94%B1)的方式进行跳转。
* **不支持引用 AppScope 目录中的资源**。在编译构建时，AppScope 中的内容不会打包到 HAR 中，因此会导致 HAR 资源引用失败。
* 可以依赖其他 HAR，但**不支持循环依赖**，也**不支持依赖传递**。

‍

## 构建 HAR

‍

#### 工具准备

1. ide : devEoc-studio  [  安装到华为官网自行下载 ]
2. **ohpm 命令行工具**：   [使用手册](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V2/ide-command-line-ohpm-0000001490235312-V2)

   * [下载 Command Line Tools](https://developer.huawei.com/consumer/cn/download/) 解压后 拷贝到 ～/ 目录下 ,重新命名为 `.ohpm_tools`​ 「 ps 如果 .ohpm 不冲突也可以 不修改 」
   * cd 进入 `.ohpm_tools/bin`​ 目录下  执行 `./init`​
   * 将 ohpm 配置到环境变量中:

     ```bash
     export OHPM_HOME=/home/xx/Downloads/ohpm  #本处路径请替换为ohpm的安装路径
     export PATH=${OHPM_HOME}/bin:${PATH}


     ## 例如
     export OHPM_HOME="$HOME/.ohpm_tools" #本处路径请替换为ohpm的安装路径
     export PATH=${OHPM_HOME}/bin:${PATH}%
     ```

‍

‍

#### 1、创建库模块

###### 1）新建一个鸿蒙项目（或者使用原有项目也可以），以新建的鸿蒙项目 `fengmap_harmony_sdk`​ 为例，鼠标移到工程目录顶部，单击右键，选择 New \> Module，在工程中添加模块：

‍

​![截屏 2025-02-23 16.13.40](/images/assets/har_buuild/截屏2025-02-23%2016.13.40-20250223081400-ks52psv.png)​

‍

2）在 Choose Your Ability Template 界面中，选择 Static Library，并单击 Next：

‍

‍

###### 3）在 Configure New Module 界面中，设置新添加的模块信息（以新增模块 `fengmap_harmony_sdk`​ 为例），设置完成后，单击 Finish 完成创建。

* Module name：新增模块的名称。比如：`fengmap_harmony_sdk`​。
* Device type：支持的设备类型。
* Enable native：是否创建一个用于调用 C++ 代码的模块。

  ‍

​![截屏 2025-02-23 16.16.17](/images/assets/har_buuild/截屏2025-02-23%2016.16.17-20250223081631-emrcp2r.png)​![截屏 2025-02-23 16.17.10](/images/assets/har_buuild/截屏2025-02-23%2016.17.10-20250223081736-i4mo6l6.png)​

‍

#### ？？已经存在模块

> 如果已经有了要打包的模块，那么 选中模块 shift+F6  弹出菜单 选择《重命名模块》修改名称即可，devEoc-studio 会自动帮你修改所有相关名称

‍

‍

#### 2、HAR 包的构建

1. 将要发布使用的代码放在 src/main/ets/components 目录下，比如新增一个 ets 文件 index.ets。 随便写些功能，如增加 FMMapViewComponent 页面。

2. 在模块包根目录下 Index.ets  如 `fengmap_harmony_sdk/Index.ets`​  导出页面

```js
export { FMMapViewComponent } from './src/main/ets/components/FMMapViewComponent'
```

‍

3. 在模块的 `fengmap_harmony_sdk/oh-package.json5`​ 文件修改希望使用的 **引用名称**。 如：`@fengmap/sdk`​  根据官方名规范 为 `@+组织名称/名称`​  当然在该文件可以修改某些 包的关键信息，如： 版本 `version`​ 、描述 `description`​ 、入口 `main`​、开源仓库检索关键字 `keywords`​、作者 `author`​、开源协议 `license`​

   > 如现在命名为：@fengmap/sdk 那么用户在 引入调用的 名称 与此一致
   >
   > 实际使用 `import { FMMapViewComponent } from '@fengmap/sdk';`​
   >

```json
{
  "name": "@fengmap/sdk",
  "version": "1.0.0",
  "description": "XXXX",
  "main": "Index.ets",
  "author": "wxq24",
  "license": "Apache-2.0",
  "dependencies": {},
  "compatibleSdkType": "HarmonyOS"
}
```

‍

‍

4. 开发完库模块后，**选中模块名**，然后通过 DevEco Studio 菜单栏的 **Build**  **&gt;**  **Make Module '**​`fengmap_harmony_sdk`​ **'**  进行编译构建，生成 HAR。HAR 可用于工程其它模块的引用，或将 HAR 上传至 ohpm 仓库，供其他开发者下载使用。若部分源码文件不需要打包至 HAR 中，可通过[创建.ohpmignore 文件](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-hvigor-build-har-V5#li18114177202814)，配置打包时要忽略的文件/文件夹。
5. 使用 **release 模式构建 HAR 具体看。 其他构建方式 具体看**  **[官方构建文档](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V13/ide-hvigor-build-har-V13#section18223859204318)**

   点击 DevEco Studio 右上角图标 ![image](/images/assets/har_buuild/image-20250223092749-e3cgdzi.png)**Build Mode** 中选择 **release。** 默认为 **&lt;Default&gt;** 模式：在编译 App 时使用 release 模式，编译 HAP/HSP/HAR 时使用 debug 模式。

   ​![image](/images/assets/har_buuild/image-20250223092801-x16de5r.png)​

   在使用 release 模式 为保护代码资产，建议开启混淆。 在模块级 build-profile.json5 文件 配置如下：

   ```json
   {
     "apiType": "stageMode",
     "buildOption": {
     },
     "buildOptionSet": [
       {
         "name": "release",
         "arkOptions": {
           // 混淆相关参数
           "obfuscation": {
             "ruleOptions": {
               // true表示进行混淆，false表示不进行混淆。5.0.3.600及以上版本默认为false
               "enable": true,
               // 混淆规则文件
               "files": [
                 "./obfuscation-rules.txt"
               ]
             },
             // consumerFiles中指定的混淆配置文件会在构建依赖这个library的工程或library时被应用
             "consumerFiles": [
               "./consumer-rules.txt"
             ]
           }
         },
       },
     ],
     "targets": [
       {
         "name": "default"
       }
     ]
   }
   ```
6. 最外层工程级 `build-profile.json5` ​中的 useNormalizedOHMUrl 字段设置为 false。

   ```json
   {
     "app": {
       "products": [
         {
            "buildOption": {
              "strictMode": {
                "useNormalizedOHMUrl": false
              }
            }
         }
       ]
     }
   }
   ```
7. 构建成功后，会生成一个包 `fengmap_harmony_sdk`​.har，在 build/default/outputs/default/`fengmap_harmony_sdk`​.har 路径下。

​![截屏 2025-02-23 16.33.23](/images/assets/har_buuild/截屏2025-02-23%2016.33.23-20250223083332-cyi7xdn.png)​![截屏 2025-02-23 16.34.14](/images/assets/har_buuild/截屏2025-02-23%2016.34.14-20250223083427-k5zrozd.png)​

‍

‍

#### 3、HAR 包的导入和使用

1）验证下本地构建好的 HAR 包是否可以在项目中导入和使用，首先新建一个测试项目 `maptest`​ ，工程目录下**新建文件夹 libs**，并将 `fengmap_harmony_sdk`​.har 包拷贝到该目录下，打开项目的 `oh-package.json5`​  添加 har 包的依赖，最后执行同步操作 Sync Now

​![WechatIMG28](/images/assets/har_buuild/WechatIMG28-20250223084107-3g8zenb.jpg)​

‍

​`maptest`​ 项目 下的 `oh-package.json5`​ 示例如下：

```json
{
  "name": "entry",
  "version": "1.0.0",
  "description": "Please describe the basic information.",
  "main": "",
  "author": "",
  "license": "",
  "dependencies": {
    "@fengmap/sdk": "file:../libs/fengmap_harmony_sdk.har"
  }
}

```

‍

‍

2）导入成功后，会生成一个文件 `oh-package-lock.json5`​，里面详细记录导入的包的信息

​![2](/images/assets/har_buuild/2-20250223084210-0b331j7.png)​

‍

3）在 `ets/pages Index.ets ` ​中导入包的头文件，调用 har 包中的相关代码。

```js
import { FMMapViewComponent } from '@fengmap/sdk';
@Entry
@Component
struct Index {
      build() {
        Column() {
          FMMapViewComponent()
        }
        .height('100%')
        .width('100%')
      }
}
```

4）接下来运行项目，查看效果。没有问题 至此 结束。 「 如果**不准备** 上传到 开源仓库 让用户通过命令使用，此处构建已经结束。 直接本地分发使用即可」

‍

## 发布 HAR 包

#### 1. 在库模块 `fengmap_harmony_sdk` ​中（与 src 文件夹同一级目录下），修改 oh-package.json5 文件内容，如下：

```json

{
  "name": "@fengmap/sdk",
  "version": "1.0.0",
  "description": "XXXXXXXXXXXX",
  "main": "Index.ets",
  "keywords": ['map','fengmap','mapSDK','室内地图','地图SDK'],
  "author": "fengmap",
  "license": "Apache-2.0",
  "dependencies": {
    "liblibrary.so": "file:./src/main/cpp/types/liblibrary",
    "pako": "^2.1.0"
  },
  "devDependencies": {
    "@types/pako": "^2.0.0"
  },
  "dynamicDependencies": {},
  "compatibleSdkVersion": "12",
  "compatibleSdkType": "HarmonyOS",
  "obfuscated": false,
  "nativeComponents": [
    {
      "name": "liblibrary.so",
      "compatibleSdkVersion": "12",
      "compatibleSdkType": "HarmonyOS"
    }
  ]
}
```

* name：**HAR 包名**。在 ohpm 中包的命名格式为  **@&lt;group&gt;/&lt;package_name&gt;**  或者  **&lt;package_name&gt;** 。

  * 其中 group 是组织，package\_name 是包名。
  * 当想要上传一个含有组织（例如 @fengmap/map）的包时，在 [ohpm-repo](https://ohpm.openharmony.cn/#/cn/home) 中需要先创建出该组织（例如 fengmap）才能进行上传。
  * **该组织名称需要审核**，**只有审核通过后，才能在该组织下发布包**，所以要**提前申请**。具体的申请，请参考官方文档：[组织管理](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-ohpm-organization-V5)。
* version：包的版本号，一般从 1.0.0 开始。
* description：包的描述。
* main: 定义包的主入口文件。
* author：开发者名称。
* license: 包的许可证类型。
* dependencies: 列出项目运行时必需的依赖包及其版本。

其他配置具体看[官方文档配置](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V13/ide-oh-package-json5-V13)

以上信息中的 name、author、description，会在 [OpenHarmony 三方库中心仓](https://ohpm.openharmony.cn/#/cn/home)中展示。

> ⚠️ 注意：在后面的 README.md 文件或者 CHANGELOG.md 中如果介绍包信息，其中包名一定要和 name 中设置的包名（@wxqos/wxqcolor）一致，否则有审核失败的风险！！！

‍

#### 2.  添加其他必要文件

* 新建 README.md 文件： 在 README.md 文件中**必须包含包的介绍和引用方式**，还可以根据包的内容添加更详细介绍。

​![截屏 2025-02-23 17.48.48](/images/assets/har_buuild/截屏2025-02-23%2017.48.48-20250223094904-bwpuv12.png)​

* 新建 CHANGELOG.md 文件：填写 HAR 的版本更新记录。

​![截屏 2025-02-23 17.50.19](/images/assets/har_buuild/截屏2025-02-23%2017.50.19-20250223095032-xhls821.png)​

‍

* 添加 LICENSE 文件：LICENSE 许可文件。  可以去  [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)  直接粘贴 具体格式 进行修改

  ‍

  ```md
  Copyright 发布年份 应用组织

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
  ```

> ⚠️ 注意：以上内容要保证文件不缺失、内容正确且真实，不然之后的审核有可能会通不过！

‍

#### 3、修改完以上信息后，需要重新执行下 Make Module '`fengmap_harmony_sdk`​' 编译构建一个新的 `fengmap_harmony_sdk`​.har 包。

‍

#### 4、开始发布

1. 利用工具 ssh-keygen 生成公、私钥，可通过执行以下命令来生成公钥和私钥：

```py
ssh-keygen -m PEM -t RSA -b 4096 -f ~/.ssh_ohpm/mykey

## 例如
ssh-keygen -m PEM -t RSA -b 4096 -f ~/.ssh_ohpm/fengmapKey

```

‍

> ❗️ 前提： 如果没有~/.ssh_ohpm 目录需要先创建  mkdir .ssh\_ohpm
>
> ⚠️ 注意：OHPM 包管理器只支持加密密钥认证，请在生成公私钥时输入密码, ❗️ 并要记住密码，发布时候会使用
>
> OHPM 包管理器只支持加密密钥认证，请在生成公私钥时输入密码。【解释：就是上面说的密码，后面上传包的时候会用】

‍

2. 登录 [OpenHarmony 三方库中心仓](https://ohpm.openharmony.cn/#/cn/personalCenter/notifications)官网，单击主页右上角的个人中心，新增 OHPM 公钥，将公钥文件（XXX.pub）的内容粘贴到公钥输入框中。

   ​![image](/images/assets/har_buuild/image-20250223100823-je3sgcy.png)​![image](/images/assets/har_buuild/image-20250223100846-03bv6tu.png)​
3. 使用 命令行 配置私钥路径：（ 此处需要 使用 ohpm 工具包 ）

   ```py
   ohpm config set key_path your-keypath
   ## 例如
   ohpm config set key_path  ~/.ssh_ohpm/fengmapKey
   ```

   > ～/.ohpm/.ohpmrc  隐藏文件 中会填充一条内容，表示命令执行成功 :
   >
   > ```ruby
   > key_path=/Users/xxx/.ssh_ohpm/fengmapKey
   > ```
   >
4. 配置登录用户发布码：

   * 登录 [OpenHarmony 三方库中心仓](https://ohpm.openharmony.cn/#/cn/personalCenter/notifications)官网，单击主页右上角的**个人中心**，复制发布码

     ​![image](/images/assets/har_buuild/image-20250223101456-tdgukej.png)​
   * 获取发布码并配置到 .ohpmrc 文件中，可执行如下命令：

   ```bash
   ohpm config set publish_id your_publish_id

   ## 如：
   ohpm config set publish_id VC2A1KN4Y8
   ```

> 此处 亦会 在 ～/.ohpm/.ohpmrc 进行追加:
>
> ```bash
> key_path=/Users/mcdowell/.ssh_ohpm/fengmapKe
> publish_id=VC2A1KN4Y8
> ```

‍

‍

‍

5. 执行如下命令发布 HAR，\<HAR 路径\>需指定为.har 文件的**绝对路径**

   ```bash
    ohpm publish /Users/mcdowell/project/HarmonyProject/fengmap_harmony/fengmap_harmony_sdk/build/default/outputs/default/fengmap_harmony_sdk.har
   ```

> ⚠️ 注意：其中会让你输入一个密码，就是上面说的上传包的密码。

‍

如果在输入密码后，见到这句 `Thanks for your contribution, the submitted OHPM library is under review, you can check the package status from https://ohpm.openharmony.cn/#/cn/personalCenter/package`​  那么就表示成功。

‍

> ❗️ 报错：
>
> * ohpm ERROR: The "paths[1]" argument must be of type string. Received undefined   --- > 注意 ohpm 版本，低版会出现该问题，尝试升级到最新版本

‍

‍

在 [OpenHarmony 三方库中心仓](https://ohpm.openharmony.cn/#/cn/personalCenter/package)的消息中能查看到你提交的信息，以及审核结果的信息：

​​

​![截屏2025-02-24 17.09.33](/images/assets/har_buuild/截屏2025-02-24%2017.09.33-20250224091041-3kv4r4f.png)​

审核通过后，就可以在 [OpenHarmony 三方库中心仓](https://links.jianshu.com/go?to=https%3A%2F%2Fohpm.openharmony.cn%2F)中搜索到了：

‍

​​

‍

​![WX20250226-103429@2x](/images/assets/har_buuild/WX20250226-103429@2x-20250226030035-04kjfr1.png)​

‍

## 发布成功 HAR 包的导入和使用

‍

#### 在项目的 Terminal 执行导入命令：

```bash
 ohpm install @fengmap/sdk
```

‍

#### 具体的使用和上面本地 har 包使用一样：

```bash
import { FMMapViewComponent } from '@fengmap/sdk'
```

开始使用 食用即可。

‍
