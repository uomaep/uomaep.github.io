---
title: "Ubuntu 현재 버전 확인 하기"
description: 명령어를 통해 ubuntu 현재 버전 확인하기
author: uomaep
categories: [Linux]
tags: [ubuntu]
date: 2024-06-12
---

ubuntu 인스턴스를 생성하고 처음 접속했을 때 버전이 알맞게 설치되었는지, 아니면 무슨 버전인지 궁금할 때가 있다. 버전마다 호환가능한 패키지가 있으므로 버전 확인은 거의 필수이다. 그렇지만 자주 쓰지 않는 명령어 중에 하나이므로 잘 까먹기도 한다. 필자도 매번 버전 확인을 할 때마다 구글링을 통해 명령어를 알아낸 다음 사용한다. 아무튼 ubuntu 버전 확인하는 방법을 같이 한 번 알아보자.  

  

### 명령어

- [ ] `cat /etc/issue`
- [ ] `lsb_release -a`
- [ ] `cat /etc/os-release`

  

## cat /etc/issue

`/etc/issue`에는 시스템 식별 세부정보가 기록되어 있다. `cat` 명령을 통해 파일 내용을 볼 수 있다.

![screenshot-8210770](https://github.com/user-attachments/assets/309f9bb2-3908-4fc7-a4b6-8a91adcaf18d)  

## lsb_release -a

Linux Standard Base(LSB) 정보를 보여준다. `/etc/issue` 보다 좀 더 자세한 정보가 나온다.

![screenshot-8211150](https://github.com/user-attachments/assets/6ea56440-2290-463c-b6f0-df09573a5855)  

## cat /etc/os-release

`/etc/os-release`에는 운영체제에 대한 세부 정보가 기록되어 있다. 참고로 이 방법은 `ubuntu 16.04` 이상 버전에서만 작동한다고 한다. `cat` 명령을 통해 볼 수 있다.

![screenshot-8211214](https://github.com/user-attachments/assets/41f0fe6a-755e-44f2-a2b6-273c6170929b)

## 마무리

버전을 확인할 수 있는 방법은 더 많을 수 있지만 그래도 이 정도면 충분히 버전 확인을 할 수 있지 않을까 싶다. 필자도 거의 `lsb_release` 명령만 사용하는 것 같다.