---
title: Sequences, Time Series and Prediction - 03. Recurrent Neural Networks for Time Series
date: '2022-10-25 14:00:00'
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

# Recurrent Neural Networks for Time Series

## Intro

- Recurrent Neural Networks 와 Long Short Term Memory Networks 는 시계열 데이터의 예측과 분류에 매우 유용함
- Lambda Layer : 신경망 내 임의의 코드를 레이어로 활용할 수 있음 (전처리와 후처리)
	+ 명시적인 전처리 단계로 데이터를 스케일링한 다음 신경망에 넣는 게 아니라 Lambda 레이어를 사용할 수 있음

## Conceptual overview

- RNN : 순환 레이어를 포함한 신경망
	+ 시퀀스 입력값을 순차적으로 처리하도록 설계
	+ 입력값의 형태 : 배치 사이즈, 타임스탬프 (윈도우사이즈), 컬럼디멘전 (다변량) = 3차원
		* 지금까지 사용한 입력값 형태 : 배치 사이즈, 입력값 특징 수 (윈도우 사이즈)
- RNN Cell
	+ 겉으로 보기에는 셀이 많은 것 같지만, 셀은 하나 뿐이고 이를 반복적으로 사용하여 출력값을 산출
	+ 입력값이 2개 (X 값과 상태벡터 H 값) - 상태벡터값을 이용해 이전 입력값의 잔존 데이터를 전달받음
	+ 입력차원 (예: 타임스탬프가 30개) 만큼 반복

## RNN Notebook

- jupyter notebook 자료

## Shape of the inputs to the RNN

- 데이터의 형태, 데이터를 분할한 배치
	+ 예시
		* Window size 가 30 : 시간 단계가 30
		* 4개로 일괄 처리 : 배치값 4
		* 입력 형태는 4 * 30 * 1
	+ 셀의 관점
		* 하나의 셀은 고정된 시간 단계에서 (Batch Size : 4 * 1) 의 입력을 받음
		* 레이어 내 메모리셀이 3개의 뉴런으로 구성된다면
		* 출력값 행렬은 4 * 3
		* 출력 형태는 4(Batch Size) * 30(Window Size) * 3(Unit Size)
	+ 단순 RNN 에서의 상태 출력값 H 는 출력값 행렬 Y 와 동일함
	+ 일부 경우에는 시퀀스를 입력하되, 출력값의 경우 배치 내 각 인스턴스에 대한 단일 벡터를 얻고 싶은 경우가 있음
		* 마지막 (마지막 시퀀스 스텝-Window) 을 제외하고 모든 출력값을 무시
		* 시퀀스 출력값을 도출하려면 레이어를 생성할 때 return_sequences 를 True 로 지정해야 함
			- 하나의 RNN 레이어를 다른 레이어 위에 스태킹 할때 이 작업이 반드시 필요
		
## Outputting a sequence

- 적층 예시

	```
	model = tf.keras.models.Sequential([
  	tf.keras.layers.SimpleRNN(40, return_sequences=True, input_shape = [None,1]),
  	tf.keras.layers.SimpleRNN(40),
  	tf.keras.layers.Dense(1),
	])
	```

	+ 타 RNN 레이어에 입력으로 들어가야 하는 RNN 레이어에 return_sequences 를 True 로 설정
	+ Dense 레이어에 입력으로 들어가야 하는 RNN 레이어는 마지막 시퀀스 단계의 결과값만을 출력
	+ input_shape (배치 사이즈) 를 설정하지 않음 : 어떤 크기든 상관이 없으니 정의할 필요가 없음
	+ Timestamp 값을 None 으로 설정 : 시퀀스 길이와 관계 없이 입력값을 받음
	+ 마지막 차원이 1로 되어있는 이유 : 일변량 시계열을 다루기 때문
	+ 두번째 층 RNN 레이어에 return_sequences 값을 True 로 설정할 경우
		* 시퀀스 값이 출력됨
		* Keras 는 각 시간 단계별로 동일한 Dense 레이어를 독립적으로 활용함
		* 입력값이 시퀀스이고 출력값 또한 시퀀스일 경우 : 시퀀스 to 시퀀스 RNN
		* 차원의 값은 RNN 레이어의 유닛 값에 따라 변동될 수 있음

## Lambda layers

- 예시

	```
	model = tf.keras.models.Sequential([
	  tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=-1),
	                      input_shape=[window_size]),
	  tf.keras.layers.SimpleRNN(40, return_sequences=True),
	  tf.keras.layers.SimpleRNN(40),
	  tf.keras.layers.Dense(1),
	  tf.keras.layers.Lambda(lambda x: x * 100.0)
	])	
	```

	+ 첫 lambda 레이어 `tf.expand_dims(x, axis=-1)` : 기존 window 생성 function 을 그대로 활용하기 위해 차원을 하나 늘림 (2차원->3차원)
	+ 마지막 lambda 레이어 `lambda x: x * 100.0` : RNN 의 기본 활성함수 tanh 의 출력값 -1 ~ 1 > 시계열 값은 10개 단위로 구성되고, 비슷한 값으로 출력값을 올리면 학습에 도움이 됨

## Adjusting the learning rate dynamically

- 예시

	```
	train_set = windowed_dataset(x_train, window_size, batch_size=128,
		shuffle_buffer=shuffle_buffer_size)

	model = tf.keras.models.Sequential([
			tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=1), input_shape=[None]),
			tf.keras.layers.SimpleRNN(40, return_sequences=True),
			tf.keras.layers.SimpleRNN(40),
			tf.keras.layers.Dense(1),
			tf.keras.layers.Lambda(lambda x: x * 100.0)
		])

	lr_schedule = tf.keras.callbacks.LearningRateScheduler(lambda epoch: 1e-8 * 10**(epoch / 20))

	optimizer = tf.keras.optimizers.SGD(learning_rate=1e-8, momentum=0.9)

	model.compile(loss=tf.kears.losses.Huber(),
					optimizer=optimizer,
					metrics=["mae"])

	history = model.fit(train_set, epochs=100, callbacks=[lr_schedule])
	```

	+ callback 함수를 활용, epoch 진행 별로 학습률을 약간 변경
	+ Huber 손실함수 : 이상치에 덜 민감하게 반응하는 손실함수, 데이터에 노이즈가 많이 섞여있을 때 시도해볼만 함
		* squared error loss 보다 이상치에 덜 민감함
		
## LSTM

- RNN
	+ X 가 셀에 투입되면 Y 결과값과 H 상태벡터가 출력되고, 이는 다음 셀에 영향을 줌
	+ Step 이 진행되면서 초기 H 상태벡터의 영향도는 점점 작아짐
- LSTM
	+ 전체 훈련 기간 동안 상태를 유지해주는 셀 상태를 추가함
	+ 상태 값이 셀 간에 이동을 하고 Step 사이를 이동하면서 더 잘 유지될 수 있게함 - 앞 단계에 있던 데이터가 전체 추정치에 더 큰 영향을 줌
	+ 상태는 양방향으로 움직일 수 있음
	
## Coding LSTMs

- 예시

	```
	tf.keras.backend.clear_session()
	dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)

	model = tf.keras.models.Sequential([
		tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=-1), input_shape=[None]),
		tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32)),
		tf.keras.layers.Dense(1),
		tf.keras.layers.Lambda(lambda x: x * 100.0)
	])	

	model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(learning_rate=1e-6, momentum=0.9))
	model.fit(dataset, epochs=100, verbose=0)
	
	```

	+ `tf.keras.backend.clear_session()` : 내부 변수를 초기화. 이후 버전에 영향을 주지 않고 여러 모델을 시험해 볼 수 있음
	+ `tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32))` : 32개 셀의 단일 LSTM 레이어 추가. 예측에 미치는 영향을 파악할 수 있도록 양방향으로 만듦

- 예시

	```
	tf.keras.backend.clear_session()
	dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)

	model = tf.keras.models.Sequential([
		tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=-1), input_shape=[None]),
		tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32, return_sequences=True)),
		tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32)),
		tf.keras.layers.Dense(1),
		tf.keras.layers.Lambda(lambda x: x * 100.0)
	])	

	model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(learning_rate=1e-6, momentum=0.9))
	model.fit(dataset, epochs=100, verbose=0)
	
	```

	+ `tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32, return_sequences=True))` : LSTM 레이어를 한층 더 쌓음, retrun_sequences 를 True 로 설정해야만 함