---
title: 浏览器全屏api--fullScreen
comments: true
mathjax: true
categories:
  - 拓展
tags:
  - 浏览器
  - api
date: 2019-09-24 15:40:35
---

# fullScreen 浏览器 全屏 api

> 本文转载 [Fullscreen API](https://davidwalsh.name/fullscreen)

工作开发中遇到需要浏览器 全屏。
查询 api 后，决定使用 [requestFullscreen](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/requestFullscreen)，但是 api 存在兼容问题。

借助大佬的总结：

## 启动全屏模式

```js
// 处理兼容性 调用 全屏api
function launchIntoFullscreen(element) {
  if (element.requestFullscreen) {
    element.requestFullscreen()
  } else if (element.mozRequestFullScreen) {
    element.mozRequestFullScreen()
  } else if (element.webkitRequestFullscreen) {
    element.webkitRequestFullscreen()
  } else if (element.msRequestFullscreen) {
    element.msRequestFullscreen()
  }
}

// 开启全屏 模式  谷歌浏览器 、安卓手机 亲测可用
launchIntoFullscreen(document.documentElement) // 整个页面
launchIntoFullscreen(document.getElementById('videoElement')) // 或者任意对象
```

## 退出全屏模式

```bash
  // 退出全屏
  function exitFullscreen() {
    if(document.exitFullscreen) {
      document.exitFullscreen();
    } else if(document.mozCancelFullScreen) {
      document.mozCancelFullScreen();
    } else if(document.webkitExitFullscreen) {
      document.webkitExitFullscreen();
    }
  }
```

## 全屏属性和事件

- document.fullScreenElement：已推送到全屏状态的元素。
- document.fullScreenEnabled：说明当前是否启用了全屏。

```js
/* 兼容写法 */
var fullscreenElement =
  document.fullscreenElement ||
  document.mozFullScreenElement ||
  document.webkitFullscreenElement
var fullscreenEnabled =
  document.fullscreenEnabled ||
  document.mozFullScreenEnabled ||
  document.webkitFullscreenEnabled
```

实际使用

```js
// 添加事件监听
document.addEventListener('fullscreenchange', function(e) {
  console.log('fullscreenchange event! ', e)
})
document.addEventListener('mozfullscreenchange', function(e) {
  console.log('mozfullscreenchange event! ', e)
})
document.addEventListener('webkitfullscreenchange', function(e) {
  console.log('webkitfullscreenchange event! ', e)
})
document.addEventListener('msfullscreenchange', function(e) {
  console.log('msfullscreenchange event! ', e)
})
```

## 全屏 CSS

```css
:-webkit-full-screen {
  /* properties */
}

:-moz-full-screen {
  /* properties */
}

:-ms-fullscreen {
  /* properties */
}

:full-screen {
  /*pre-spec */
  /* properties */
}

:fullscreen {
  /* spec */
  /* properties */
}

/* deeper elements */
:-webkit-full-screen video {
  width: 100%;
  height: 100%;
}

/* styling the backdrop*/
::backdrop {
  /* properties */
}
::-ms-backdrop {
  /* properties */
}
```

## 实例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>全屏api测试页面</title>
  </head>
  <body>
    <div style="padding:20px;">
      <button
        onclick="launchFullscreen(document.documentElement);"
        class="sexyButton"
      >
        启动全屏
      </button>
      <button onclick="exitFullscreen();" class="sexyButton">关闭全屏</button>
      <button onclick="dumpFullscreen();" class="sexyButton">
        转储全屏属性数据
      </button>
    </div>
    <!-- 谷歌浏览器 、安卓手机 亲测可用 -->
    <style>
      /* 全屏后 样式 */
      :-webkit-full-screen {
        background: pink;
      }

      :-moz-full-screen {
        background: pink;
      }

      :-ms-fullscreen {
        background: pink;
      }

      :full-screen {
        /*pre-spec */
        background: pink;
      }

      :fullscreen {
        /* spec */
        background: pink;
      }
    </style>
    <script>
      // 处理兼容性 调用 全屏api
      function launchFullscreen(element) {
        if (element.requestFullscreen) {
          element.requestFullscreen()
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen()
        } else if (element.webkitRequestFullscreen) {
          element.webkitRequestFullscreen()
        } else if (element.msRequestFullscreen) {
          element.msRequestFullscreen()
        }
      }
      // 退出全屏
      function exitFullscreen() {
        if (document.exitFullscreen) {
          document.exitFullscreen()
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen()
        } else if (document.webkitExitFullscreen) {
          document.webkitExitFullscreen()
        }
      }

      function dumpFullscreen() {
        console.log(
          'document.fullscreenElement is: ',
          document.fullscreenElement ||
            document.mozFullScreenElement ||
            document.webkitFullscreenElement ||
            document.msFullscreenElement
        )
        console.log(
          'document.fullscreenEnabled is: ',
          document.fullscreenEnabled ||
            document.mozFullScreenEnabled ||
            document.webkitFullscreenEnabled ||
            document.msFullscreenEnabled
        )
      }

      // 添加事件监听
      document.addEventListener('fullscreenchange', function(e) {
        console.log('fullscreenchange event! ', e)
      })
      document.addEventListener('mozfullscreenchange', function(e) {
        console.log('mozfullscreenchange event! ', e)
      })
      document.addEventListener('webkitfullscreenchange', function(e) {
        console.log('webkitfullscreenchange event! ', e)
      })
      document.addEventListener('msfullscreenchange', function(e) {
        console.log('msfullscreenchange event! ', e)
      })
    </script>
  </body>
</html>
```
