---
toc: true
toc_sticky: true
categories:
  - Javascript
title: \[Javascript\] Web에서의 마이크 음성녹음 방법(MediaRecorder, Web Audio API, WebWoker, AudioWorklet)
tags: [Javascript]
excerpt: "Web 환경에서의 마이크를 통한 음성녹음 방법에 대해 자세하게 알아봅시다."
---

# Web에서의 마이크 음성녹음 방법

## 개요

웹 환경에서 자바스크립트를 통한 음성 녹음을 수행하려면 Web API를 사용해야한다.  
즉, 음성 녹음은 브라우저를 통해 마이크 정보를 얻어서 음성 데이터 Stream을 구성하고, 녹음을 통해 웹 페이지까지 수신을 하게 된다. 

1. 마이크 장치 수신: Navigator.mediaDevices
    - [https://developer.mozilla.org/ko/docs/Web/API/Navigator/mediaDevices](https://developer.mozilla.org/ko/docs/Web/API/Navigator/mediaDevices)
2. 마이크 소리 입력: MediaStream
    - [https://developer.mozilla.org/en-US/docs/Web/API/MediaStream](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream)
3. 입력된 소리 녹음: MediaRecorder or Web Audio API
    - [https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder)
    - [https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)

<br>

여기서 음성을 녹음하는 방법에는 **`MediaRecorder`** 와 **`Web Audio API`** 두 가지 방법이 있다.  
MediaRecorder는 단순 오디오 녹음을 할 경우 사용하고, 조금 더 복잡한 처리를 위해서는 Web Audio API를 사용한다. 

하지만 실제로 이 두가지 녹음방법을 통한 웹에서 녹음기를 구현하면서 발생할 수 있는 여러가지 문제점이 있다.  
이러한 문제점을 해결하는 과정에서 **`WebWoker`**, **`AudioWorklet`** 라는 놈들을 만나게 된다.

기본적인 음성녹음 구현부터 시작하여 문제점들을 마주하고 이를 해결하며 웹에서 사용중인 녹음방법들에 대해 알아보자.

<br>

## MediaRecorder 활용

Navigator.mediaDevices, MediaStream, MediaRecorder만을 활용한 간단한 예제이다.  
MediaRecorder를 이용하면 쉽게 녹음부를 구현할 수 있다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Audio Recorder 녹음 예제</title>
</head>
<body>
<button id="startButton">Start Recording</button>
<button id="stopButton" disabled>Stop Recording</button>
<audio id="audioElement" controls></audio>

<script>
let audioStream;
let mediaRecorder;
let chunks = [];

const startButton = document.getElementById('startButton');
const stopButton = document.getElementById('stopButton');
const audioElement = document.getElementById('audioElement');

startButton.addEventListener('click', startRecording);
stopButton.addEventListener('click', stopRecording);

function startRecording() {
    navigator.mediaDevices.getUserMedia({ audio: true })
        .then(function(stream) {
            audioStream = stream;
            mediaRecorder = new MediaRecorder(stream);
            mediaRecorder.ondataavailable = function(event) {
                chunks.push(event.data);
            };
            mediaRecorder.onstop = function() {
                const blob = new Blob(chunks, { 'type' : 'audio/ogg; codecs=opus' });
                chunks = [];
                const audioURL = URL.createObjectURL(blob);
                audioElement.src = audioURL;
            };
            mediaRecorder.start();
            startButton.disabled = true;
            stopButton.disabled = false;
        })
        .catch(function(err) {
            console.error('Error accessing the microphone: ' + err);
        });
}

function stopRecording() {
    mediaRecorder.stop();
    audioStream.getTracks().forEach(track => track.stop());
    startButton.disabled = false;
    stopButton.disabled = true;
}
</script>
</body>
</html>
```

<br>

위 코드를 보면 다음과 같다.

1. WebAPI **`navigator.mediaDevices.getUserMedia`** 를 호출하여 마이크의 Stream을 얻어온다.
2. 얻어진 **`stream`** 을 **`MediaRecorder`** 의 생성자로 입력하여 각체를 생성한다.
3. **`mediaRecorder`** 에 음성버퍼 이벤트 수신부 **`ondataavailable`** 를 구현한다.
4. **`mediaRecorder`** 의 **start()**, **stop()** 메소드를 호출하여 녹음 동작을 수행한다.

<br>

하지만 단순히 음성녹음을 수행하며 결과를 단순 저장과 같은 동작에 활용할 수는 있지만 복잡한 작업을 하기는 어려움이 있다.  
그럴 때에는 바로 **`Web Audio Api`** 를 사용한다.  

<br>

## Web Audio Api 활용

다음은 Web Audio Api를 활용한 예제이다. MediaRecorder에 stream을 연결하는 대신  **`AudioContext`** 를 활용하여 stream을 연결 후 Audio노드를 생성하여 녹음을 수행한다.

AudioContext와 여러 Audio노드에 접근하며 다양한 오디오 신호처리를 할 수 있다. 예를 들어 필터링, 이펙트 추가, 볼륨 조절 등이 있다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Web Audio API를 사용한 오디오 녹음</title>
</head>
<body>
<button id="startButton">녹음 시작</button>
<button id="stopButton" disabled>녹음 중지</button>
<audio id="audioElement" controls></audio>

<script>
let audioContext;
let mediaRecorder;
let audioChunks = [];

const startButton = document.getElementById('startButton');
const stopButton = document.getElementById('stopButton');
const audioElement = document.getElementById('audioElement');

startButton.addEventListener('click', startRecording);
stopButton.addEventListener('click', stopRecording);

async function startRecording() {
    try {
        audioContext = new (window.AudioContext || window.webkitAudioContext)();
        const mediaStream = await navigator.mediaDevices.getUserMedia({ audio: true });
        
        // 마이크 입력을 오디오 컨텍스트에 연결
        const source = audioContext.createMediaStreamSource(mediaStream);
        
        // 스트림에서 오디오를 처리하기 위해 녹음 시작
        mediaRecorder = audioContext.createScriptProcessor(4096, 1, 1);
        mediaRecorder.onaudioprocess = function(event) {
            const inputData = event.inputBuffer.getChannelData(0);
            // 여기서 오디오 데이터를 처리하거나 저장할 수 있습니다.
            audioChunks.push(new Float32Array(inputData));
        };
        
        source.connect(mediaRecorder);
        mediaRecorder.connect(audioContext.destination);
        
        startButton.disabled = true;
        stopButton.disabled = false;
    } catch (error) {
        console.error('Error accessing the microphone:', error);
    }
}

function stopRecording() {
    mediaRecorder.disconnect();
    
    // 녹음된 오디오를 복원
    const audioBuffer = audioContext.createBuffer(1, audioChunks.length * 4096, audioContext.sampleRate);
    const channelData = audioBuffer.getChannelData(0);
    let offset = 0;
    audioChunks.forEach(chunk => {
        channelData.set(chunk, offset);
        offset += chunk.length;
    });
    
    // 녹음된 오디오를 재생
    const audioSource = audioContext.createBufferSource();
    audioSource.buffer = audioBuffer;
    audioSource.connect(audioContext.destination);
    audioSource.start();
    
    // 녹음된 오디오를 다운로드 가능한 파일로 변환
    const audioBlob = bufferToWave(audioBuffer);
    const audioURL = URL.createObjectURL(audioBlob);
    audioElement.src = audioURL;
    
    startButton.disabled = false;
    stopButton.disabled = true;
}

// AudioBuffer를 WAV 파일로 변환하는 함수
function bufferToWave(abuffer) {
    ...
}
</script>
</body>
</html>
```

<br>

위 코드를 보면 다음과 같다.

1. WebAPI **`navigator.mediaDevices.getUserMedia`** 를 호출하여 마이크의 Stream을 얻어온다.
2. 얻어진 **`stream`** 을 이용하여 **`AudioContext`** 와 연결한다.
3. **`createScriptProcessor`** 를 통해 녹음을 시작한다.
4. 노드에 음성버퍼 이벤트 수신부 **`ondataavailable`** 를 구현한다.
5. **`connect`** 를 하여 각 노드간 연결 시킨다. 
6. 녹음 시작/중지 함수를 구현한다. 

<br>

이렇듯 MediaRecorder에서 간편하게 호출 가능한 로직상 기능들을 Web Audio Api의 AudioContext를 활용하면 직접 구현해주어야 하는 부분들이 많다.  
그러나 복잡한 작업이나 세밀한 작업, 서비스가 음성과 관련한 다양한 기능들을 제공해야할 때에는 적합한 방법이 될 수 있다.

<br>

## WebWorker 활용

위의 MediaRecorder를 사용하거나 Web Audio Api 이용할 때, 일반적인 경우 아무런 문제가 발생하지 않는다.  
하지만 웹 서비스의 복잡도가 커지거나 페이지 작업량이 많아지게 되면 클라이언트에서의 성능상 이슈가 발생한다.  
예를 들면, 버벅이거나 녹음 중 음성이 손실되는 경우 등이 있다. 이러한 문제가 발생하는 원인은 무엇일까?

그 원인은 쓰레드이다.  
우선 웹에서의 쓰레드 처리 내용을 이전 포스팅 참고하길 바란다.

- 참고사항: [https://hwanine.github.io/javascript/JsWithMultiThread/](https://hwanine.github.io/javascript/JsWithMultiThread/)

![Alt text](/assets/images/240106_3.png)

<br>

일반적으로 녹음을 할 때에는 UI를 처리하는 JS쓰레드와 녹음을 처리하는 Web Api의 브라우저쓰레드로 나뉘어 동작하기 때문에 엄청 복잡한 작업이 아니면 큰 문제는 없다.  
하지만 그러한 엄청 복잡한 작업을 처리해야 할 때에는 문제가 발생하며 이 때, Web Worker를 사용하면 성능향상을 경험할 수 있다.

Web Worker를 이용하면 JS뿐만 아니라 브라우저의 메인쓰레드도 차단하지 않는 완전 독립된 쓰레드에서 동작되며 그렇기 때문에 작업을 빠르게 수행할 수 있다.

이를 위해 보통 Web Audio Api를 이용할 때에는 **`Recorder.js`** 라이브러리를 사용하며 편리하게 사용할 수 있다.  
해당 라이브러리는 **Recorder.js**와 **RecorderWorker.js**를 프로젝트에 포함하여 쉽게 활용할 수 있다.
- [https://github.com/mattdiamond/Recorderjs](https://github.com/mattdiamond/Recorderjs)

이번 예제에서는 MediaRecorder를 활용한 방식으로 간단하게 Web Worker를 구현해보았다.

- recorder.js

```javascript
let worker = new Worker('worker.js');

// 워커로부터 메시지 수신
worker.onmessage = function(event) {
    console.log('녹음된 오디오 데이터 받음');
    let audioBlob = event.data.audioBlob;
    let audioURL = URL.createObjectURL(audioBlob);
    let audioElement = document.getElementById('audioElement');
    audioElement.src = audioURL;
};

// 녹음 시작 요청
function startRecording() {
    console.log('녹음 시작');
    worker.postMessage({ command: 'startRecording' });
}

// 녹음 중지 요청
function stopRecording() {
    console.log('녹음 중지');
    worker.postMessage({ command: 'stopRecording' });
}
```

<br>

- worker.js

```javascript
let mediaRecorder;
let audioChunks = [];

self.onmessage = function(event) {
    if (event.data.command === 'startRecording') {
        startRecording();
    } else if (event.data.command === 'stopRecording') {
        stopRecording();
    }
};

async function startRecording() {
    const audioStream = await navigator.mediaDevices.getUserMedia({ audio: true });
    mediaRecorder = new MediaRecorder(audioStream);
    
    mediaRecorder.ondataavailable = function(event) {
        audioChunks.push(event.data);
    };

    mediaRecorder.onstop = function() {
        const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
        self.postMessage({ audioBlob: audioBlob });
    };

    mediaRecorder.start();
}

function stopRecording() {
    mediaRecorder.stop();
}
```

<br>

위 코드를 보면 다음과 같다.

1. worker 객체를 생성하여 **`worker.onmessage`** 수신부를 구현한다
2. **`postMessage()`** 를 호출하며 동작을 처리한다(startRecording).
3. worker에서 WebAPI **`navigator.mediaDevices.getUserMedia`** 를 호출하여 마이크의 Stream을 얻어온다.
4. 얻어진 **`stream`** 을 **`MediaRecorder`** 의 생성자로 입력하여 객체를 생성한다.
5. **`mediaRecorder`** 에 음성버퍼 이벤트 수신부 **`ondataavailable`** 를 구현한다.
6. worker에서 **`postMessage()`** 를 호출하면 recorder.js의 onmessage가 호출되며 음성버퍼가 송신된다.

<br>

이렇게 워커와의 통신은 **`postMessage`**, **`onmessage`** 로 동작하게 되며 복잡한 작업을 처리할 때에 성능향상의 결과를 얻을 수 있다. 

<br>

## AudioWorklet 활용

웹에서 녹음을 하면서 Web Worker를 사용한다고 하더라고 성능상의 이슈는 언제나 곁에서 발생한다. 녹음부와 UI를 쓰레드로 구분하더라도 결국 최종 처리는 싱글쓰레드인 JS에서 적용해야하기 때문이다.  
그렇다면 더 좋은 방법은 없을까?

**`AudioWorklet`** 을 사용하는 것이다.  
AudioWorklet은 Web Audio API의 한 부분으로, 실시간 오디오 신호 처리를 위한 고성능 방식을 제공한다. 이것은 기존의 `ScriptProcessorNode`와는 다르게, 브라우저의 오디오 스레드에서 별도로 실행된다. 이는 브라우저의 메인 스레드와 분리되어 있어서 오디오 처리가 브라우저의 렌더링이나 사용자 인터랙션을 방해하지 않는다.

사실 AudioWorklet과 Web Worker 방식은 둘 다 백그라운드 스레드에서 동작하는 방식이지만 Web Worker가 모든 종류의 계산 집약적인 작업을 위한 쓰레드라면, AudioWorklet는 오디오 신호 처리만을 위한 특별한 쓰레드이다.

- Web Worker는 다양한 종류의 작업을 수행하지만 AudioWorklet는 오디오 신호 처리만을 위한 쓰레드로 오디오 작업이 최적화 및 특화 되어있다.
- Web Worker는 메인쓰레드와 워커쓰레드간 통신을 하지만, AudioWorklet는 오디오쓰레드와 워커쓰레드간 통신을 하기 때문에 오버헤드가 더 적을 수 있다는 이점이 있다.

다음은 AudioWorklet를 활용한 예제이다.

<br>

- main.js

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AudioWorklet 녹음 예제</title>
</head>
<body>
  <button id="startButton">녹음 시작</button>
  <button id="stopButton">녹음 중지</button>
  <button id="playButton">녹음된 오디오 재생</button>

  <script src="main.js">

// 녹음 버튼과 재생 버튼의 DOM 요소를 가져옴
const startButton = document.getElementById('startButton');
const stopButton = document.getElementById('stopButton');
const playButton = document.getElementById('playButton');

// 오디오 컨텍스트 생성
const audioContext = new AudioContext();

// 오디오 워크렛 생성
audioContext.audioWorklet.addModule('recorder-worklet.js')
  .then(() => {
    const recorderNode = new AudioWorkletNode(audioContext, 'recorder-worklet');

    // 녹음된 오디오 데이터 저장 배열
    let recordedChunks = [];

    // 녹음 시작
    startButton.addEventListener('click', () => {
      recordedChunks = []; // 녹음된 오디오 데이터 초기화
      recorderNode.port.onmessage = ({ data }) => {
        recordedChunks.push(...data); // 녹음된 오디오 데이터를 배열에 추가
      };
      recorderNode.connect(audioContext.destination);
    });

    // 녹음 중지
    stopButton.addEventListener('click', () => {
      recorderNode.disconnect();
    });

    // 녹음된 오디오 재생
    playButton.addEventListener('click', () => {
      const audioBlob = new Blob(recordedChunks, { type: 'audio/wav' });
      const audioURL = URL.createObjectURL(audioBlob);
      const audioElement = new Audio(audioURL);
      audioElement.play();
    });
  });

  </script>
</body>
</html>
```

<br>

- recorder-worklet.js

```javascript
class RecorderWorkletProcessor extends AudioWorkletProcessor {
  constructor() {
    super();
    this.recordedChunks = [];
  }

  process(inputs, outputs, parameters) {
    const input = inputs[0];
    this.recordedChunks.push(new Float32Array(input[0]));
    return true;
  }

  // 녹음된 오디오 데이터를 반환하는 함수
  getRecordedChunks() {
    return this.recordedChunks;
  }
}

registerProcessor('recorder-worklet', RecorderWorkletProcessor);
```

<br>

위 코드를 보면 다음과 같다.

1. **`AudioContext`** 객체를 생선한다.
2. audioContext에서 **`audioWorklet`** 모듈을 생성한다. 
3. Worklet 노드를 생성하고 음성버퍼 이벤트 수신부 **`onmessage `** 를 구현한다.
4. **`connect`** 를 하여 노드를 연결시켜 녹음을 시작하고 **`disconnect`** 하여 녹음을 중지한다.

<br>

위 코드처럼 AudioWorklet 방식은 생각보다 쉽게 구현하고 그만큼 강력한 성능을 가진다. Web Worker 대비 큰 성능 향상은 없을지 모르지만, AudioWorklet을 사용해야하는 가장 큰 이유는 Web Audio ApI의 ScriptProcessorNode가 deprecated 되었기 때문이다.

잘 운영되던 서비스가 언제 어떻게 특정 브라우저에서 정상동작 되지 않을 수도 있다는 말이다.  
그렇다고 복잡한 서비스의 경우 MediaRecorder를 사용할 수도 없는 노릇이기에 더더욱 AudioWorklet 사용이 권장되고있다.

<br>

## 정리

지금까지 Web에서 음성 녹음을 수행하는 방법들에 대해 알아보았다.
웹에서의 이러한 기술들도 꾸준히 발전해왔으나, 본인이 음성 관련 서비스를 개발하면서 느낀점은 여전히 웹에서 제공하는 기능은 Native에 비해 많이 부족하다는 것이다.  

Web시장이 더욱 커져가고 상대적으로 기존의 Native환경은 조금씩 좁아지고있는 실정이라고는 하지만, 서비스의 복잡도와 작업량 규모가 커질수록 웹에서의 서비스는 모든 부분을 커버하지는 못하며 아직까지는 한계가 드러나는 것 같다.

웹 기반 서비스가 편리하고 사용성 측면에서 이점이 많지만, 개발자 측면에서는 더욱 브라우저에 의존적이게 되면 고객과의 신뢰성을 조금씩 보장하기 어려워지지 않을까 생각한다.

<br>

## 참고자료

- [https://curryyou.tistory.com/446](https://curryyou.tistory.com/446)
- [https://pks2974.medium.com/web-audio-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0-952a19d6aa45](https://pks2974.medium.com/web-audio-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0-952a19d6aa45)