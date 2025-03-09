---
title: webrtc-媒体协商
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - 笔记
  - 直播
  - webrtc
date: 2021-08-01 15:24:44
---
# 媒体协商

> 媒体协商 就是 双方客户端 设备都支持那些编解码器，双方都支持的设备，作为协议编解码的参数

![webretc1对1音视频实时通话的过程图](/images/webRTC/webrtc1to1协商.png)

媒体协商包括两部分：

- **创建连接**，指的是**创建 RTCPeerConnection**，它负责端与端之间彼此建立 P2P 连接。
- **信令**signal，指的是客户端通过信令服务器交换 SDP 信息。

## 媒体协商作用

媒体协商的作用就是**让双方找到共同支持的媒体能力**(如双方都支持的编解码器)，从而**最终实现彼此之间的音视频通信**。

## 媒体协商过程

1. 通信双方将它们各自的媒体信息，如编解码器、媒体流的 SSRC(RTCP 的控制报文)、传输协议、IP 地址和端口等，按 SDP 格式整理好。
2. 通信双方通过信令服务器交换 SDP 信息，并待彼此拿到对方的 SDP 信息后，找出它们共同支持的媒体能力。
3. 双方按照协商好的媒体能力开始音视频通信

## RTCPeerConnection

> **端与端之间建立连接**,WebRTC 库中最关键的一个类，通过它创建出来的对象可以做很多事情，如 NAT 穿越、音视频数据的接收与发送，甚至它还可以用于非音视频数据的传输等等
> RTCPeerConnection 除了会在端与端之间建立连接、传输音视频数据外，还要**进行两次绑定**：一次是与媒体源进行绑定，以解决数据从哪里来的问题；另外一次是与输出进行绑定，以解决接收到的音视频数据显示 / 播放的问题。

```js
var pcConfig = null
var RTCPeerConnection =
  window.RTCPeerConnection || window.webkitRTCPeerConnection
var pc = new RTCPeerConnection(pcConfig)
```

## 媒体协商的过程

### Offer 与 Answer

- Offer，在双方通讯时，呼叫方（clientA）发送的 SDP 消息称为 Offer。
- Answer，在双方通讯时，被呼叫方（clientB）发送的 SDP 消息称为 Answer。

![媒体协商过程图](/images/webRTC/媒体协商过程.png)

### 协商的过程

- 呼叫方 **创建 Offer** 类型的 SDP 消息。
- 创建完成后，**调用 setLocalDescriptoin 方法将该 Offer 保存到本地 Local 域**，然后**通过==信令==将 Offer 发送给被呼叫方**。
- 被呼叫方**收到 Offer** 类型的 SDP 消息后，**调用 setRemoteDescription 方法将 Offer 保存到它的 Remote 域**。
- 作为应答，被呼叫方**要创建 Answer 类型的 SDP 消息，Answer 消息创建成功后，再调用 setLocalDescription 方法将 Answer 类型的 SDP 消息保存到本地的 Local 域**。
- 最后，被呼叫方将 **Answer 消息通过信令发送给**呼叫方。至此，被呼叫方的工作就完部完成了。

- 接下来是呼叫方的收尾工作，呼叫方收到 Answer 类型的消息后，调用 RTCPeerConnecton 对象的 setRemoteDescription 方法，将 Answer 保存到它的 Remote 域。

> 当通讯双方拿到彼此的 SDP 信息后，就可以进行媒体协商了。媒体协商的具体过程是在 WebRTC 内部实现的，我们作为 webrtc 使用者，需要 **本地的 SDP 和远端的 SDP 都设置好**后，协商就算成功了。

- createOffer ，创建 Offer；
- createAnswer，创建 Answer；
- setLocalDescription，设置本地 SDP 信息；
- setRemoteDescription，设置远端的 SDP 信息。

> 通信双方链路的建立是在设置本地媒体能力，即调用 setLocalDescription 函数之后才进行的。

详细代码过程：

1. 呼叫方**创建 Offer**

   - 创建 Offer 类型的 SDP 信息，即**调用 RTCPeerConnection 的 createOffer()** 方法
   - 成功后，调用 **RTCPeerConnection 的 setLocalDescription()** 把本地 SDP（会话描述）信息，提交到本地域
   - 通过信令通道将此会话描述发送给被呼叫方

   > createOffer、setLocalDescription 返回 一个 Promise

   ```js
   function doCall() {
     console.log('Sending offer to peer')
     // pc.createOffer(setLocalAndSendMessage, handleCreateOfferError)
     pc.createOffer(
       function (sessionDescription) {
         // sessionDescription -- RTCSessionDescription 类型的 SDP 信息
         console.log(sessionDescription, 'sessionDescription--成功')
         // 将本地 SDP 描述信息设置到 WebRTC 的 Local 域
         pc.setLocalDescription(sessionDescription)
         sendMessage(sessionDescription)
       },
       function (error) {
         console.log(error, '---报错')
       }
     )
   }
   ```

   ```js
   // 推荐使用方式
   function doCall() {
     pc.createOffer()
       .then(function (offer) {
         console.log(offer, 'offer')
         pc_offer = pc.setLocalDescription(offer)
         return pc_offer
       })
       .then(function () {
         console.log('通过信令服务来发送', pc_offer)
         // Send the offer to the remote peer using the signaling server
       })
       .catch((reportError) => {
         console.log(reportError, 'reportError--报错')
       })
   }
   ```

2. 被呼叫方收到 Offer

   - **调用 setRemoteDescription** 方法设置呼叫方发送给它的 Offer 作为远端描述。

   ```js
     socket.on('message', function(message) {
         ...
         } else if (message.type === 'offer') {
             // 设置 呼叫方的 offer 到远端域
             // 借助 RTCSessionDescription 进行解析
             pc.setRemoteDescription(new RTCSessionDescription(message));
             // 一般在此处 进行 第三步，createAnswer 创建被呼叫放的应答
             doAnswer();
         } else if (...) {
             ...
         }
         ....
     });
   ```

3. 被呼叫方**创建 Answer**

   - 被呼叫方**调用 RTCPeerConnection 对象的 createAnswer 方法**，它会生成一个与远程会话兼容的本地会话，并最终将该会话描述发送给呼叫方。

   > createAnswer、setLocalDescription 返回 一个 Promise

```js
pc.createAnswer()
  .then(function (answer) {
    return pc.setLocalDescription(answer)
  })
  .then(function () {
    // Send the answer to the remote peer through the signaling server.
  })
  .catch(handleGetUserMediaError)
```

4. 呼叫方收到 Answer
   - 当呼叫方得到 被呼叫方的 会话描述(SDP) 时，**调用 setRemoteDescription 方法，将收到的会话描述设置为一个远程会话**。代码如下：

```js
    socket.on('message', function(message) {
      ...
      } else if (message.type === 'answer') {

          pc.setRemoteDescription(new RTCSessionDescription(message));
      } else if (...) {
          ...
      }
      ....
  });
```

5. 媒体协商过程完成。
6. 紧接着**在 WebRTC 底层会收集 ==Candidate==，并进行连通性检测**，最终在通话双方之间建立起一条链路来。


### webrtc-offer.txt 示例
```bash

## 示例是实际 抓包后的数据

-----会话层

v=0			版本号
o=- 5571710891932704000 2 IN IP4 127.0.0.1	session名 sessionId  版本(每次sdp生成版本号+1) IN(互联网)
s=-			session名
t=0 0		起始时间 结束时间
a=group:BUNDLE 0	媒体流绑定一起 传输为1条链路
a=msid-semantic: WMS lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s		媒体流ID

--媒体描述
m=video 9 UDP/TLS/RTP/SAVPF 96 97 98 99 100 101 102 122 127 121 125 107 108 109 124 120 123 119 114 115 116		视频流  9表示该传输端口不使用
c=IN IP4 0.0.0.0						webrtp并不适用的网络连接描述
a=rtcp:9 IN IP4 0.0.0.0
//======= 安全描述 ============
a=ice-ufrag:TfmK							// 进入连通性检测的用户名 -- 链路有效性校验
a=ice-pwd:d+m1xsN8cqRVOQpNLknLX9kG		// 密码，这两个是用于连通性检测的凭证--	链路有效性校验
a=ice-options:trickle						ice通路手机策略 异步
a=fingerprint:sha-256 4F:1D:54:C2:84:4C:C4:CD:75:DC:CB:1C:3A:CA:88:B9:1A:67:1F:85:BC:D9:A9:E5:35:F3:0C:41:BA:03:13:55		//DTLS 指纹认证，以识别是否是合法用户 -- 链路有效性校验
a=setup:actpass 							可选择作为服务端和客户端			actpass 服务器端  active客户端
a=mid:0									对应媒体流组ID的描述
a=extmap:14 urn:ietf:params:rtp-hdrext:toffset
a=extmap:13 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time
a=extmap:12 urn:3gpp:video-orientation
a=extmap:2 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01
a=extmap:11 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type
a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-timing
a=extmap:8 http://tools.ietf.org/html/draft-ietf-avtext-framemarking-07
a=extmap:9 http://www.webrtc.org/experiments/rtp-hdrext/color-space
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid
a=extmap:4 urn:ietf:params:rtp-hdrext:sdes:rtp-stream-id
a=extmap:5 urn:ietf:params:rtp-hdrext:sdes:repaired-rtp-stream-id
a=sendrecv									可发送而可接受  还有 只发送 只接受 等值
a=msid:lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s 3ccc5e67-09ad-434b-83e0-1c29f316b4b7

//======== 服务质量描述 =========
a=rtcp-mux									多路复用
a=rtcp-rsize								如果该路数据过多影响了网路 可不发送该路数据
a=rtpmap:96 VP8/90000
a=rtcp-fb:96 goog-remb
a=rtcp-fb:96 transport-cc
a=rtcp-fb:96 ccm fir						关键帧
a=rtcp-fb:96 nack
a=rtcp-fb:96 nack pli						丢包响应
a=rtpmap:97 rtx/90000
a=fmtp:97 apt=96
a=rtpmap:98 VP9/90000
a=rtcp-fb:98 goog-remb
a=rtcp-fb:98 transport-cc
a=rtcp-fb:98 ccm fir
a=rtcp-fb:98 nack
a=rtcp-fb:98 nack pli
a=fmtp:98 profile-id=0
a=rtpmap:99 rtx/90000
a=fmtp:99 apt=98
a=rtpmap:100 VP9/90000
a=rtcp-fb:100 goog-remb // 使用 google 的带宽评估算法
a=rtcp-fb:100 transport-cc // 启动防拥塞
a=rtcp-fb:100 ccm fir // 解码出错，请求关键帧
a=rtcp-fb:100 nack  // 启用丢包重传功能
a=rtcp-fb:100 nack pli // 与 fir 类似
a=fmtp:100 profile-id=2
a=rtpmap:101 rtx/90000
a=fmtp:101 apt=100
a=rtpmap:102 H264/90000
a=rtcp-fb:102 goog-remb
a=rtcp-fb:102 transport-cc
a=rtcp-fb:102 ccm fir
a=rtcp-fb:102 nack
a=rtcp-fb:102 nack pli
a=fmtp:102 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42001f
a=rtpmap:122 rtx/90000
a=fmtp:122 apt=102
a=rtpmap:127 H264/90000
a=rtcp-fb:127 goog-remb
a=rtcp-fb:127 transport-cc
a=rtcp-fb:127 ccm fir
a=rtcp-fb:127 nack
a=rtcp-fb:127 nack pli
a=fmtp:127 level-asymmetry-allowed=1;packetization-mode=0;profile-level-id=42001f
a=rtpmap:121 rtx/90000
a=fmtp:121 apt=127
a=rtpmap:125 H264/90000
a=rtcp-fb:125 goog-remb
a=rtcp-fb:125 transport-cc
a=rtcp-fb:125 ccm fir
a=rtcp-fb:125 nack
a=rtcp-fb:125 nack pli
a=fmtp:125 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42e01f
a=rtpmap:107 rtx/90000
a=fmtp:107 apt=125
a=rtpmap:108 H264/90000
a=rtcp-fb:108 goog-remb
a=rtcp-fb:108 transport-cc
a=rtcp-fb:108 ccm fir
a=rtcp-fb:108 nack
a=rtcp-fb:108 nack pli
a=fmtp:108 level-asymmetry-allowed=1;packetization-mode=0;profile-level-id=42e01f
a=rtpmap:109 rtx/90000
a=fmtp:109 apt=108
a=rtpmap:124 H264/90000
a=rtcp-fb:124 goog-remb
a=rtcp-fb:124 transport-cc
a=rtcp-fb:124 ccm fir
a=rtcp-fb:124 nack
a=rtcp-fb:124 nack pli
a=fmtp:124 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=4d0032
a=rtpmap:120 rtx/90000
a=fmtp:120 apt=124
a=rtpmap:123 H264/90000
a=rtcp-fb:123 goog-remb
a=rtcp-fb:123 transport-cc
a=rtcp-fb:123 ccm fir
a=rtcp-fb:123 nack
a=rtcp-fb:123 nack pli
a=fmtp:123 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=640032
a=rtpmap:119 rtx/90000
a=fmtp:119 apt=123
a=rtpmap:114 red/90000
a=rtpmap:115 rtx/90000
a=fmtp:115 apt=114
a=rtpmap:116 ulpfec/90000					fec冗余包 传输
a=ssrc-group:FID 488892873 4271901047
a=ssrc:488892873 cname:bpXJsotPCw9hoiWF
a=ssrc:488892873 msid:lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s 3ccc5e67-09ad-434b-83e0-1c29f316b4b7
a=ssrc:488892873 mslabel:lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s
a=ssrc:488892873 label:3ccc5e67-09ad-434b-83e0-1c29f316b4b7
a=ssrc:4271901047 cname:bpXJsotPCw9hoiWF
a=ssrc:4271901047 msid:lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s 3ccc5e67-09ad-434b-83e0-1c29f316b4b7
a=ssrc:4271901047 mslabel:lyasyMn4vDMTNLbTCNwVFjqnrIa5pfzLtA5s
a=ssrc:4271901047 label:3ccc5e67-09ad-434b-83e0-1c29f316b4b7


```