---
title: Jenkins Pipeline - Declarative and IaC approaches for DevOps
date: '2022-09-30 14:00:00'
tags:
- Coursera
- Rudi Hinds
- Jenkins
- Declarative and IaC approaches for DevOps
- 젠킨스
- CI/CD
related: true
categories:
- Jenkins
toc: false
---

# 후기

![강좌에 대한 설명](/assets/images/posts/coursera_JenkinsPipeline-DeclarativeandIaCapproachesforDevOps_course_info.png)

- 해당 강좌는 영상플레이 시간 2시간 (하지만 영어 강좌여서 3시간 이상) 소요 되는 강좌입니다.
- 장점은 부담되는 양의 자료 (책, 강의 등) 를 보기 전에 짧은 시간 훑어보기에 좋은 강의라는 점입니다.
- 대부분 Declarative Script 로 Jenkinsfile 을 작성하여 활용할 것으로 생각하는 바, 기초에 좋은 강의일듯 싶습니다.


# 강좌

## 파이프라인이란 무엇인가?

- 정의와 기능
	+ SCM(Source Code Management) 의 Continuous Delivery 절차를 위한 플러그인 집합
	+ 제품 개발 라이프사이클 (Submitting Code -> Testing -> Staging -> Deployment ...) 에 연관
	+ 각 단계의 성공 / 실패 여부 제공
	+ 다양한 타 환경에서의 운영 지원
	+ 저장소 단계에서 실 환경 배포까지의 자동화
- 파이프라인 작성법
	+ Pipeline script 를 Jenkins UI 에서 작성
	+ Jenkins file 을 통한 작성
- 파이프라인 언어
	+ **declarative**
	+ scripted
- jenkinsfile 의 장점
	+ IaC (Infrastructure as Code)
		* application code 와 마찬가지로 취급되어 저장소에 committed 됨
		* 저장소의 이점을 누리며, 동시에 어떤 구조로 되어있는지 구성원들이 시각적으로 확인할 수 있음

## 파이프라인 스크립트의 형태와 전역변수

- 젠킨스 서버 구동
	```java -jar jenkins.war httpPort=8080```
	+ 브라우저에서 `localhost:8080` 으로 접속
	+ 기본 계정 로그인 : admin / admin
- new items
	+ 네이밍
	+ Pipeline 생성 
	+ Pipeline 섹션으로 이동
	
	+ Jenkinsfile (Declarative Pipeline)
	```
		pipeline {
			agent any // Execute this Pipeline or any of its stages, on any available agent.
			stages {
				// stage : conceptually distinct subset or tasks performed throughout the entire pipeline
				stage('Build') { // Defines the "Build" stage. (Keyword is flexible)
					// Steps represents a single task.
					// It tells Jenkins what to do at a particular point in time of a particular step in the process.
					steps {
						//  Perform some steps related to the "Build" stage.
					}
				}
				stage('Test') { // Defines the "Test" stage.
					steps {
						// Perform some steps related to the '"Test" stage.
					}
				}
				stage('Deploy') { Defines the "Deploy" stage. 
					steps {
						// Perform some steps related to the "Deploy" stage.
					}
				}
			}
		}
	```
- Environment Variables (Global Variables)
	+ **`http://localhost:8080/pipeline-syntax/globals#env`**

## 첫 파이프라인 스크립트 작성과 환경변수 삽입

- pipeline 섹션에 코드 작성

	```
		pipeline {
		    agent any
		    stages {
		        stage('stage 1'){
		            steps {
		                echo "hello world"
		                echo BUILD_ID // Global Variable
		            }
		        }
		    }
		}
	```

	+ Build Now
	+ Console Output 확인
- environment 변수 삽입
	+ environment 변수는 최상단에 위치할 수도, stage 안에 존재할 수도 있음
	
	```
		pipeline {
		    agent any
		    environment {
		        mainenv = 'dev'
		    }
		    stages {
		        stage('stage 1'){
		            steps {
		                echo "hello world"
		                echo BUILD_ID
		                echo """mainenv = ${mainenv}""" // enject template, literals, variables
		            }
		        }
		    }
		}
	```

	```
		pipeline {
		    agent any
		    environment {
		        mainenv = 'dev'
		    }
		    stages {
		        stage('stage 1'){
		            environment{
		                subenv = "prod"
		            }
		            steps {
		                echo """inside stage 1: mainenv = ${mainenv}""" // enject template, literals, variables
		                echo """inside stage 1: subenv = ${subenv}"""
		            }
		        }
		        stage('stage 2'){
		            steps {
		                echo """inside stage 2: mainenv = ${mainenv}"""
		                echo """inside stage 2: subenv = ${subenv}""" // causing error, because of scope.
		            }
		        }
		    }
		}
	```

## 실제 Github 저장소를 사용한 파이프라인 스크립트 작성과 build steps

- 대시보드에서 new item 선택
	+ Pipeline 생성
		* 우측 드롭다운 메뉴에서 샘플 코드를 불러올 수 있음
		* Pipeline Syntax 에서 Snippet Generator 활용하기
- Most common MVN build phases

	|Build Phase|Description|
	|:---|:---|
	|validate|Validates that the project is correct and all necessary information is available. This also makes sure the dependencies are downloaded.|
	|compile|Compiles the source code of the project.|
	|test|Runs the tests against the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed.|
	|package|Packs the compiled code in its distributable format. such as a JAR.|
	|install|Install the package into the local repository, for use as a dependency in other projects locally.|
	|deploy|Copies the final package to the remote repository for sharing with other developers and projects.|

- 작성한 소스코드

	```
		pipeline {
	    	agent any
	    	stages {
	        	stage('Setup'){
	            	steps {
	                 	// this will delete folder whatever the OS platform is.
	                	dir('jenkins-spring'){
	                    	deleteDir()               
	                	}
	            	}
	        	}
	        	stage('Build'){
	            	steps {
	                	// sh : for Linux
	                	// for Windows (copy github source)
	                	bat 'git clone https://github.com/rudihinds/jenkins-spring.git'
	                	bat 'mvn clean -f jenkins-spring'
	            	}
	        	}
	        	stage('Test'){
	            	steps {
	                	bat 'mvn clean test -f jenkins-spring'
	            	}
	        	}
	        	stage('Deploy'){
	            	steps {
	                	bat 'mvn clean package -f jenkins-spring'
	            	}
	        	}
	    	}
		}
	```

## Jenkinsfile 을 이용하여 SCM 에 파이프라인 연결

- SCM 을 통해 어떻게 파이프라인을 가져올 수 있는지?
- 복잡한 Microarchitecture 구조가 아니라면 보편적으로 Jenkinsfile 은 프로젝트의 root 디렉토리에 있음
- Github 에 있는 Jenkinsfile 에 작성된 declarative script 와 지금까지 작성한 스크립트의 차이점
	+ clone 스테이지가 없음 : Github의 Jenkinsfile을 사용한다는 것은 이미 Jenkins 에게 SCM 에서 소스코드를 가져오라고 지시한 것임 
	+ setup 스테이지가 없음 : 해당 프로세스는 이미 통합되어 있음 (Jenkins가 핸들링)
- 실습
	+ 새 파이프라인을 만들고, 파이프라인의 정의를 Pipeline script from SCM 으로 설정
		* SCM 종류 Git으로 설정
		* Repository URL 설정
		* 브랜치 설정
	+ Build Now
		* Declarative: Checkout SCM 스테이지가 자동생성된 것을 확인할 수 있음
