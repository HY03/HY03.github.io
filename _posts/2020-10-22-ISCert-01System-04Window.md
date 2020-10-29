---
title: 01.시스템보안 - 04.윈도우 클라이언트 및 서버 보안
date: '2020-10-25 16:00:00'
tags:
- 정보보안기사
- 시스템 보안
- 윈도우 보안
related: true
categories:
- IS_Certification
toc: true
---

# 윈도우 아키텍처
- (Ring 구조) - 경량화된 운영체제, Plug In Play
- 하드웨어 / HAL / 마이크로 커널 / 각종 관리자 / 응용 프로그램
![Win NT Kernel Architecture](/assets/images/posts/win_nt_kernel_architecture.png)

[이미지 출처](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT)

|구성 내용|세부 내용|
|:---|:---|
|HAL<br/>(Hardware Abstraction Layer)|드라이버 개발자가 HAL 표준을 준수하면 하드웨어-시스템 간 원할한 통신 가능|
|Micro Kernel|Manager 에게 작업을 분담시키고 하드웨어를 제어|
|IO Manager|시스템 입출력을 제어, 장치 드라이버 사이에서 메시지를 전달, 응용 프로그램이 하드웨어와 통신할 수 있는 통로를 제공|
|Object Manager|파일, 포트, 프로세스, 스레드와 같은 각 객체에 대한 정보를 제공|
|Security Reference Manager|데이터 및 시스템 자원의 제어를 허가/거부 함으로써 시스템의 보안설정을 책임짐|
|Process Manager|프로세스 및 스레드를 생성하고 요청에 따른 작업 처리|
|Local Procedure Call|프로세스는 서로의 메모리 공간을 침범하지 못하기 때문에 프로세스 간에 통신이 필요한 경우 이를 처리하는 장치|
|Virtual Memory Manager|응용 프로그램의 요청에 따라 RAM 메모리 할당, 가상 메모리의 Paging을 제어|
|Win32/64 Sub System|윈도우의 기본 서버 시스템, 32비트 및 64비트 응용 프로그램이 동작할 수 있도록 지원|
|POSIX|유닉스 운영체계에 기반을 두고 있는 일련의 표준 운영체계 인터페이스|
|Security Sub System|사용자가 로그인할 때 데이터를 보호하고 운영체제가 이를 제어할 수 있도록 만든 서브 시스템|

# 윈도우 파일시스템
## 1. FAT16 (File Allocation Table)
+ 최대 디스크 2GB 지원
+ 암호화 및 압축이 불가능(접근 제어 불가)
+ 파일명 최대 길이는 영문 8자
+ 클러스터당 1632KB 할당, 내부단편화 발생

## 2. FAT32
- 최대 디스크 2TB 지원 (대용량 디스크 지원)
- 암호화 및 압축이 불가능(접근 제어 불가)
- 파티션 지원
- 파일명 최대 길이는 영문 256자
- 클러스터당 4KB 할당, 내부단편화를 줄임

## 3. **NTFS**
- 암호화(Encrypting File System:대칭키 기법) 및 압축 지원, 대용량 파일 시스템 지원
- 가변 클러스터 크기(512 ~ 64KB), 기본 값은 4KB
- 트랜잭션 로깅을 통한 복구/오류 수정이 가능
- Windows NT 이상에서 지원
- 저널링 기능 제공 (파일 시스템이 변경될 때 그 내용을 기록), 롤백가능
- ADS(Alternate Data Stream) : MAC 파일 시스템과 호환성을 위해 만든 공간
- Quotas : 사용자별 디스크 사용량 제한
- 대용량 지원 (2TB 이상)
    + Volume Boot Record
    + Master File Table
    + Data Area
- VBR(Volume Boot Record) : 부트섹터, 부트코드, NTLDR 위치 정보
- MFT(Master File Table) : NTFS의 메타정보, 파일 및 디렉터리 정보, 파일위치, 속성, 시간정보, 파일명, 크기 등의 정보

- Convert 로 FAT 에서 NTFS로 파일 시스템 변경
    + `CONVERT D: /FS:NTFS`

# 윈도우 프로세스 구조 (Message Driven)
- 키보드에서 발생시킨 이벤트를 윈도우 시스템에 전송하고 윈도우 시스템은 메시지 큐에 저장한 후 각 윈도우 별 핸들을 식별 후 해당 윈도우를 호출한다.
    1. 이벤트 발생 (입력장치)
    2. 이벤트 감지 (윈도우 시스템)
    3. 이벤트 전송 (메시지 큐)
    4. 메시지 추출 
    5. 메시지 전송 (WinMain)
    6. 메시지 처리 (WinProc), 화면에 출력

# **윈도우 인증**

![Security-Architecture-of-Windows](/assets/images/posts/Security-Architecture-of-Windows.jpg)
[이미지 출처](https://networkencyclopedia.com/security-reference-monitor/)

- Winlogon : 로그인 프로세스
    + GINA(msgina.dll) : 계정정보와 암호화 된 패스워드를 LSA에 전달
- LSA(lsas.exe) : 계정 검증, 감사 기록
    + 계정과 암호를 검증하기 위해서 NTLM(암호화)모듈을 로딩하고 계정을 검증
    + SRM 이 작성한 감사로그를 기록
- SAM
    + 사용자 계정정보(해시 값) 에 저장
    + 리눅스의 /etc/shadow 파일과 같은 역할을 수행
- SRM(Security Reference Monitor) : 사용자 별 고유 SID 부여 및 권한 부여
- 윈도우 운영체제 관련 프로세스 세부 내역

    |프로세스|설명|
    |:---|:---|
    |wininit.exe|윈도우 시작 프로그램|
    |services.exe|윈도우 서비스를 관리|
    |lsm.exe|{::nomarkdown}<ul><li>Local Session Manager</li><li>시스템 관리 작업, 주요 함수 실행, 호스트 컴퓨터와 서버의 연결을 관리</li></ul>{:/}|
    |lsass.exe|{::nomarkdown}<ul><li>LSASS(Local Security Authority Subsystem Service)</li><li>사용자 로그인 검사, 비밀번호 변경관리, 액세스 토큰을 생성</li><li>Windows Security Log를 작성</li></ul>{:/}|
    |svchost.ext|서비스를 관리하기 위한 프로세스|
    |conhost.exe|키보드, 마우스 입력 허용, 문자 출력, 콘솔 API 등 셸의 기본 기능을 수행|


# **윈도우 로그** (책임 추적성)
- 로그 종류 : 응용 프로그램 로그, 보안 로그, 시스템 로그

## 1. 윈도우 이벤트 로그 (Event Log)
+ 윈도우 시스템을 사용하는 동안 발생되는 모든 내용을 기록하는 로그파일
+ `*.evt` : 이벤트 뷰어라는 관리도구를 사용하여 확인
+ 일정 크기가 되면 덮어씀
+ 윈도우 로그 유형

    |윈도우 로그 유형|내용|
    |:---|:---|
    |응용 프로그램 로그|프로그램 개발자에 의해서 이벤트를 정의하고 분류하여 응용 프로그램에 기록할 이벤트들이 수록된 자료|
    |보안 로그|{::nomarkdown}<ul><li>관리자에 의해서 보안 로그에 기록된 이벤트 유형을 지정하고, 보안로그에 기록됨.</li><li>로그온 횟수, 로그인 오류 정보, 파일 생성 및 다른 개체 만들기, 파일 열기 및 삭제 등의 리소스 사용관련 이벤트 기록</li></ul>{:/}|
    |시스템 로그|윈도우 시스템에서 사전에 정한 윈도우 시스템 구성요소에서 기록한 이벤트 자료|

## 2. 웹 아티팩트 분석
- 분석 대상

    |분석대상|설명|
    |:---|:---|
    |웹브라우저 캐시|다운로드 받은 이미지 텍스트 파일, 아이콘 등이 있음|
    |히스토리 분석|웹 사이트 접속정보를 저장하는 것으로 월별, 일별 방문 기록을 가지고 있음|
    |쿠키정보|호스트 저보, 경로, 수정시간, 만료시간 등의 값이 있음|

# **윈도우 계정**
## 1. 내장된 계정
+ Users : 기본적인 권한이 없고 Domain Users 그룹 구성원
+ Account Operators : 서버 관리자, 도메인 추가, 그룹 생성, 삭제 등
+ Print Operators : 도메인 컨트롤러에 프린터를 생성
+ Server Operators : 도메인 컨트롤러의 자원의 공유, 폴더 백업 및 복구 

## 2. 유니버설 그룹 (MAC 별 권한 관리)
+ 복수의 도메인 환경에서 상호 관련되어 있는 자원에 대한 허가를 부여할 때 사용
+ Administrators : 모든 권한을 보유
+ Users : 로컬 사용자 계정
+ Guests : 네트워크 접근 가능, 허락된 권한만 보유
+ Backup Operators : 도메인 컨트롤러에 있는 파일을 백업
+ Power Users : 로컬 사용자 생성, 수정 권한, 시스템 관리 가능 (전체 권한은 없음)

# 윈도우 디렉터리

|구성 내용|세부 내용|
|:---|:---|
|`System32`|윈도우 기본 서버 시스템, 32비트 및 64비트 응용프로그램이 동작할 수 있도록 지원|
|`System32\Config`|윈도우 레지스트리 파일이 저장|
|`System32\DllCache`|윈도우 시스템 폴더를 백업해 놓은 폴더, 복원시 사용
|`System32\drivers\etc`|hosts 파일이 존재하며 특정 사이트 인터넷 접속을 차단|
|`Repair`|마지막으로 성공한 구성으로 부팅할 때 사용하는 레지스트리 저보가 저장됨|
|`Media`|윈도우 웨이브 파일과 미디어 파일이 들어 있는 폴더(경고음 등)|

# 공유 폴더

- `net share` : 공유폴더 목록 확인
- `net share <폴더 이름> /delete` : 공유 폴더 삭제
- `IPC$` : 네트워크 서버원격 관리 용도로 사용
- 포트스캐닝 : `nmap -O -v <IP>` : 운영체제 식별, 상세히 출력
    + 윈도우의 SMB 를 사용하여 윈도우 버전을 식별
        * 취약점 공격 가능
    + 취약 포트 (예:445 - MS 파일 및 프린터 공유 프로그램 서비스)
        * 패스워드 Cracking

# 레지스트리

## 1. **레지스트리**
+ 운영체제 및 응용 프로그램 등에 필요한 정보를 저장/관리 하기 위한 계층형 데이터베이스
+ Key(식별자), Value(이름), Data Type(종류), Data(데이터) 로 이루어져 있음
+ `regedit.exe` 로 관리

    |루트 키(Root Key)|설명|
    |:---|:---|
    |HKEY_CLASS_ROOT|파일의 각 확장자에 대한 정보와 파일과 프로그램 간의 연결에 대한 정보|
    |HKEY_LOCAL_MACHINE|설치된 하드웨어와 소프트웨어 설치 드라이버 설정에 대한 정보|
    |HKEY_USERS|사용자에 대한 정보|
    |HKEY_CURRENT_CONFIG|디스플레이 설정과 프린트 설정에 관한 정보|

## 2. 하이브(Hive) 파일
- `c:\windows\system32\config` 에 위치
- 레지스트리 정보를 가지고 있는 물리적인 파일
- `regedit.ext` : 하이브 파일을 읽어서 보여주거나 변경하는 프로그램
- 일반적인 에디터로 변경 불가능, 커널에 의해서 관리
- 하이브 셋 (파일) : 
        
    |하이브 파일|설명|
    |:---|:---|
    |SYSTEM|시스템 부팅에 필요한 시스템 전역 구성정보|
    |SOFTWARE|시스템 부팅에 필요없는 시스템 전역 구성정보, 소프트웨어 정보|
    |SECURITY|시스템 보안정책과 권한 할당 정보, 시스템 계정만 접근 가능|
    |SAM|로컬 계정 정보와 그룹 정보, 시스템 계정만 접근 가능|
    |HARDWARE|시스템 하드웨어 디스크립션, 모든 하드웨어의 장치 드라이버 매핑 정보|
    |COMPONENTS|설치된 컴포넌트와 관련된 정보 관리|
    |BCD00000000|부팅 환경 데이터를 관리, 과거 XP의 Boot.int 를 대체|

- 레지스트리 키
    + `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion` : 윈도우 버전정보
    + `HKLM\SYSTEM\ControlSet00X\Control\ComputerName\ActiveComputerName` : 컴퓨터 이름
    + `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run*` : 시작프로그램 관련 (autoruns 프로그램으로 확인 가능)
    + `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Run*` : 시작프로그램 관련 (autoruns 프로그램으로 확인 가능)
    + `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\RunMRU` : 최근에 실행한 명령어

# 바이러스와 악성코드
## 1. 세대별 컴퓨터 바이러스
+ 1세대 : 원시형 바이러스 (Primitive Virus)
    * 단순, 분석이 쉬움
+ 2세대 : 암호화 바이러스 (Encryption Virus)
    * 컴퓨터 프로그램 일부/전체를 암호화 시켜 백신으로부터 감염여부를 확인할 수 없게 함
    * 암호화 방식이 일정, 복호화 방식도 일정
+ 3세대 : 은폐형 바이러스 (Stealth Virus)
    * 자기 스스로 은폐
    * 다른 실행파일에 기생하여 그 실행파일의 크기를 증가시킴
    * 감염 여부를 진단할 때 이전 상태를 보여줌
+ 4세대 : 갑옷형 바이러스 (Armor Virus)
    * 다양한 암호화 기법을 사용
+ 5세대 : **매크로 바이러스**
    * 액셀이나 워드처럼 매크로 명령을 사용하는 프로그램을 감염시킴

## 2. 감염대상에 따른 바이러스
+ 부트 바이러스 : 부트 섹터에 영향을 주는 컴퓨터 바이러스
+ 파일 바이러스 : 사용자가 사용하는 일반파일에 감염되는 바이러스
    
# 그 외

## 1. NetBIOS 
* 컴퓨터에 있는 애플리케이션들이 근거리통신망(LAN) 내에서 서로 통신할 수 있게 해주는 프로그램. NetBIOS는 그 자체로 라우팅 기능(경로설정)을 지원하지 않고 광역통신망 상에서 통신하는 애플리케이션은 반드시 TCP와 같은 다른 Transport 매커니즘을 추가해서 사용해야 함.
- OSI 모델에서 Session 과 Transport 계층의 서비스를 제공
- Session 과  Datagram 두 가지 통신모드를 제공

## 2. `systeminfo`  
* 시스템의 기본적인 정보 획득

## 3. DDE(Dynamic Data Exchange) 취약점
+ DDE : 윈도우 에서 애플리케이션 간 데이터 전송을 위한 프로토콜
+ 애플리케이션 간 공유 메모리를 사용하여 데이터를 공유
+ 특정 프로그램을 실행하면 다른 프로그램이 자동으로 실행됨.
    * 예: MS Word : 문서 열 때 자동 연결 업데이트

## 4. Shellcode
+ 어셈블리어로 작성된 짧은 코드로 소프트웨어 취약점을 이용한다.
+ 해당 코드가 메모리에 로딩되면 바로 악성코드가 실행된다.

## 5. **Heap Spray**
+ Shell Code를 Heap 영역에 뿌리는 것

## 6. ASLR(Adress Space Layout Randomization)
+ 실행 파일이 메모리에 로드될때 기본주소(ImageBase) 는 항상 동일한 주소임
+ 동일한 메모리 주소를 가지면 공격자에게 매우 취약한 문제점이 발생
+ 이 주소를 랜덤하게 배치하는 것
    * `sysctl -w kernel.randomize_va_space=0` : 해제
    * `sysctl -w kernel.randomize_va_space=1` : 설정

## 7. **버퍼 오버플로우(Buffer Overflow)**
+ 프로세스가 사용 가능한 메모리 공간을 초과해서 발생되는 공격
+ C, C++ 등으로 개발할 때 메모리 공간에 제한을두지 않는 API를 사용해서 발생
+ 프로세스 메모리 구조
    * argv(문자열)
    * argv(포인터) 
    * argc : 환경변수, 프로그램을 실행할 때의 입력값
    * 스택 영역 : 지역변수와 함수 복귀주소 저장
    * 빈 공간
    * 힙 영역 : 동적 메모리 할당
    * 데이터 영역 : 전역변수, 정적 변수
    * 텍스트 영역 : 프로그램 코드
+ 스택 버퍼 오버플로우 : 스택에 저장되어 있는 복귀주소가 지역변수에 의해서 침범당하는 공격
+ 버퍼 오버런 : 메모리 공간에 할당된 공간보다 더 큰 데이터를 입력
+ 힙 버퍼 오버플로우 : 경계 값을 검사하지 않고 메모리를 사용하면 경계를 초과

## 8. Race Condition 공격
+ 원리 : 심볼릭링크를 수정하면 원본파일도 수정된다.
+ setUID 가 걸린 "rc" 라는 프로그램이 있고, 이 프로그램이 실행될 때 /tmp/hi 라는 임시파일을 만든다고 가정
+ rc 에는 setUID 가 걸려있으므로 실행되는 동안에는 root 권한을 가짐
+ /tmp/hi 파일을 삭제하고 /etc/passwd 의 심볼릭링크 파일로 /tmp/hi 를 생성하고 rc를 실행시킴
+ rc 실행중에는 root 권한을 가지므로 /tmp/hi 파일을 수정함으로써 /etc/passwd 를 수정할 수 있다.
+ setUID 가 걸린 "rc" 프로그램이 임시 파일을 생성하고 내용을 쓰는 용도를 권한이 없는 파일에 내용을 쓰는 용도로 바꾸기 위해 링크 파일을 중간에 끼워넣음

## 9. **APT 공격**
+ Advanced Persistent Threat : 특정 기법 및 조직을 대상으로 다양한 공격 기법을 사용하여 지속적으로 공격을 수행
+ 사회관계망 서비스를 사용하여 정보 수집, 악성 코드 배포를 수행하고 공격표적을 선정하여 지속적으로 공격을 수행
+ 침투 > 탐색 > 수집 > 공격

## 10. 바이너리 디핑(Binary)
+ Malware 탐지, 오픈소스 라이선스 준수여부 확인