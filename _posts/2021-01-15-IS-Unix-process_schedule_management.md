---
title: 정보보안 - 유닉스 프로세스 스케쥴 관리
date: '2021-01-15 19:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 프로세스 스케쥴 관리

- 데몬 프로세스
    + UNIX 시스템 (서버용) 프로세스 특징 : 데몬 프로세스
        * 백그라운드 모드로 실행
        * 제어 터미널(포그라운드 모드)을 갖지 않는다.
        * 클라이언트 요구가 있을 때까지 CPU 사용권 반납, 블록
        * 클라이언트 요구가 있을 때 깨어나 해당 작업을 처리 후 블록
        * 시스템 셧다운 전까지 종료되지 않는다.

- 정기적 스케쥴 관리 : cron
    + cron 데몬 프로세스는 UNIX 시스템에서 정기적인 작업을 지정한 시간에 처리하기 위해 사용
    + 세 가지 구성 요소가 필요
        * 정기적으로 처리할 작업 목록을 정의한 crontab 파일
        * crontab 파일을 제어하는 crontab 명령
        * crontab 파일을 읽어서 내용에 정의된 대로 작업을 처리하는 cron 데몬 프로세스
    + 사용자가 crontab 명령어로 crontab 파일을 제어 -> cron 데몬 프로세스가 작업을 실행
    + crontab 파일 : 정기적으로 처리해야 하는 작업 목록을 정의한 파일
        * 분 시 일 월 요일(0\~6/일\~토) 사용자 실행명령
            + `30 3 * * 2 root /home/clean.sh` : 매주 화요일 3시 30분에 /home/clean/sh 명령을 실행
            + `*/10 2-5 * * * root /home/user/abc` : 매일 2-5시 까지 10분마다 실행
            + `0 7 1 1-10/2 * /bin/vipcheck.sh` : 1-10월 두 달 주기로 1일 7시에 실행
            + `0 3,5,7 * * * batch.sh` : 매일 3,5,7 시
            + `0 8-10 * * * batch.sh` : 매일 8,9,10 시
    + crontab 옵션
        * `-l` : 현재 로그인 된 계정에 잡혀있는 작업 스케줄 확인
        * `-e` : 현재 로그인 중인 계정에 작업을 등록
        * `-r` : 현재 crontab에 등록된 작업을 삭제
        * `-u <USER명>` : 작업을 실행시킬 유저명 설정
            - 유닉스의 경우 `crontab [-option] [user]`;
        * 주의사항 : `crontab` 명령 실행 시 현재 입력되는 내용으로 덮어씀.
    + 사용자 별 crontab 파일이 존재
        * root는 계정별 crontab 파일 편집이 가능
        * `crontab [-u user] [-e]` : 계정에 해당되는 crontab 파일 편집/생성
        * `crontab -u kim -l` : kim 계정의 crontab 파일 출력
    + crontab 접근통제를 위한 파일
        * `/etc/cron.allow` : cron 명령어 사용 가능 사용자
        * `/etc/cron.deny` : cron 명령어 사용 불가 사용자
        * 파일이 둘 다 있는 경우 cron.allow 로 적용
        * 파일이 둘 다 없는 경우 root 만 crontab 명령 실행


- 일시적 스케줄 관리 : `at`
    + 작업을 정해진 시간에 한 번만 실행
    + 명령 형식 : `at [-option] [time_date]`;
    + 옵션
        * `-t time_date` : 작업 시간을 지정
        * `-l` : 현재 대기중인 작업 목록 출력
        * `-r job_id` : job_id 에 해당하는 작업을 목록에서 삭제 (UNIX)
        * `-d job_id` ; job_id 에 해당하는 작업을 목록에서 삭제 (LINUX)
    + at 사용법
        * at 데몬 시작 : `/etc/init.d/atd start`
        * 작업 시간 및 작업 내용을 지정
            - `at 6:00 pm`
            - `at) /usr/local/apache/bin/apachectl restart`
            - `at)` : Ctrl + D 저장
            - `at 6pm +2days` : 이틀 후 오후 6시
            - `at 2pm tomorrow` : 내일 오후 2시
            - `at 11am jul 20` : 6월 20일 오전 11시
        * 예약 작업 확인
            - `at -l` 또는 `atq`
        * 예약 작업 삭제
            - `atrm <job_id>`
            - `at -r <job_id>`
    + 접근통제
        * `/etc/at.allow` : 화이트리스트
        * `/etc/at.deny` : 블랙리스트




- 파일 시스템 연결
    * 장치들은 UNIX 시스템이 의해 인식되어야 사용할 수 있음
    * mount 명령은 보조기억장치에 설치된 파일 시스템을 UNIX 시스템이 인식하도록 특정 디렉토리에 논리적으로 연결시켜 줌
    * 명령 형식 : `mount [-option] [device] [mount_point]` ;
    * 옵션
        - `-a` : `/etc/fstab` 파일에 정의된 모든 파일 시스템을 마운트
        - `-t` : 파일 시스템의 형식을 지정
            + 윈도우 계열
                * vfat : fat, fat32, fat16
                * ntfs
        - `-n` :`/etc/mtab` 파일에 정보를 남기지 않고 마운트
    * 디바이스
        - `/dev/hda1`, `/dev/hda2`, ...
    * `mount -t vfat /dev/hda1 /mnt/win98` ;

- 자동 마운트 설정 : `fstab`
    + 파일 경로 : `/etc/fstab`
    + UNIX 시스템이 부팅시 이 파일을 참조하여 마운트 작업을 자동으로 수행
    + `mount -a` : `/etc/fstab` 에 저장된 정보를 기반으로 명시적 실행
    + `/etc/mtab` : 파일 시스템이 mount 되거나 해제 될 때마다 mount 프로그램에 의해 업데이트 되는 일반 파일

- 파일 시스템 연결 해제 : `umount`
    + 명령 형식 : `umount [-option] [device| mount_point]` ;
    + 사용중이거나 사용자가 해당 디렉터리에 있다면 unmount 명령은 "device is busy" 메시지와 함께 실패한다.
    + 옵션
        * `-a` : 마운트된 모든 파일 시스템을 언마운트
        * `-f` : 해당 파일 시스템을 사용하는 프로세스를 강제로 종료하고 파일 시스템을 언마운트

- 파일 시스템 관리 : `du`, `df`
    + `du` : 디렉터리의 하드디스크 사용량을 확인
    + 명령 형식 : `du [-option] [directory_name]` ;
    + 옵션
        * `-a` : 디렉터리 및 하위 파일정보까지 출력 (All)
        * `-s` : 현재 디렉터리가 사용중인 용량만 출력 (Summary)
        * `-k` : 사용량을 KB 단위로 출력
    + 디렉터리를 명시하지 않을 경우 현재 디렉터리를 기준으로 한다.
    + `df` : 파일 시스템의 전체 공간 및ㅊ 사용 가능 공간에 대한 정보
    + 명령 형식 : `df [-option] [file_system | file]` ;
    + 옵션
        * `-k` : 통계 데이터를 KB 단위로 출력
        * `-i` : 파일 시스템의 사용가능 또는 사용중인 i-node 정보를 출력
    + file_system 인수는 파일 시스템 장치명 또는 마운트 디렉터리를 의미 