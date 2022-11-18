---
title: Sequences, Time Series and Prediction - 01. Sequences and Prediction
date: '2022-10-20 14:00:00'
tags:
- Coursera
- deeplearning.ai
- Laurence Moroney
- timeseries
- 시계열
- tensorflow
related: true
categories:
- AI
- Time Series
toc: false
---

# Introduction

- 순차 시계열 데이터 (Sequential time series data)
	+ 값이 시간에 따라 변하는 것
	+ 예
		* 주식거래의 종가
		* 특정 일의 기온
		* 웹사이트의 방문자 수
		* 스프레드시트에 기록할 수 있는 데이터
- 다룰 내용
	+ 미래 시점의 값 예측을 위한 다양한 방법론
	+ 위의 내용의 구현법 

## Introduction, A conversation with Andrew Ng

- 데이터의 합성 시퀀스를 만들기
- 데이터 시계열에서 공통 속성을 살펴보기
	+ 계절성 : 날씨의 경우 6월이 1월보다 따듯하고, 11월은 10월보다 습할 수 있음
	+ 경향성 : 주식의 종가처럼 시간이 가면서 상승, 혹은 하강
	+ 노이즈 : 무작위 요소
- 구현 : 흑점 활동 모니터
	+ 흑점 활동
		* 11년, 혹은 22년의 주기 (계절성)
		* 노이즈
		* 250년 전부터 측정해온 데이터 활용

# Sequences and Prediction

## Time series examples

- 시계열 (Time Series) 이란 무엇인가?
	+ 오랜 시간에 걸쳐 균등한 간격으로 순서가 지정된 시퀀스로 나타나는 값
	+ 다변량 시계열 : 각 시점에서 복수 개의 값이 표시된 경우
		* 데이터에 추가값을 더하여 상관관계를 파악할 수 있음
			- 시간의 흐름에 따른 기온과 이산화탄소 배출량의 상관관계
			- 자동차의 이동경로 (동시간의 간격 (속도), 위도, 경도 등)
			
## Machine learning applied to time series

- 머신러닝으로 가능한 시계열 관련 작업
	+ 데이터를 기반으로 한 예측 작업
	+ 이미 가지고 있는 데이터보다 이전 시점의 데이터를 예측
	+ 실질적으로 존재하지 않는 데이터의 시점의 데이터를 예측
	+ 변칙 감지에 활용
	+ 패턴의 발견 (예: 음파를 인식)
	
## Common patterns in time series

- 흔하게 나타나는 시계열 패턴 (눈으로 보고 인식하는데 유용)
	+ 추세 : 특정한 방향으로 움직이는 경우 (예: 무어의 법칙)
	+ 계절성 : 패턴이 예측 가능한 간격으로 반복됨 (예: 쇼핑사이트 방문자수 (주말에 올라감))
	+ 노이즈 : 전혀 예측이 불가능한 임의의 값들로 구성된 세트
	+ 자기상관관계 : 시간의 흐름에 따라 과거 혹은 현재의 값이 미래에 영향을 주는 것
	+ 복합적으로 나타나는 경우
	+ 비정상시계열 : 명확한 패턴을 보이다가 큰 이벤트로 인해 패턴이 깨지는 경우
		* 특정 경향성을 보이는 경우에 특정 구간만 학습
		* 하지만 현실의 데이터는 단순하지 않음 (패턴이 깨지며 경향이 나타났으나 다시 과거 패턴으로 회귀)

## Introduction to time series

- Colab 파일

## Train, validation and test sets

- 예측 모델의 성능 측정
	+ Fixed Partitioning (고정 파티셔닝) : 시계열을 훈련기간, 검증 기간, 테스트 기간으로 분할
		* 시계열이 계절성이 있는 경우 각각의 기간에 계절 전체를 포함하고 싶음
		* 시간이 지남에 따라 검증 기간의 데이터를 훈련에 사용, 테스트 기간의 데이터로 검증을 하고, 새로운 테스트 기간으로 테스트를 함
		* 테스트 기간은 현재 데이터에 가장 영향을 많이 줄 수 있는 데이터. 따라서 테스트 세트를 포기하는 경우가 흔함
	+ 롤 포워드 파티셔닝
		* 짧은 훈련기간을 가지고, 점점 증가시켜 (한번에 하루, 한번에 한 주) 반복수행
		* 검증기간에는 다음 달이나 다음 주를 예측
		* 고정 파티셔닝을 여러 번 시행하고 모델을 계속 다듬는 과정
		
## Metrics for evaluating performance

- 성능을 계산할 지표
	```
		errors = forcasts - actual // 오차산출
		mse = np.square(errors).mean() // 평균제곱오차 :  가장 일반적인 지표 (음수제거를 하여 에러간 상쇄가 없도록 함)
		rmse = np.sqrt(mse) // 평균제곱근오차 : 원래 에러 규모와 동일한 규모를 만들기 위해 제곱근 계
		mae = np.abs(errors).mean() // 평균절대오(편)차 :  제곱 대신 절대값을 사용
		mape = np.abs(errors / x valid).mean() // 평균절대백분율오차 : 절대 오차와 절대값의 평균 비율 (값 대비 오차의 크기를 파악)
	```
	+ 작은 오차보다 큰 오차가 생겼을 경우 비용이 훨씬 크다면 MSE
	+ 손익이 단순 오차의 크기에 비례한다면 MAE 
		* 케라스에서의 구현
			`keras.metrics.mean_absolute_error(x_valid, native_forecast).numpy()`

## Moving average and differencing

- 이동 평균을 계산 : 일반적이고 간단한 예측 방법
	+ 노이즈를 제거하고, 원본 시계열을 대략적으로 유추하는 곡선이 도출
	+ 추세나 계절성을 예측하지는 않음 : 현재 시점에서 미래를 예측하고자 하는 기간 이후에는 단순 예측보다 결과가 저조할 수 있음
	+ 차분은 이를 피하는 방법 중 하나 : 시계열에서 추세와 계절성을 제거
		* 즉, 시계열 자체를 연구하는게 아니라 T 시점의 값과 이전 기간의 값 사이의 차이를 연구
		* 차분에 이동평균선을 예측하면 이는 차분에 대한 예측일 뿐이고, 원본 시계열에 대한 것은 아님
			- 뺀 값 (이전 기간의 값) 을 다시 더해줘야 함
			- 하지만 이전의 값을 더해줄 때 노이즈도 같이 생기게 됨. > 이동 평균을 이용하여 과거의 노이즈를 제거
			
## Trailing versus centered windows

- Trailing Window (현재 값의 이동 평균을 산출할때)
	`t1000 = (t970 + t971 + ... + t999) / 30`
- Centered Window (과거 값의 이동 평균을 산출할때) : 정확도가 Trailing Window 보다 높음
	`t635 = (t630 + t631 + ... + t640) / 11`

## Forecasting

- jupyter notebook 자료

## Week 1: Working with time series

- jupyter notebook 자료