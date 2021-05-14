---
title: 정보보안 - 윈도우 시스템 구조
date: '2021-01-07 10:00:00'
tags:
- 정보보안기사
- 윈도우 시스템 구조
related: true
categories:
- IS_Certification
toc: true
---

# 윈도우 아키텍처
- **(Ring 구조) - 경량화된 운영체제, Plug In Play**
- 하드웨어 / HAL / 마이크로 커널 / 각종 관리자 / 응용 프로그램
![Win NT Kernel Architecture](/assets/images/posts/win_nt_kernel_architecture.png)

[이미지 출처](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT)

- 2중모드
    + 사용자모드
        * Application 에 문제생길 경우 프로그램 종료됨
        * Win32 서브시스템
            - (연계)POSIX : 유닉스 OS 공통 API (IEEE 표준)
            - (연계)OS/2 : IBM PS/2
        * NTVDM (NT Virtual Dos Machine) : 구형 DOS 사용가능
    + 커널모드
        * 커널 자체에 문제생길 경우 메모리덤프 후 재시작됨
        * 시스템 서비스
        * 마이크로 커널
        * HAL
        * 관리자
            - 입출력 관리자
            - 객체 관리자
            - 보안 참조 관리자
            - 프로세스 관리자
            - 로컬 프로시저 호출(LPC) 관리자
            - 가상 메모리 관리자
            - 기타 관리자
            - 그래픽 장치 관리자

|구성 내용|세부 내용|
|:---|:---|
|HAL<br/>(Hardware Abstraction Layer)|드라이버 개발자가 HAL 표준을 준수하면 하드웨어-소프트웨어 간 원할한 통신 가능|
|Micro Kernel|관리자 에게 역할을 분담시키고 하드웨어를 제어<br/>%systemRoot%\sysem32\ntoskernel.exe|
|IO Manager<br/>입출력관리자|시스템 입출력을 제어, 장치 드라이버 사이에서 메시지를 전달, 응용 프로그램이 하드웨어와 통신할 수 있는 통로를 제공|
|Object Manager<br/>객체관리자|파일, 포트, 프로세스, 스레드와 같은 각 객체에 대한 정보를 제공|
|Security Reference Manager<br/>보안 참조 관리자|데이터 및 시스템 자원의 제어를 허가/거부 함으로써 시스템의 보안설정을 책임짐|
|Process Manager<br/>프로세스 관리자|프로세스 및 스레드를 생성하고 요청에 따른 작업 처리|
|Local Procedure Call<br/>로컬 프로시저 호출관리자|프로세스는 서로의 메모리 공간을 침범하지 못하기 때문에 프로세스 간에 통신이 필요한 경우 이를 처리하는 장치|
|Virtual Memory Manager<br/>가상 메모리 관리자|응용 프로그램의 요청에 따라 RAM 메모리 할당, 가상 메모리의 Paging을 제어|
|Win32/64 Sub System|윈도우의 기본 서버 시스템, 32비트 및 64비트 응용 프로그램이 동작할 수 있도록 지원|
|POSIX|유닉스 운영체계에 기반을 두고 있는 일련의 표준 운영체계 인터페이스|
|Security Sub System|사용자가 로그인할 때 데이터를 보호하고 운영체제가 이를 제어할 수 있도록 만든 서브 시스템|

