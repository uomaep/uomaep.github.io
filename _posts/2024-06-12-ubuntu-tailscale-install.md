---
title: "Ubuntu에 Tailscale 설치하기"
description: Ubuntu에 Tailscale 설치하여 안전하게 원격 접속하기
author: uomaep
categories: [Linux]
tags: [ubuntu, tailscale]
date: 2024-06-12
---

필자 지인 분들 중 우리 사이에서 `GOD`이라고 불리는 한 분이 계신다. 그 만큼 지식이 많고 좋은 정보를 항상 공유해주시기 때문에 굉장히 감사한 분이다. 아무튼 그 분께서 필자가 ssh 연결을 하기위해 22번 포트를 개방해둔 것을 보고 말씀하셨다. `well-known port`, `registered port`는 웬만하면 개방하지 말라고... 바로 보안 때문이었다. 세상엔 생각보다 신기한(~~위험한~~) 사람들이 많고 기본 포트에 대해서 공격 시도가 많이 들어온다고 한다. 필자는 다시 질문했다. *그럼 어떻게 인스턴스에 ssh 원격 접속을 해요?* 그러자 신께서 말씀하셨다.  

***tailscale** 쓰세용*  

## Tailscale

Tailscale은 [WireGuard](https://www.wireguard.com/)를 기반으로 한 VPN 서비스이다. 이 글에서는 인스턴스의 22번 포트를 닫아도 Tailscale 망을 통해서 안전하게 원격 접속하는 방법에 대해 알아볼 것이다. 또한 Tailscale의 기능 중 Subnet router 설정과 Exit Node 설정에 대해서도 얘기해 볼 것이다.  

### Tailscale 설치하기

먼저 [Tailscale 홈페이지](https://tailscale.com/)에 접속하여 회원가입을 한다.  

 ![screenshot-8218856](https://github.com/user-attachments/assets/a49d6376-31e1-4002-ab9a-f3160e2e9e72)

그 다음 자신의 기기(인스턴스 말고)의 운영체제에 맞는 걸로 다운로드한다.  

![screenshot-8218913](https://github.com/user-attachments/assets/68be9f84-973d-4ba7-8249-c1ed964ce79c)

필자는 맥북, 윈도우, 아이패드, 아이폰에 모두 Tailscale를 설치하여 사용 중이다. 설치가 끝나고 Tailscale을 사용할 기기에서 로그인을 하면 [Admin 콘솔](https://login.tailscale.com/admin/machines)에 아래와 같이 기기들을 볼 수 있다.  

![screenshot 2](https://github.com/user-attachments/assets/5fa3528d-7be3-4f11-87a8-ef8a88ff8711)

그 다음 제일 중요한 서버 인스턴스에도 Tailscale을 설치해야하는데, 일단 처음 접속할 땐 22번 포트를 열고 ssh 원격 접속을 해주어야 한다. 이 글을 보고 있는 독자들은 ssh 원격 접속 정도는 껌일 것이라 생각한다. 필자는 Ubuntu 인스턴스를 사용하기 때문에 Ubuntu 기준으로 설명하겠다.  

![screenshot-8219707](https://github.com/user-attachments/assets/dc2dfc61-d8be-4e6c-995d-70b711070ff3)

우선 Linux를 선택하고 밑으로 스크롤 하면 Ubuntu 22.04 LTS 에 대한 설치 메뉴얼이 있다.    

![screenshot-8219782](https://github.com/user-attachments/assets/dacb6bb1-a703-40bb-8aba-5209842310c2)

나와 있는 순서대로 명령어를 입력하면 된다. 여기서  **3번 과정** `sudo tailscale up` 을 하면 tailscale을 실행하고 아래와 같이 링크가 뜬다. 저 링크를 자신의 데스크탑(필자의 경우 맥북)에서 접속하자. 

![screenshot-8219950](https://github.com/user-attachments/assets/6015034c-7c1f-4bc0-8391-bd28571815b8)  

접속하면 Tailscale 로그인 창이 뜰 것이다. 로그인을 하면 연결 버튼이 아래와 같이 뜬다.  

![screenshot-8220173](https://github.com/user-attachments/assets/57a0c9cc-3f62-4b4e-89b4-fdf08b28ba16)

`Connect` 버튼을 누르면 성공적으로 Ubuntu 인스턴스가 Tailscale 계정에 등록될 것이다.  등록된 기기는 [Admin 콘솔](https://login.tailscale.com/admin/machines)에 접속하여 아래와 같이 확인할 수 있다.  

저기 나와있는 IP 주소들은 각 기기들의 Tailscale 내부 IP 주소이다. Tailscale 망으로 묶여 있기 때문에, 이 IP 주소들을 이용하여 서로 통신할 수 있다.

![screenshot 2](https://github.com/user-attachments/assets/5fa3528d-7be3-4f11-87a8-ef8a88ff8711)

  

**kr-1머신(필자의 ubuntu 인스턴스)** 의 상태를 보면 `Connected`로 정상적으로 연결되어있는 것을 확인할 수 있다. 그럼 이제 tailscale ip로 ssh 원격 접속을 해보자.  

![screenshot 3](https://github.com/user-attachments/assets/83ee13bf-55bf-4d0a-8c8e-83b022c74db6)필자의 맥북 `~/.ssh/config` 파일이다. `Host kr-1` 설정은 실제 인스턴스의 IP로 접속하는 설정이고, 그 밑에 `Host kr-1-ts` 설정은 tailscale IP로 접속하는 설정이다. HostName 필드 부분이 원격 접속할 대상의 IP이므로, `Host kr-1-ts`의 HostName 필드에 tailscale IP를 입력하면 된다.(tailscale IP는 [Admin 콘솔](https://login.tailscale.com/admin/machines)에서 확인 가능)  

한 번 tailscale IP로 ssh 원격 접속을 해볼까?  

```bash 
ssh kr-1-ts
```

![screenshot-8221132](https://github.com/user-attachments/assets/bd4268b5-8f47-499f-a225-b415bf5a1c5f)

정상적으로 연결되는 모습을 볼 수 있다. 이 글의 목적은 22번 포트를 닫고도 tailscale 망을 통해 원격 접속을 할 수 있도록 하는 것이 목적이었다.(현재는 22번 포트 개방되어 있음) 한 번 22번 포트를 닫고 다시 연결해보자.    

우선 원래 인스턴스의 IP로 접속 요청을 보내보자. `kr-1` 로 요청을 보내도 **time out**이 뜨는 것을 볼 수 있다.

![screenshot 4](https://github.com/user-attachments/assets/1bd77b28-c570-4138-a452-b24c25980e79)  

그 다음 tailscale IP로 접속 요청을 보내보자. `kr-1-ts` 로 요청을 보냈더니 정상적으로 원격 접속이 되었다. 이로써 22번 포트를 닫고도 안전하게 나만 사용할 수 있는 tailscale 망으로 ubuntu 인스턴스가 마치 내부망에 있는 것처럼 이용할 수 있게 되었다.

![](https://github.com/user-attachments/assets/87b4403a-6510-4f76-b62f-bd80bec81162)

## Subnet Router

서브넷 라우터는 Tailscale을 이용하여 서로 다른 서브넷에 있는 기기들이 서로 통신할 수 있도록 해준다. 아래 사진처럼 Tailscale에 연결된 기기가 게이트웨이 역할을 하여 다른 서브넷에 있는 기기들과 통신할 수 있도록 해주는 것이다.  

  

![screenshot-8223346](https://github.com/user-attachments/assets/8b256753-5787-4a75-93ae-906d44d08bf9)

  

아래 명령어는 리눅스 기기에서 서브넷 라우터를 활성화하는 명령어이다.  

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf

sudo tailscale up --advertise-routes=192.168.0.0/24
```

여기서는 192.168.0.0/24 대역으로 설정했다.  

그 다음 [Admin 콘솔](https://login.tailscale.com/admin/machines)에서 노드 설정의 `Edit route setting`을 클릭하여 라우팅을 허용해주면 된다.  

![screenshot-8222975](https://github.com/user-attachments/assets/367a0f75-0c5e-4816-989d-c15b419086c5)

![screenshot-8223949](https://github.com/user-attachments/assets/441addb2-64e5-487b-bc8f-f0e8cab7dfca)

이제 Tailscale에 연결된 상태로 192.168.0.0/24 대역에 접속을 하면 해당 노드를 통해 접속이 된다.  

## Exit Node

일반적인 VPN이라면 연결하는 순간 모든 트래픽이 VPN을 통해 나가게 되면서 공인 IP 또한 변경되게 된다. 하지만 Tailscale의 경우에는 Exit Node를 설정하지 않는다면 각 노드들의 통신만 Tailscale을 통해 이루어지게 된다. 외부의 공공 WiFi를 이용하거나, 공인 IP를 변경해야 하는 경우에 Exit Node를 사용해서 통신을 할 수 있다.  

그림으로 설명하면 아래와 같다.  

![screenshot-8222233](https://github.com/user-attachments/assets/83492284-8648-407c-9aed-fbfd75fdf652)

![screenshot-8222242](https://github.com/user-attachments/assets/05fcc7b5-5468-4ed7-b9d6-87d02c0a8144)

### Exit Node 설정하는 방법

[공식 설명 페이지](https://tailscale.com/kb/1103/exit-nodes)를 참조해도되지만, 필자가 설명하겠다.  

Exit Node 기능을 사용하려면 IP 포워딩을 활성화해야 한다고 한다. ubuntu 인스턴스에서 다음 명령을 입력해주자. 서브넷 설정을 할 때 밑에 명령을 입력했다면 생략해도된다.

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

그 다음 ubuntu 인스턴스를 exit node로서 사용할 수 있도록 다음 명령어를 입력하자.

``` bash
sudo tailscale set --advertise-exit-node
```

그 다음 [Admin 콘솔](https://login.tailscale.com/admin/machines)에서 노드의 설정에서  `Edit route settings` 을 클릭하여 exit node를 허용해주자.

![screenshot-8222975](https://github.com/user-attachments/assets/089f43db-3a88-440c-8d1e-a88c8ec89398)

![screenshot-8223064](https://github.com/user-attachments/assets/464cb982-fa57-4000-a3e0-8d26281f76ab)

Exit node가 활성화된 것을 볼 수 있고 `Use as exit node`를 체크하면 exit node로써 사용이 가능하다.  

이제 Exit Node를 통한 통신이 필요한 클라이언트에서 Exit Node를 사용하도록 설정하면 된다.

![screenshot](https://github.com/user-attachments/assets/b8c20789-7fa0-49aa-9995-505c721a549f)

## 마무리

필자의 경우 주변의 지인 분들이 좋은 정보를 공유해주셔서 이렇게 좋은 지식을 얻어갈 수 있는 것 같다. 앞으로도 `우맵의 공유 공간`에 도움이 될만한 정보들을 계속 올릴 수 있도록 해보겠다.