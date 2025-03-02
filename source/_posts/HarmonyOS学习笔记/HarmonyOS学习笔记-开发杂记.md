---
title: HarmonyOS学习笔记-开发杂记
comments: true
mathjax: true
categories:
  - 客户端
tags:
  - 笔记
  - HarmonyOS
  - 拓展
date: 2025-03-03 20:33:27
---
## 网络请求

### 配置权限

鸿蒙中发起网络请求 需要 配置权限`应用需要在module.json5配置文件的requestPermissions标签中声明权限。`​

```json
 "requestPermissions": [
	{
        "name": "ohos.permission.INTERNET",
        "usedScene": {
          "abilities": [
            "entryability"
          ],
          "when":"inuse"
        }
      },
      {
        "name": "ohos.permission.GET_NETWORK_INFO",
        "usedScene": {
          "abilities": [
            "entryability"
          ],
          "when":"inuse"
        }
      },
   ]
```

还有，每次发起请求 `每一个httpRequest对应一个HTTP请求任务，不可复用`​还需要及时清理

```js
import { http } from "@kit.NetworkKit";
// 每一个httpRequest对应一个HTTP请求任务，不可复用
let httpRequest = http.createHttp();
 httpRequest
	.request(url, requestOption)
    .then(async (res: http.HttpResponse) => {
	// do something
	}).catch((res) => {
	// do something
	})

// 取消订阅HTTP响应头事件
httpRequest.off("headersReceive");

// 当该请求使用完毕时，调用destroy方法主动销毁
httpRequest.destroy();
```

简单封装了一个请求 [源码](https://github.com/mcdowell8023/iFavorites/blob/master/harmonyos/tools/httpRequest/index.ts)

‍

## 文件相关

‍

### 文件下载

```ts

import { BusinessError, request } from "@kit.BasicServicesKit";
/**
 * @description: 下载文件
 * @param {string} downloadUrl: 要下载的文件的url
 * @param {string} savePath: 保存路径
 * @return {*}
 */
export async function downloadFile(
  downloadUrl: string,
  savePath: string,
  context
): Promise<BusinessError> {
  return new Promise(async (resolve, reject) => {
    // 下载文件
    try {
      request
        .downloadFile(context, {
          url: downloadUrl,
          filePath: savePath,
        })
        .then((downloadTask: request.DownloadTask) => {
          downloadTask.on("complete", () => {
            console.log(
              "fileTools/file >> downloadFile ::log:: complete 文件下载成功，保存路径：",
              savePath
            );
            resolve({
              code: 200,
              message: "文件下载成功",
              name: "downloadFile",
            });
          });
        })
        .catch((err: BusinessError) => {
          console.error(
            `fileTools/file >> downloadFile ::log:: catch:Invoke downloadTask failed, code is ${err.code}, message is ${err.message}`
          );
          reject(err);
        });
    } catch (error) {
      let err: BusinessError = error as BusinessError;
      console.error(
        `fileTools/file >> downloadFile ::log:: try-catch:Invoke downloadFile failed, code is ${err.code}, message is ${err.message}`
      );
      reject(err);
    }
  });
}
```

### gizp 解压

> 你往往发现 下载下来的文件 乱码 大小与 浏览器下载的大小不一致还不一致？？
>
> 往往，服务器默认会对文件 进行 gzip 处理， 浏览器下载自动进行解压处理了 ，这里 我们需要 自己借助 pako 进行 gizp 解压

‍

使用 pako 进行 gizp 解压

```ts
import pako from "pako";
import { fileIo as fs } from "@kit.CoreFileKit";
/**
 * 解压gz文件方法
 *
 * @since 7
 * @permission N
 * @param {string} src - 解压缩的.gz文件的路径和名称.
 * @param {string} target - 解压缩的目标文件路径.
 * @returns {void | Promise<boolean>} return返回Promise否则返回true或false
 */
export async function unGzipFile(
  src: string,
  target: string
): Promise<boolean> {
  try {
    const reader = fs.openSync(src, fs.OpenMode.READ_ONLY);
    const stat = fs.statSync(src);
    const buf = new ArrayBuffer(stat.size);
    await fs.read(reader.fd, buf);
    const data: string | Uint8Array | undefined = pako.ungzip(
      new Uint8Array(buf)
    );
    if (typeof data == "string") {
      console.error(`fileTools/gzip >> unGzipFile ::log:: unGzipFile [src: $\{src},target: $\{target}] data:  ${data}`);
      return false;
    }
    const writer = fs.openSync(
      target,
      fs.OpenMode.READ_WRITE | fs.OpenMode.CREATE
    );
    fs.writeSync(writer.fd, data?.buffer);
    fs.closeSync(writer);
    fs.closeSync(reader);
    return true;
  } catch (error) {
    console.error(
      `fileTools/gzip >> unGzipFile ::log:: unGzipFile [src: ${src},target: ${target}] error: ${error}`
    );
    return false;
  }
}
```

‍

### zip 解压

> 如果文件是 压缩包，需要用到 zlib

```ts
import { zlib, BusinessError } from "@kit.BasicServicesKit";
export async function unZipFile(
  inFile: string,
  outFile: string,
  opt?: zlib.Options
): Promise<boolean> {
  // 代码中使用的路径需为应用的沙箱路径，如/data/storage/el2/base/haps,也可以通过context获取。
  // let inFile = '/xxx/filename.zip';
  // let outFile = '/xxx/xxx';
  let options: zlib.Options = opt || {
    level: zlib.CompressLevel.COMPRESS_LEVEL_DEFAULT_COMPRESSION,
    memLevel: zlib.MemLevel.MEM_LEVEL_DEFAULT,
    strategy: zlib.CompressStrategy.COMPRESS_STRATEGY_DEFAULT_STRATEGY,
  };

  return new Promise((resolve, reject) => {
    try {
      // zlib.unzipFile
      zlib
        .decompressFile(inFile, outFile, options)
        .then((data: void) => {
          console.info(
            "fileTools/zipFile >> unZipFile ::log:: zipFile result is " +
              JSON.stringify(data)
          );
          resolve(true);
        })
        .catch((err: BusinessError) => {
          console.error(
            "fileTools/zipFile >> unZipFile ::log:: error is " +
              JSON.stringify(err)
          );
          reject(false);
        });
    } catch (err) {
      console.error(
        "fileTools/zipFile >> unZipFile ::log:: error is " + JSON.stringify(err)
      );
      reject(false);
    }
  });
}
```

### rawfile目录读取

> rawfile下的资源会打成一个hap包，没有对外暴露的文件路径，且没有获取文件绝对路径的api

‍

#### N**ative侧读取rawfile目录文件**

* [Native Rawfile接口操作Rawfile目录和文件]([https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/rawfile-guidelines-0000001820880449-V5#ZH-CN_TOPIC_0000001881259997__接口说明](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/rawfile-guidelines-0000001820880449-V5#ZH-CN_TOPIC_0000001881259997__%E6%8E%A5%E5%8F%A3%E8%AF%B4%E6%98%8E))
* 如果需要通过沙箱路径或绝对路径获取rawfile资源，就只能先将rawfile下资源拷贝进应用沙箱目录再进行操作。

#### **ArkTS元应用侧读取rawfile目录文件**

* [getRawFileContentSync ]([https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-resource-manager-V5#getrawfilecontentsync10](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-resource-manager-V5#getrawfilecontentsync10))

* [getRawFileContent](https://developer.huawei.com/consumer/cn/doc/harmonyos-references-V5/js-apis-resource-manager-V5#getrawfilecontent9)

下面是几个 arkTS **rawfile** 的操作实例

```ts


    /**
     * @description: 拷贝 zip 文件到沙箱路径, 并自动解压
     * @param {string} fileName: 要拷贝的文件  '2001.zip'
     * @param {string} san的boxPath: 拷贝后的文件保存路径   'filesDir/2001'
     * @return {*}
     * @example
        copyRawFileZip('2001.zip', this.getDirPathTheme(), applicationContext);
        copyRawFileZip('2001.zip', '/data/storage/el2/base/files/Fmap/theme', applicationContext);
     */
    public copyRawFileZip(fileName: string, sandBoxPath: string): Promise<boolean> {
        return new Promise(async (resolve, reject) => {
            try {
                this.context.resourceManager.getRawFd(fileName, async (err, data) => {
                    if (err != null) {
                        console.error(`FMResourceManager.ts@copyRawFileZip >> getRawFd error : ${JSON.stringify({ fileName:fileName, sandboxPath:sandBoxPath,err:err })} ` );
                        resolve(false);
                        return;
                    }
                    console.log(`FMResourceManager.ts@copyRawFileZip >> getRawFd : `, { fileName:fileName, sandboxPath:sandBoxPath });

                    await checkDir(sandBoxPath);
                    await this.copyFile(fileName,sandBoxPath,data);
					// 此处依赖了一zip 解压函数
                    const isCompled = await unZipFile(sandBoxPath+'/'+fileName, sandBoxPath);

                    this.context.resourceManager.closeRawFd(fileName, (err, data) => {
                        if (err != null) {
                            console.error(`FMResourceManager.ts@copyRawFileZip >> closeRawFd ->:${JSON.stringify(err)}`);
                        }
                    });
                    return resolve(isCompled ? true : false);
                });
            } catch (error) {
                let code = (error as BusinessError).code;
                let message = (error as BusinessError).message;
                console.error(`FMResourceManager.ts@copyRawFileZip >> catch  ->:${JSON.stringify({code:code,message:message})}`, );
                return(false);
            }
        });
    }



    /**
     * 拷贝rawfile到沙箱，md5验证文件，文件已经存在，返回已存在的文件名称
     * @param fileName rawfile静态资源名称
     * @param sandboxPath 沙箱目录
     * @returns 复制后的文件名称,可能和传入的文件名不同 | null 拷贝失败
     */
    public async copyRawFile(fileName: string,sandboxPath: string) : Promise<string> {
        console.log(`FMResourceManager.ts@copyRawFile >> 沙箱路径: ${JSON.stringify({fileName,sandboxPath})}`);
        try {
            let data = await this.context.resourceManager.getRawFdSync(fileName);
            //先将文件复制到临时目录,然后校验md5,如果已有文件，则删除临时文件，返回已有文件的 文件名，如果没有相同文件，则从临时目录移动到最终目录
            let tempPath = sandboxPath+"/temp";
            let tempFileName = tempPath +"/" + fileName;

            console.log(`FMResourceManager.ts@copyRawFile  >> 复制临时文件 ${JSON.stringify({tempPath})}`);
            await this.copyFile(fileName,tempPath,data);
            console.log(`FMResourceManager.ts@copyRawFile  >> 计算文件 Md5 ${JSON.stringify({tempFileName})}`);
            let rawFdFileMd5 = await calFileMd5(tempFileName);
            if(rawFdFileMd5 == null){
                return;
            }

            //校验文件 md5判断是否有重复文件
            let filenames = await fileIo.listFileSync(sandboxPath);

            for (let i = 0; i < filenames.length; i++) {
                let path = sandboxPath +"/"+filenames[i];

                if(filenames[i] === 'temp')continue;
                let fileMd5 = await calFileMd5(path);
                if(fileMd5 == null) continue;
                console.log(`FMResourceManager.ts@copyRawFile  >> 计算目标目录下文件md5->${JSON.stringify({path,fileMd5})}`);
                if(fileMd5 === rawFdFileMd5){
                    //将临时文件删除
                    console.log(`FMResourceManager.ts@copyRawFile  >> 找到md5相同的文件，返回该文件名->${JSON.stringify({path,fileMd5,rawFdFileMd5})}`);
                    fileIo.unlink(tempFileName);
                    return filenames[i];
                }
            }
            //未找到md5同一的文件，则移动临时文件到最终目录
            console.log(`FMResourceManager.ts@copyRawFile  >> 没有重复文件，拷贝文件到指定目录 ->${JSON.stringify({tempFileName,dest:sandboxPath+"/"+fileName})}`);
            await fileIo.copyFile(tempFileName, sandboxPath+"/"+fileName);
            console.log(`FMResourceManager.ts@copyRawFile  >> 删除临时文件->${JSON.stringify({tempFileName})}`);
            await fileIo.unlink(tempFileName);
            return fileName;
        } catch (error) {
            let code = (error as BusinessError).code;
            let message = (error as BusinessError).message;
            console.error(`FMResourceManager.ts@copyRawFile catch error :->${JSON.stringify({code:code,message:message})}`);
            return;
        }
		}


/**
 * 计算文件 md5
 * @param fileUrl 文件路径
 * @returns 文件md5
 */
export async function calFileMd5(fileUrl: string): Promise<string | null> {
  try{
    return await hash.hash(fileUrl, "md5");
  }catch (e){
    FMLog.i("file.ts@calFileMd5 >> calFileMd5 : ",e);
  }
  return null;

}


    /**
     * 将传入文件拷贝到指定目录
     * @param fileName 文件资源名称
     * @param sandboxPath 沙箱目录
     * @returns
     */
    public async copyFile(fileName: string, sandboxPath: string,data:any){
        try{
            await checkDir(sandboxPath);
            let destFilePath = sandboxPath+'/'+fileName;
            console.log(`FMResourceManager.ts@copyFile >> copyFile `,{destFilePath});

            let dest = fileIo.openSync(destFilePath,fileIo.OpenMode.CREATE | fileIo.OpenMode.READ_WRITE);

            let bufsize = 4096;
            let buf = new ArrayBuffer(bufsize);
            let off = 0,len = 0,readedLen = 0;
            /**
             * 通过buffer将rawfile文件内容copy到沙箱路径
             */
            while (len = fileIo.readSync(data.fd, buf, { offset: data.offset + off, length: bufsize })) {
                readedLen += len
                fileIo.writeSync(dest.fd, buf, { offset: off, length: len })
                off = off + len
                if ((data.length - readedLen) < bufsize) {
                    bufsize = data.length - readedLen
                }
            }
            fileIo.close(dest.fd);
        }catch (e) {
            console.error(`FMResourceManager.ts@copyFile >> copyFile  catch error->: ${JSON.stringify(e)} `);
        }
    }
```

‍

### 根据传入的字符串生成图片

实现思路：

* 使用 `@Builder`​ 函数组件 作为 截图对象。（函数组件如果为全局，可能会报错）

* 使用 `componentSnapshot.createFromBuilder`​ 进行离屏渲染截图
* 截图后得到包装图片包装对象，使用  `pixelMap.readPixelsToBufferSync`​ 或者  `image.createImagePacker`​ 的`packToData`​ 进行解码

 [代码](https://github.com/mcdowell8023/iFavorites/blob/master/harmonyos/CreateImageBufferTest.ets)

‍
