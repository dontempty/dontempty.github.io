---
title: OS - 멀티스레드와 멀티프로세스
author: yoongyoonge
date: 2023-10-16 20:30:00 +0900
categories: [Data Engineering, OS]
tags: [study, os, computer, multithread, multiprocessing]
render_with_liquid: false
---

> 멀티스레드와 멀티프로세스의 차이를 알아보도록한다.


## 1. Multi Thread(멀티스레드)
- 스레드는 하나의 프로세스 내에 있는 실행 흐름으로 멀티 스레드는 하나의 프로세스 안에 여러개의 스레드가 있는 것을 말함
- 하나의 프로그램에서 두 가지 이상의 동작을 동시에 처리하도록 하는 행위가 가능해짐
- 멀티 프로세스가 웹 브라우저에서 여러 탭이나 여러 창이라고 한다면 멀티 스레드는 웹 브라우저의 단일 탭 또는 창 내에서 브라우저 이벤트 루프, 네트워크 처리, I/O 및 기타 작업을 관리하고 처리하는데 사용

### 장점
- 스레드는 프로세스보다 가벼움
    - 스레드는 프로세스 내에서 생성되기 때문에 스레드의 실행 환경을 설정하는 작업이 매우 간단하여 생성 및 종료가 빠름
    - 코드, 데이터, 스택 영역을 제외한 나머지 자원을 서로 공유하기 때문에 기본 내장 데이터 용량이 프로세스보다 작음

- 자원의 효율성
    - 하나의 프로세스 내에서 여러 개의 스레드를 생성하기 때문에 heap 영역과 같은 공유 메모리에 대해 스레드 간에 자원을 공유하는 것이 가능
    <img width="766" alt="image" src="https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/bdd352c0-7859-4e51-99fb-740d7fe6c094">

- Context Switching 비용 감소
    - 프로세스 컨텍스트 스위칭이 스위칭 할 때마다 CPU 캐시에 있는 내용을 모두 초기화하고, 새로운 프로세스 정보를 CPU 캐시에 적재해야 하므로 높은 비용이 드는 반면 스레드 컨텍스트 스위칭은 공유 자원을 제외한 스레드 정보(stack, register)만을 교체하면 되므로 비용이 상대적으로 낮음

### 단점
- 안정성
    - 멀티스레드 모델은 하나의 스레드에서 문제가 발생하면 다른 스레드들도 영향을 맏아 전체 프로그램이 종료될 수 있음
    - 적절한 예외처리를 잘 해둔다거나 에러 발생 시 새로운 스레드를 생성하거나 스레드 풀에서 잔여 스레드를 가져오는 개발을 통해 프로그램 종료를 방지할 수 있음

- 동기화로 인한 성능 저하
    - 여러 개의 스레드가 공유 자원에 동시 접근할 수 있기 때문에 동기화 문제가 발생할 수 있음
    - 스레드 간 동기화는 데이터 접근을 제어하기 위한 필수적인 기술

- 데드락(교착 상태)
    - 다수의 프로세스나 스레드가 서로 자원을 점유하고, 다른 프로세스나 스레드가 점유한 자원을 기다리는 상황에서 발생하는 교착 상태
    - 여러 개의 스레드가 서로 대기하면서 무한하게 기다리게 되는 무한 루프와 같은 증상

- Context Switching Overhead
    - 멀티 프로세스보다 멀티 스레드의 컨텐스트 스위칭 오버헤드가 작지만 스레드 수가 많으면 많을 수록 그만큼 컨텍스트 스위칭이 많이 발생되게 되고 이는 당연히 성능 저하로 이어짐

- 디버깅이 어려움
    - 코드를 디버깅하는 도중 다른 스레드가 실행되어 예기치 않은 결과가 발생하거나 어떤 스레드가 어떤 자원에 접근하는지 순서는 어떤지 파악하기 어려울 수 있음
    - 스레드 간의 상호작용과 동기화 기법을 잘 이해하고, 디버깅 도구를 잘 활용해야 함


## 2. Multi Process(멀티프로세스)
- 운영체제에서 하나의 응용 프로그램에 대해 동시에 여러 개의 프로세스를 실행
- 하나의 부모 프로세스가 여러 개의 자식 프로세스를 생성

<img width="511" alt="image" src="https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/b11da8fc-7d57-48cb-ac06-5506e7f040f6">

### 장점
- 프로그램 안전성
    - 각 프로세스가 독립적인 메모리 공간을 가지므로, 한 프로세스가 비정상적으로 종료되어도 다른 프로세스에 영향을 주지 않음
- 프로그램 병렬성
    - 멀티 프로세스와 여러개의 CPU 코어를 활용하여 둘의 시너지를 합쳐, 다중 CPU 시스템에서 각 프로세스를 병렬적으로 실행하여 성능을 향상 시킬 수 있음
- 시스템 확장성
    - 멀티 프로세스는 각 프로세스가 독립적이므로, 새로운 기능이나 모듈을 추거하거나 수정할때 다른 프로세스에 영향을 주지 않아 시스템의 규모를 쉽게 확장할 수 있음

### 단점
- Context Switching Overhead
    - 프로세스를 컨텍스트 스위칭 하면 CPU는 다음 프로세스의 정보를 불러오기 위해 메모리를 검색하고 CPU 캐시 메모리를 초기화하며, 프로세스 상태를 저장하고, 불러올 데이터를 준비해야하기 때문에 비용 오버헤드가 발생할 수 있고 성능 저하가 올 수 있음
    <img width="766" alt="image" src="https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/29bebf08-9164-40a0-9c12-a10902f7f5f6">
    - 프로세스 수를 적정하게 유지하거나, I/O 바운드 작업이 많은 프로세스와 CPU 바운드 작업이 많은 프로세스를 분리하여 관리하고, CPU 캐시를 효율적으로 활용하는 등의 방법을 고려해 봐야 함

- 자원 공유 비효율성
    - 프로세스가 독립적인 메모리 공간을 가지므로, 결과적으로 메모리 사용량이 증가
    - 프로세스간 자원공유 시 복잡한 통신 기법 필요


### 참고그림
<img width="858" alt="image" src="https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/f5d2f9fd-e260-4ad6-ae95-a90deb9b71e5">

<br>

#### 참고자료 <br>

[멀티 프로세스 vs 멀티 스레드 비교 완전 총정리](https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-multi-process-multi-thread) <br>

[완전히 정복하는 프로세스 vs 스레드 개념](https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%E2%9A%94%EF%B8%8F-%EC%93%B0%EB%A0%88%EB%93%9C-%EC%B0%A8%EC%9D%B4)

!) 위 블로그에 진짜 좋은 포스트 내용들이 많다!!