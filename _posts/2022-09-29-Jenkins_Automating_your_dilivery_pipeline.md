---
title: Jenkins - Automating your delivery pipeline
date: '2022-09-29 16:00:00'
tags:
- Coursera
- Anju M Dominic
- Jenkins
- Automating your delivery pipeline
- 젠킨스
- CI/CD
related: true
categories:
- Jenkins
toc: false
---

# 후기

![강좌에 대한 설명](/assets/images/posts/coursera_Automatingyourdeliverypipeline_course_info.png)

- 해당 강좌는 영상플레이 시간 1시간 (하지만 영어 강좌여서 2시간 이상) 소요 되는 강좌입니다.
- 개인적으로 추천하고 싶지는 않은데, 내용을 떠나 구축된 Cloud 환경의 젠킨스가 구 버전이어서 플러그인과 호환이 되질 않습니다.
- 실습의 절반정도는 영상을 보는 것으로만 하였습니다.
- 장점은 부담되는 양의 자료 (책, 강의 등) 를 보기 전에 짧은 시간 훑어보기에 좋은 강의라는 점입니다.


# Jenkins 란?

- DevOps 환경에서 Continuous integration and Delivery 를 가능하게 하는 툴
- Continuous Delivery
	+ 개발자가 개발한 새 소스코드를 즉시 소스코드 저장소에 반영
	+ 빌딩, 테스팅, 패키징이 등이 일어나 배포 가능 버전이 생성됨
- 이점
	+ 배포속도가 빨라짐
	+ 피드백을 빨리 받을 수 있음
	+ 초기 단게에서 결함을 발견할 수 있음



# 실습

1. 젠킨스 서버에서 사용하기 위한 Maven 설정
- DashBoard 에서 Global Tool Configuration 설정
- 하단의 Maven 설치 (네이밍 포함)
- Maven : 자바용 프로젝트 빌드, 관리에 사용되는 도구
	+ 라이브러리의 추가, 라이브러리 버전 동기화의 어려움을 해소
	+ 프로젝트 생성, 테스트 빌드, 배포 등의 작업을 위함

2. Spring pet clinic 어플리케이션의 첫번째 Job 생성
- Spring pet clinic 의 깃헙 소스코드 fork 하기
- Jenkins 의 파이프라인 첫 단계는 compile stage 혹은 build stage 가 될 것
- New Item
	+ Freestyle project : 어떠한 제약, 제한조건 없는 프로젝트
		* 네이밍하기
	+ 섹션들
		* General
		* Source Code Management
		* Build Triggers
		* Build Environment
		* Build
		* Post-build Actions
	+ Source Code Management 설정
		* Git URL 값 입력
		* Branch 입력 (소스코드가 위치하는 경로)
	+ Build 설정
		* Add build step -> Invoke top-level Maven targets : Maven 을 통한 build task 수행
		* Maven 버전 설정 : Global Tool Configuration 에서 네이밍한 Maven 선택
		* Goals 설정 : compile 입력
		* Save
- Build Now
	+ #1 빌드 수행
	+ 버튼 색에 따른 상태
		* Blue : 성공(완료)
		* Blinking : 현재 실행중 
		* Rend : 실패
	+ 버튼 옆 arrow -> Console Output 으로 결과 확인

3. Delivery Pipeline 생성하기 (Build, Test Stage 생성하기)
- Jenkins -> New Item
	+ Freestyle project
		* 네이밍하기
	+ Source Code Management 설정
		* Git URL 값 입력
		* Branch 입력 (소스코드가 위치하는 경로)
	+ Build 설정
		* Add build step -> Invoke top-level Maven targets : Maven 을 통한 build task 수행
		* Maven 버전 설정 : Global Tool Configuration 에서 네이밍한 Maven 선택
		* Goals 설정 : test 입력
		* Save
- Build Now
	+ #1 빌드 수행
	+ Console Output 에서 테스트 케이스에 대한 성공/실패여부를 볼 수 있음
- Test Job 의 Configure (Build 수행 후 Test 를 진행하고 싶다)
	+ Build Triggers : 어떻게 특정 Job 을 Trigger 할지 설정
		* Build after other projects are built
			- Projects to watch : Build Job 입력
		* Save
- Pipeline 전체를 살펴보기 위해 Plugin 설치하기
	+ Jenkins -> Manage Jenkins -> Manage Plugins
		* Available tab -> search Build pipeline -> install
	+ Jenkins Dashboard 의 작은 + 버튼 클릭 -> Build Pipeline View
		* 네이밍
	+ Pipeline Flow -> Upstream / downstream config
		* Initial Job (첫 시작 Job) 설정
		* 초록색 Job : 이미 실행된 Job
		* 파란색 Job : 아직 실행되지 않은 Job
		* 노란색 Job : 실행중인 Job
		* 빨간색 Job : Job 실패
	+ Run 

4. Codify Pipeline
- Build pipeline 의 코드화 필요성
	+ 위 활동에서의 Configuration Setup 을 직접 수행할 경우 신뢰성이 떨어짐
	+ Jenkins 는 Groovy Script 를 사용함
- Pipeline Maven integration plugin 설치
	+ 메이븐과 젠킨스의 통합을 쉽게 해줌
	+ scripted pipeline 에서 사용할 많은 Wrapper methods 를 제공
- New item
	+ Pipeline
		* Pipeline Tab
		
		```
		node{
		    stage('Checkout'){
		        git branch: 'main', url:'https://github.com/HY03/SpringPetClinic.git'
		    }
		    stage('Build'){
		        withMaven(maven: 'M3'){
		            sh 'mvn compile'
		        }
		    }
		}
		```

			- node : where you run your job
				+ master : where jenkins installed
				+ slave : for the distributed

5. declarative pipeline
- 필요성
	+ Jenkins 와 엮여있는 Groovy Script 의 어려움
- New item
	+ Pipeline
		* Pipeline Tab
		
		```
		pipeline{
		    agent{label 'master'}
		    tools{maven 'M3'}
		    stages{
		        stage('Checkout'){
		            steps{
		                git branch: 'main', url:'https://github.com/HY03/SpringPetClinic.git''
		            }
		        }
		        stage('Build'){
		            steps{
		                sh 'mvn compile'   
		            }
		        }
		        stage('Test'){
		            steps{
		                sh 'mvn test'
		            }
		        }
		        stage('Package'){
		            steps{
		                sh 'mvn package'
		            }
		        }
		        stage('Deploy'){
		            steps{
		                sh 'java -jar /var/lib/jenkins/workspace/PetClinicDeclarativePipeline/target/*.jar'
		            }
		        }
		    }
		}
		```

			- `sh 'mvn [goal]'` : 실행할 mvn goal 설정
			- 메이븐은 여러 플러그인으로 구성되어 있으며, 각각의 플러그인은 하나 이상의 goal(명령, 작업)을 포함하고 있다. 
			- Goal은 Maven의 실행 단위이다.
			
6. Declarative Pipeline 을 Jenkins File 로 저장시켜 저장소에 저장하기
- 소스코드 저장소에 Jenkins Pipeline 스크립트를 복사
- Github 에서 파일 생성 : Jenkinsfile (파일명은 고정)
- Job 의 Pipeline 설정에서 Pipeline script 대신 Pipeline script from SCM 선택
- Git 경로 및 Branch 설정
- Script Path : jenkinsfile 위치 설정
- Lightweight Checkout 설정 : jenkinsfile 먼저 checkout 한 뒤, jenkinsfile 의 모든 스테이지를 수행

