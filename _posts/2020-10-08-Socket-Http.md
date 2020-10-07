---
toc: true
toc_sticky: true
categories:
  - Network
title: 소켓 통신방식과 http 통신방식의 차이
tags: [Network, Socket, http]
excerpt: "서버와 통신을 할 경우 Http 통신과 Socket 통신이 있는데 어떤 차이가 있을까요?"
---

# Client - Server 통신

일반적으로 소프트웨어에서 사용하는 데이터들은 보통 서버에서 관리됩니다.

그럼 클라이언트에서 이 서버에 접속하여 데이터를 주고받게 되는데, 여기서 통신방식이 크게 Http 통신과 Socket 통신 2가지 방식이 있습니다.

<br>

## Http 통신

> *Client의 요청(Request)이 있을 때만 서버가 응답(Response)하여 해당 정보를 전송하고 곧바로 연결을 종료하는 방식*

<br>

`Http 통신`은 Client의 요청이 있을 때만 Server가 응답하여 처리를 하는 방식입니다.

즉, Client가 요청을 보내는 경우에만 Server가 응답하는 단방향적 통신으로, Server가 Client로 요청을 보낼수는 없습니다. 

그렇기 때문에 요청을 보낼때, 내용을 기다리는 시간과 함께 연결하는 시간이 들어가게 됩니다. 

이러한 Http 통신은 실시간 연결이 아닌, 필요한 경우에만 Server로 접근하는 콘텐츠 위주의 데이터를 사용할 때 용이합니다. 

만약 게시물에 대한 내용을 요청하기 위해 실시간으로 연결을 유지하는 Socket통신을 사용하게 되면, 게시물을 받은 후에도 계속 통신을 위한 연결이 성립되어 있어 부하가 걸리게 됩니다. 

일반적으로 필요한 경우에만 Server로 정보를 요청하는 경우가 많은데, 이러한 Web Server로 Http 통신을 주로 사용하며 비용 및 유지보수 등 대부분의 방면에서 좋습니다.

<br>

## Socket 통신

> *Server와 Client가 특정 Port를 통해 실시간으로 양방향 통신을 하는 방식*

<br>

`Socket통신`은 Http 통신과 달리 Server와 Client가 특정 Port를 통해 연결을 성립하고 있어 실시간으로 양방향 통신을 하는 방식입니다.

Client만 필요한 경우에 요청을 보내는 Http 통신과 달리 Socket 통신은 Server 역시 Client로 요청을 보낼 수 있으며, 계속 연결을 유지하는 연결지향형 통신이기 때문에 실시간 통신이 필요한 경우에 자주 사용됩니다.

예를 들면, 실시간 Streaming 중계나 실시간 채팅과 같이 즉각적으로 정보를 주고받는 경우에 사용합니다. 

만약 실시간 동영상 Streaming 서비스를 Http 통신으로 구현하였다고 가정해보면, 이러한 경우에 사용자가 서버로 동영상을 요청하기 위해서는 동영상이 종료되는 순간까지 계속해서 Http 통신을 보내야 하게 됩니다.

이러한 구조는 계속 연결을 요청하기 때문에 부하가 걸리게 됩니다. 

그러므로 이러한 경우에는 Socket을 통해 구현하는 것이 적합합니다.

<br>

## 결론

![image](https://user-images.githubusercontent.com/57826388/95358518-1ca49780-0904-11eb-9386-e4ecb76381d6.png)

### **Http 통신의특징**

- Client가 요청을 보내는 경우에만 Server가 응답하는 단방향 통신

- Server로부터 응답을 받은 후에는 연결이 바로 종료

- 실시간 연결이 아니고, 필요한 경우에만 Server로 요청을 보내는 상황에 유리

- 요청을 보내 Server의 응답을 기다리는 어플리케이션의 개발에 주로 사용

<br>

### **Socket 통신의특징**

- Server와 Client가 계속 연결을 유지하는 양방향 통신

- Server와 Client가 실시간으로 데이터를 주고받는 상황이 필요한 경우에 사용

- 실시간 동영상 Streaming이나 온라인 게임 등과 같은 경우에 자주 사용

<br>

## 참고자료

<https://mangkyu.tistory.com/48>
<http://www.secmem.org/blog/2019/08/17/websocket-socketio/>





