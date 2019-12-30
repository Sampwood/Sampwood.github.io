---
title: web端的音频流
date: 2019-10-25 09:39:09
categories:
- coding
- html
tags: 
- html5
---

昨天看到一个有意思的题目：怎么使用HTML5实现录音的功能？

然后试着了解了下，实现原理主要是用了`MediaStream`这个对象。

> The MediaStream interface represents a stream of media content. A stream consists of several tracks such as video or audio tracks. 
> Each track is specified as an instance of MediaStreamTrack.
> You can obtain a MediaStream object either by using the constructor or by calling MediaDevices.getUserMedia().

这是来自`MDN文档` 对 `MediaStream` 的说明。

从中我们可以知道，可以通过 `MediaDevices.getUserMedia()` 方法来获取设备的音频输入功能。

<!-- more -->

### 获取设备的摄像头和麦克风功能

> The MediaDevices.getUserMedia() method prompts the user for permission to use a media input 
> which produces a MediaStream with tracks containing the requested types of media. 
> That stream can include, for example, 
> a video track (produced by either a hardware or virtual video source such as a camera, video recording device, screen sharing service, and so forth), 
> an audio track (similarly, produced by a physical or virtual audio source like a microphone, A/D converter, or the like), and possibly other track types.

这是来自`MDN文档`的说明。

`MediaDevices.getUserMedia()` 这个方法需要传入一个参数 `constraints` , 来指定对语音/视频的限制，例如： `{ audio: true, video: true }`

然后参考它提供的demo，写了个 实时录音并播放的功能：
```html
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div style="padding: 50px;">
    <audio src="" controls></audio>
  </div>
  <script>
    // Older browsers might not implement mediaDevices at all, so we set an empty object first
    if (navigator.mediaDevices === undefined) {
      navigator.mediaDevices = {};
    }

    // Some browsers partially implement mediaDevices. We can't just assign an object
    // with getUserMedia as it would overwrite existing properties.
    // Here, we will just add the getUserMedia property if it's missing.
    if (navigator.mediaDevices.getUserMedia === undefined) {
      navigator.mediaDevices.getUserMedia = function(constraints) {

        // First get ahold of the legacy getUserMedia, if present
        var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

        // Some browsers just don't implement it - return a rejected promise with an error
        // to keep a consistent interface
        if (!getUserMedia) {
          return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
        }

        // Otherwise, wrap the call to the old navigator.getUserMedia with a Promise
        return new Promise(function(resolve, reject) {
          getUserMedia.call(navigator, constraints, resolve, reject);
        });
      }
    }

    navigator.mediaDevices.getUserMedia({ audio: true, video: false })
    .then(function(stream) {
      var audio = document.querySelector('audio');
      // Older browsers may not have srcObject
      if ("srcObject" in audio) {
        audio.srcObject = stream;
      } else {
        // Avoid using this in new browsers, as it is going away.
        audio.src = window.URL.createObjectURL(stream);
      }
      audio.onloadedmetadata = function(e) {
        audio.play();
      };
    })
    .catch(function(err) {
      console.log(err.name + ": " + err.message);
    });
  </script>
</body>
</html>
```

从上可以看出，只要把 `audio.srcObject` 设置成获取到的 `stream` 就可以播放录音了。

### 如何停止录音

在上面的例子中，我们实现了音频流的输入和输出。但是这个 `audio` 的暂停似乎只控制了音频的输出，那如何停止音频的输入呢？
`MediaStream` 本身没有提供 `stop/end` 之类的方法。

从`MediaStream` 的说明中，可以看出 `MediaStream` 是由 `MediaStreamTrack` 组成的。

> The MediaStreamTrack interface represents a single media track within a stream; 
> typically, these are audio or video tracks, but other track types may exist as well.

`MediaStreamTrack` 提供了 `stop` 方法：
> Stops playing the source associated to the track, both the source and the track are deassociated. The track state is set to ended.

> Calling stop() tells the user agent that the track's source—whatever that source may be, including files, network streams, or a local camera or microphone—is no longer needed by the MediaStreamTrack.  Since multiple tracks may use the same source (for example, if two tabs are using the device's microphone), the source itself isn't necessarily immediately stopped. It is instead disassociated from the track and the track object is stopped. Once no media tracks are using the source, the source may actually be completely stopped.

> Immediately after calling stop(), the track state is set to ended.

所以停止录音的方法是：
```javascript
stream.getTracks().forEach(function(track) {
  track.stop();
});
```

### 音频流的保存

当我们调用了设备的麦克风之后，得到了 `stream`。通过 `audio` 标签实现了 `stream` 的输出。此时如果要把得到这段`stream` 保存下来该如何做呢？

> The MediaStream Recording API, sometimes simply referred to as the Media Recording API or the MediaRecorder API, is closely affiliated with the Media Capture and Streams API and the WebRTC API. The MediaStream Recording API makes it possible to capture the data generated by a MediaStream or HTMLMediaElement object for analysis, processing, or saving to disk. It's also surprisingly easy to work with.

> The MediaStream Recording API is comprised of a single major interface, MediaRecorder, which does all the work of taking the data from a MediaStream and delivering it to you for processing. The data is delivered by a series of dataavailable events, already in the format you specify when creating the MediaRecorder. You can then process the data further or write it to file as desired.

原来还有个 `MediaRecorder` 用以完成数据的保存😄

参考`MDN` 提供的demo，实现音频的保存和重播
```javascript
const recordedChunks = []
const mediaRecorder = new MediaRecorder(stream) // stream 就是我们前面获取到的 stream

mediaRecorder.ondataavailable = function (e) {
  recordedChunks.push(e.data)
}

mediaRecorder.onstop = function(e) {
  const blob = new Blob(recordedChunks, { 'type' : 'audio/ogg; codecs=opus' })
  const audio = document.querySelector('audio') // 或者其他的 audio 对象
  audio.src = URL.createObjectURL(blob)
  audio.srcObject = null // 如果之前的 audio设置了 srcObject 则需要在此处设置为null；否则不需要次操作
}

mediaRecorder.start() // 开始记录

setTimeout(() => {
  mediaRecorder.stop() // 结束记录
}, 5000) // 自定义5s后结束记录，或者手动结束
```

如果要下载的话，只需把上面创建的 `url` 放到 `a` 标签中就可实现。



## 参考
1. [Media Capture and Streams API (Media Stream)](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API)
2. [MediaDevices.getUserMedia()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)
3. [MediaStreamTrack.stop()](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/stop)
4. [MediaStream Recording API](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream_Recording_API)
