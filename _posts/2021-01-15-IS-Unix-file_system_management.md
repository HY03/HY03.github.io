---
title: 정보보안 - 유닉스 파일시스템 관리
date: '2021-01-15 17:00:00'
tags:
- 정보보안기사
- UNIX
related: true
categories:
- IS_Certification
toc: true
---

# 파일 시스템 관리

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