---
title: 01.시스템보안 - 02.컴퓨터 구조
date: '2020-10-23 16:00:00'
tags:
- 정보보안기사
- 시스템 보안
- 컴퓨터 구조
related: true
categories:
- IS_Certification
toc: true
---

# CPU (속도 빠름, 고가) - 연산처리

## 1. CPU 구조

![CPU Fetch Slow](/assets/images/posts/cpu_fetch_execute_slow.gif)

[이미지 출처](https://www.doc.ic.ac.uk/~eedwards/compsys/cpu/)

| 구성요소 | 상세 기능 |
|:---|:---|
|**ALU(연산장치)**|⦁ 각종 산술연산들과 논리연산들을 수행하는 회로<br/>⦁ 산술연산 : +, -, \*, /<br/>⦁ 논리연산 : AND, OR, NOT, XOR|
|**Register(레지스터)**|⦁ CPU 내부의 소규모 데이터나 중간 결과를 일시적으로 기억해 두는 고속의 전용 영역<br/>⦁ 컴퓨터 기억장치 중 Access 속도가 가장 빠름|
|**Control Unit(제어장치)**|프로그램 코드(명령어)를 해석하고, 그것을 실행하기 위한 제어 신호들(Control Signals)을 발생시킴|
|**내부 CPU 버스**|ALU와 레지스터 간의 데이터 이동을 위한 경로|

## 2. Register

|종류|내용|
|:---|:---|
|**PC(Program Counter)**|다음에 수행할 명령어가 저장된 주기억장치의 번지를 지정|
|**MAR(Memory Address Register)**|주기억장치에 접근하기 위한 주기억장치의 번지를 기억|
|**MBR(Memory Buffer Register)**|주기억장치에 입/출력할 자료를 기억하는 레지스터|
|**IR(Instruction Register)**|주기억장치에서 인출한 명령코드를 기억하는 레지스터|

## 3. 버스 (전송 라인) 

|종류|내용|
|:---|:---|
|데이터 버스(Data Bus)|시스템 컴포넌트 간 처리 데이터를 전송하기 위한 용도|
|주소 버스(Address Bus)|⦁ 기억장소의 위치 또는 장치 식별을 지정하기 위한 라인<br/>⦁ 라인의 비트 수에 따라 접속될 수 있는 장치의 용량이 결정됨|
|제어 버스(Control Bus)|CPU와 기억장치 또는 I/O 장치 사이의 제어 신호를 전송하는 라인|

## 4. Instruction Cycle

|단계|동작설명|
|:---|:---|
|**인출(Instruction Fetch)**|인출단계는 메모리(Memory)에서 데이터를 로드(Load)하여 CPU에 있는 레지스터(Register)에 적재하는 과정|
|**간접(Indirect)**|⦁ 메모리를 참조할 때 간접주소 방식을 사용하는 경우에 실행<br/>⦁ 간접주소란 CPU가 메모리를 참조했을 때 데이터가 존재하는 것이 아니라 메모리에 주소가 존재하여 메모리 내에서 한 번 더 조회해서 데이터를 얻는 것|
|**실행(Execution)**|명령과 데이터로 CPU가 산술 및 논리연산을 수행하는 것|
|**인터럽트(Inturrupt)**|⦁ 컴퓨터 작동 중 예기치 않은 문제가 발생한 경우라도 업무 처리가 계속될 수 있도록 하는 컴퓨터 운영체제의 한 기능으로, 크게 하드웨어 인터럽트와 소프트웨어 인터럽트로 나눔<br/>⦁ SVC 하드웨어 인터럽트 : 기계착오 인터럽트, 외부 인터럽트, 입출력 인터럽트, 프로그램 검사 인터럽트<br/>⦁ 소프트웨어 인터럽트 : CPU 내부에서 자신이 실행한 명령이나 CPU의 명령 실행에 관련된 모듈이 변화하는 경우 발생|


# 메모리 - 임시 기억

## 1. 캐시 메모리

+ CPU와 Memory 속도 차이 해결
+ 캐시사상 방법
    + **직접사상**
        * 메인 메모리를 분할하여 Cache 슬롯과 매핑
        * 장점 : 매핑 절차가 단순하고 신속하게 처리
        * 단점 : 높은 캐시 미스율 (같으 블록에 사상되는 데이터 캐시 적재 시 교체 발생)
    + **연관사상(가장 빠름)**
        * 메인 메모리의 각 블록이 Cache 어느 슬롯이든 적재 가능
        * 장점 : 지역성 높은 접근 시 캐시 적중률 높음
        * 단점 : 구현 하드웨어가 복잡하여 구현 비용 상승
    + **집합연관사상**
        * 직접/연관 절충안으로 메인메모리 구역을 셋으로 묶어 Cache 슬롯과 매핑
        * 장점 : 직접사상과 연관사상의 장점 수용
        * 단점 : 캐시 Fin/Fout 발생 증가. 구현 비용이 많이 듦.
+ 교체기법 :

    | 종류 | 세부 내용 | 특징 |
    |:---|:---|:---|
    | **Random** | 교체될 page 를 임의 선정 | overhead 적음 |
    | **FIFO <br/>(First in First Out)** | 캐시 내에 오래 있었던 page 교체 | 자주 사용되는 page가 교체될 우려 |
    | **LFU <br/>(Least Frequently Used)** | 사용 횟수가 가장 적은 page 교체 | 최근 적재된 page가 교체될 우려 |
    | **LRU <br/>(Least Recently Used)** | 가장 오랫동안 사용되지 않은 page 교체 | time stamping overhead 존재 |
    | **Optimal** | 향후 가장 참조되지 않을 page 교체 | 실현 불가능 |
    | **NUR <br/>(Not Used Recently)** | 참조비트, 수정비트로 미사용 page 교체 | 최근 사용되지 않은 page 교체 |
    | **SCR <br/>(Second Chance Replacement)** | 최초 참조비트 1로 셋, 1인 경우 0으로 셋, 0인 경우 교체 | 기회를 한번 더 줌 |

+ 페이지 교체 관리 시 문제점 :
    
    |문제점|세부 내용|
    |:---|:---|
    |Page Fault 발생<br/>(페이지 부재)|기억장치에 적재되지 않은 Page를 사용하려 할 때 Page Fault 발생|
    |Demand Paging<br/>(요구 페이징)|요구될 때에만 Process가 Page를 적재하는 방식|
    |Thrashing 발생<br/>(스레싱)|Page 부재가 너무 빈번하게 발생하여 CPU가 Process 수행보다 Page 교체에 더 많은 시간을 소요하는 비정상적인 현상|

+ 페이지 부재 예방

    | 종류 | 세부 내용 |
    |:---|:---|
    |**Load Control**|일정 시간 동안 새로운 프로세서가 생성되는 것을 지연시키고 Suspend Queue에 대기시켜서 Thrashing 현상을 감소시킴|
    |**Locality(구역성)**|시간과 공간 지역성을 집중적으로 참조함|
    |**Working Set(워킹셋)**|일정 시간 동안 참조되는 페이지 집합(Working Set)을 주기억장치에 유지|
    |**PFF(Page Fault Frequency)**|⦁ Process의 Page Fault 빈도에 따라 Residence set을 조정<br/>⦁ PFF가 높으면 Residence set의 크기 증가, 낮으면 감소|

+ 캐시 메모리 일관성 (Cache Coherence)
    * 캐시 메모리 일관성 유지 방식
        - 멀티프로세서 환경에서 각 프로세서가 캐시를 보유하며 캐시에 로드된 데이터를 변경한 경우 주기억장치의 데이터와 동일하게 유지하는 메커니즘
    * Write through
        - CPU가 캐시 메모리와 주기억장치를 동시에 수정
    * Write back
        - 캐시 메모리만 수정 후 나중에 일괄적으로 주 기억장치를 수정


## 2. 가상 메모리 

+ 가상 메모리의 개념
    * 보조기억장치를 주기억장치처럼 사용하여 주기억장치의 공간을 확대
+ 가상 메모리 관리 단위
    * **페이지(page)** : 가상 기억장치 상에서 동일한 크기의 최소 논리 분할 단위로 나눈 것
    * **세그먼트(segment)** : 사용자 주소 공간을 용도별로 논리적 단위로 나눈 것

        | 구분 | **Paging 기법** | **Segment 기법** |
        |:---:|:---|:---|
        |할당|고정(Static) 분할|가변(Dynamic) 분할|
        |적재|요구 Page만 일부 적재(On-demand)|프로그램 전체 적재(On-demand)|
        |관점|메모리 관리 측면|파일 관리 측면|
        |장점|⦁ 요구 Page만 적재 Load<br/>⦁ 외부 단편화 해결<br/>⦁ 교체시간 최소|⦁ 사용자 관점<br/>⦁ 개발/프로그래밍 용이<br/>⦁ 내부 단편화 해결<br/>⦁ 코드, 데이터 공유 용이|
        |단점|⦁ **내부 단편화(Fragmentation) 발생**<br/>⦁ Thrashing, 잦은 디스크 I/O 유발|⦁ **외부 단편화 심각**<br/>⦁ 메인 메모리가 커야 함|

        + 내부 단편화 : 블록 내 할당 공간을 모두 사용하지 않고 빈 공간이 생김
        + 외부 단편화 : 블록과 블록사이의 빈 공간이 프로그램보다 작아 활용할 수 없음

+ 가상 메모리 관리 정책

    |종류|세부 내용|기법의 유형|
    |:---|:---|:---|
    |**할당 기법**<br/>(Allocation)|프로세스에게 할당되는 메모리 블록의 단위를 결정|고정 할당, 가변 할당, Paging, Segmentation|
    |**호출 기법**<br/>(Fetch Policy)|보조기억장치에서 주기억장치로 적재할 시점 결정|Demand Fetch, Pre Fetch|
    |**배치 기법**<br/>(Placement)|요구된 페이지를 주기억장치의 어느 곳에 적재할 것인지를 결정|First fit, Best fit, Next fit, Worst fit|
    |**교체 기법**<br/>(Replacement)|주기억장치 공간 부족 시 교체 대상 결정|Random, FIFO, LRU, LFU, NUR, SCR, Optimal|

+ 할당 정책(Allocation Policy)

    |구분|종류|세부 내용|
    |:---:|:---:|:---|
    |연속 할당|고정 분할|⦁ 고정된 크기의 단위로 메모리 할당<br/>⦁ 내부 단편화 발생|
    ||가변 분할|⦁ 할당 단위를 요청마다 다른 크기로 할당<br/>⦁ 외부 단편화 발생|
    |비연속 할당|Paging|가상 메모리 블록을 페이지 단위 관리. TLB와 MMU, Page table로 관리|
    ||Segmentation|가변 크기인 세그먼트로 분할, Segment Table로 관리|

# I/O  인터페이스

## 1. I/O Interface

* CPU 경유 유무에 따른 분류
    - CPU 경유 : 프로그램에 의한 I/O, 인터럽트에 의한 I/O
        + 프로그램에 의한 I/O
            * 컴퓨터 메모리에 기록된 입출력 명령에 의해 수행
            * CPU가 주변장치를 연속 감시하는 Polling 방식
            * 프로세서의 시간을 낭비하고 처리 효율이 낮음
        + 인터럽트에 의한 I/O
            * CPU가 주변 장치들의 데이터 전송을 위한 인터럽트 요청을 감지하면 수행 중이던 작업을 중단하고 데이터 전송을 처리하기 위해 서브루틴으로 분기하여 전송을 수행
    - CPU 비경유 : **DMA (Direct Memory Access Controller) Channel I/O**
        + CPU의 개입 없이 I/O 장치와 기억장치 사이의 데이터를 전송하는 방법
        + 속도가 빠르다.
* DMA 동작 방식
    * **Cycle Stealing** : DMA 제어기와 CPU가 BUS 를 공유하며, CPU가 BUS를 사용하지 않는 사이클에만 접근한다. CPU보다 높은 우선순위를 가진다.
    * **Burst Mode** : DMA 제어기가 BUS를 점유하며, 동작 완료 후 BUS의 점유를 해제한다.

## 2. I/O Processor

* 채널에 의한 입출력
    - **Selector 채널** : 한 번에 한 개씩 데이터를 주기억장치에게 전송
    - **Multiplexer 채널** : 동시에 많은 데이터를 전송
        + Byte Multiplexer
        + Block Multiplexer
    - I/O 장치의 다양함, 복잡함 
        + CPU나 DMA 대신 독립된 입출력 프로세서인 채널이 입출력을 담당
        + 채널이 입출력을 수행하는 동안 CPU는 다른 작업을 처리해 효율 향상

* I/O Processor의 종류
    - **Multiplexer Channel**
        + 저속장치(Printer, Serial 등) 연결
        + 시분할 방식으로 Byte 단위 전송
    - **Selector Channel**
        + 고속장치(Disk, CDROM), 단일 입출력만 가능
        + Burst Mode 동작
    - **Block Multiplexer Channel**
        + Hybrid 모드
        + 동시에 여러 I/O 처리 (블록 단위)
    - **Byte Multiplexer Channel**
        + 한 개의 채널에 여러 개의 입출력 장치를 연결하여 시분할 공유하는 방식
        + 저속 입출력 방식