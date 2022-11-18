---
title: Sequences, Time Series and Prediction - 04. Real-world time series data
date: '2022-11-07 14:00:00'
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

# Real-world time series data

## Intro

- 실사용 데이터 (태양의 흑점활동) 이용하기
- Conv1D, LSTM, DNN 을 결합한 모델을 활용할 것

## Convolutions

- 예시

	```
		model = tf.keras.models.Sequential([
			tf.keras.layers.Conv1D(filters=32, kernel_size=5, // 32개의 필터를 학습할 1D Conv
								strides=1, padding="casual",
								activation="relu",
								input_shape=[None, 1]),
			tf.keras.layers.LSTM(32, return_sequences=True),
			tf.keras.layers.LSTM(32),
			tf.keras.layers.Dense(1),
			tf.keras.layers.Lambda(lambda x: x * 200)
		])

		optimizer = tf.keras.optimizers.SGD(learning_late=1e-5, momentum=0.9)

		model.compile(loss=tf.keras.losses.Huber(),
					optimizer=optimizer,
					metrics=["mae"])

		model.fit(dataset, epochs=500)
	```

## Bi-directional LSTMs

- 예시

	```
		model = tf.keras.models.Sequential([
			tf.keras.layers.Conv1D(filters=32, kernel_size=5, 
								strides=1, padding="casual",
								activation="relu",
								input_shape=[None, 1]), // LSTM 의 입력값을 재구성하는 Lambda 레이어를 없앰
			tf.keras.layers.LSTM(32, return_sequences=True),
			tf.keras.layers.LSTM(32),
			tf.keras.layers.Dense(1),
			tf.keras.layers.Lambda(lambda x: x * 200)
		])

		optimizer = tf.keras.optimizers.SGD(learning_late=1e-5, momentum=0.9) // 플로팅한 결과 불안정해지기 전의 학습률

		model.compile(loss=tf.keras.losses.Huber(),
					optimizer=optimizer,
					metrics=["mae"])

		model.fit(dataset, epochs=500) // epoch 늘리기
	```

- windowed_dataset 헬퍼 함수

	```
		def windowed_dataset(series, window_size, batch_size, shuffle_buffer):

			ds = tf.data.Dataset.from_tensor_slices(series)
			ds = ds.window(window_size + 1, shift=1, drop_remainder=True)
			ds = ds.flat_map(lambda w: w.batch(window_size + 1))
			ds = ds.shuffle(shuffle_buffer)
			ds = ds.map(lambda w: (w[:-1], w[-1]))

			return ds.batch(batch_size).prefetch(1)
	```

- Bi-directional LSTM

	```
		model = tf.keras.models.Sequential([
			tf.keras.layers.Conv1D(filters=32, kernel_size=5, 
								strides=1, padding="casual",
								activation="relu",
								input_shape=[None, 1]), 
			tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32, return_sequences=True)), // 양방향
			tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32)),
			tf.keras.layers.Dense(1),
			tf.keras.layers.Lambda(lambda x: x * 200)
		])

		optimizer = tf.keras.optimizers.SGD(learning_late=1e-5, momentum=0.9) // 플로팅한 결과 불안정해지기 전의 학습률

		model.compile(loss=tf.keras.losses.Huber(),
					optimizer=optimizer,
					metrics=["mae"])

		model.fit(dataset, epochs=500)
	```

	+ 결과수치는 긍정적이나 검증세트에 예측을 플로팅 해보면 과적합이 보여 일부 파라미터에 변화를 줄 필요가 있음
	+ MAE 로 손실을 플로팅하면 문제점을 확인할 수 있음
		* 스파이크 현상은 배치 크기가 작아서 무작위 노이즈가 많기 때문임
		* 배치사이즈를 줄이거나 늘이는 것에 따라 학습과 예측이 달라짐
		
## More on batch sizing

- 신경망을 더 빠르게 학습하도록 하는 최적화 알고리즘
	+ 머신러닝 : 잘 작동되는 모델을 찾기 위해 많은 훈련을 거쳐야 하는 반복적인 과정
		* 모델을 빠르게 훈련시키는 것이 매우 중요함
	+ 딥러닝은 빅데이터에서 가장 잘 작동됨 -> 훈련이 어려움 (큰 데이터 세트에서 훈련하는 것은 매우 느린과정)
	+ 좋은 최적화 알고리즘을 찾는 것은 효율성을 향상시켜준다.
- 미니배치 경사 하강법
	+ 벡터화 : m개의 샘플에 대한 계산을 효율적으로 만들어줌. 명시적인 반복문 없이도 훈련 세트를 진행할 수 있도록 함.
	+ 훈련 샘플을 받아서 큰 벡터에 저장함
		* `X = [x^1, x^2, ..., x^m]` : shape : (n_x,m)
		* `Y = [y^1, y^2, ..., y^m]` : shape : (1,m)
		* 하지만 m 의 수치가 크면 여전히 학습은 느리다.
		* 예를들어 m 의 수치가 500만 이라면?
	+ 전체 훈련 세트에 대한 경사 하강법을 구현하면 경사 하강법의 작은 한 단계를 밟기 전에 모든 훈련 세트를 처리해야 함
		* 즉, 경사하강법의 다음 단계를 밟기 전에 500만 개의 전체 훈련 샘플을 처리해야 함
		* 500만 개의 전체 훈련 샘플을 모두 훈련하기 전에 경사 하강법이 진행되도록 하면 더 빠른 알고리즘을 얻을 수 있음
	+ 훈련 세트를 더 작은 훈련세트 (미니배치) 로 나눔
		* mini-batch 가 1000개의 샘플을 갖는다고 가정
		* `X = [x^1, x^2, ..., x^1000 | x^1001, ..., x^2000 | ... | ... x^m]`
		* `X = X^{1},                   X^{2}, ... ,               X^{5000}` : shape : (n_x, 1000)
		* `Y = Y^{1},                   Y^{2}, ... ,               Y^{5000}` : shape : (1, 1000)
		* Mini-batch t : X^{t}, Y^{t}
	+ 표기법 정의
		* `x^(i)` : i 번째 훈련 샘플
		* `z^[l]` : l 번째 신경망의 z 값 
		* `X^{t}` : t 번째 미니배치 X
	+ Batch Gradient Descent : 일반적인 경사하강법, 모든 훈련 세트를 동시에 훈련시킴, 훈련 샘플의 모든 배치를 진행시킨다는 관점
	+ 미니배치 : 전체 훈련 세트 X,Y 를 한번에 진행시키지 않고, 하나의 미니배치 X^{t}, Y^{t} 를 동시에 진행시키는 알고리즘
		```
		for t=1 ,..., 5000 : 총 미니배치의 수 (5000개)
			// 1 step of gradient descent using X^{t}, Y^{t}
			// (as if m=1000)
			// 모든 1000개의 샘플에 대해 명시적인 반복문을 갖는 것보다 벡터화를 사용해 모든 1000개의 샘플을 동시에 진행함
			Forward prop on X^{t}
				Z^[1] = W^[1] * X^{t} + b^[1] : Vectorized Implementation (1000 examples)
				A^[1] = g^[1] * (Z^[1]) : Vectorized Implementation (1000 examples)
				...
				A^[l] = g^[l] * (Z^[l]) : Vectorized Implementation (1000 examples)
			Compute cost J^{t} = 1/1000 * Sum (( i = 1 to l) Loss(expect(y^(i)), y^(i)))
				+ 정규화 항
			Backprop to compute Gradients cost J^(t) using X^{t}, Y^{t}
				W^[l] = W^[l] - adW^[l], b^[l] = b^[l] - adb^[l]
			...
			1 epoch : pass through training set (5000 개의 경사하강단계)
		```

## Convolutions with LSTM notebook

- Jupyter notebook 자료

## Convolutions with LSTM

- Jupyter notebook 자료

## Real data - sunspots

- 소스 내려받기 : 케글에서 내려받거나, 이번학습을 위한 데이터 제공 저장소를 사용 (후자)

	`!wget --no-check-certificate https://storage.googleapis.com/laurencemoroney-blog.appspot.com/Sunspots.csv -O /tmp/sunspots.csv`

- CSV 읽기

	```
		import csv
		time_step = []
		sunspots = []

		with open('/tmp/sunspots.csv') as csvfile:
			reader = csv.reader(csvfile, delimiter=',')
			next(reader)
			for row in reader :
				sunspots.append(float(row[2]))
				time_step.append(int(row[0]))
	```

- numpy 배열로 전환

	```
		series = np.array(sunspots) // numpy 에 항목을 추가할 때마다 목록을 복제하는데,
		time = np.array(time_step) // 메모리 관리 과정이 많이 진행되기 때문에 데이터 양이 많으면 느려질 수 있음
	```

- 시계열을 훈련 및 검증 데이터 세트로 분할


	```
		split_time = 1000
		time_train = time[:split_time]
		x_train = series[:split_time]
		time_valid = time[split_time:]
		x_valid = series[split_time:]

		window_size = 20
		batch_size = 32
		shuffle_buffer_size = 1000
	```

- 이전 windowed_dataset 함수와 동일 코드를 사용

	
	```
		def windowed_dataset(series, window_size, batch_size, shuffle_buffer):
			dataset = tf.data.Dataset.from_tensor_slices(series)
			dataset = dataset.window(window_size + 1, shift=1, drop_remainder=True)
			dataset = dataset.flat_map(lambda window: window.batch(window_size + 1))
			dataset = dataset.shuffle(shuffle_buffer).map(lambda window: (window[:-1], window[-1]))
			dataset = dataset.batch(batch_size).prefetch(1)
			return dataset
	```

## Train and tune the model

- 이전 수업에서 배웠던 모델로 예측하여 플로팅하면, 결과는 괜찮아보이나 MAE 가 매우 큼
- 이는 이전 window_size 가 20 (여기서는 약 2년이 안되는 시간) 이나, 사실 흑점 데이터의 주기는 11년 혹은 22년으로 추정됨
- window_size 를 11년에 해당하는 132로 두고 다시 훈련을 하면 차트는 더 잘 나오나 MAE는 더 커짐
	+ 데이터를 되돌아보면 11년 주기의 계절성을 갖지만 창 안에 계절 전체가 있어야 할 필요는 없음
	+ 플롯을 확대해보면 전형적인 시계열 형태 데이터임
	+ 나중에 오는 값이 앞선 값과 연관이 있지만 노이즈가 많음
	+ 그래서 훈련 시에 창 크기가 클 필요는 없을 수도 있음
- data 의 분할을 1000을 훈련, 2500을 검증으로 설정하였는데 이는 좋지 못한 분할임
	+ 3500과 500으로 지정
- 신경망 설계와 파라미터의 크기 변경
	+ 10, 10, 1 레이어 를 30, 15, 1 로 값을 바꿔서 훈련 (입력 Shape 값이 30)
	
## Prediction

- 예측
	`model.predict(series[3205:3235][np.newaxis])` : 7.077 개의 흑점 예상 (실 데이터 8.7 개)


- 설정 변경 : MAE 13.7
	```
	split_time = 3000
	window_size = 60
	
	model = tf.keras.models.Sequential([
		tf.keras.layers.Dense(20, input_shape=[window_size], activation="relu"),
		tf.keras.layers.Dense(10, activation="relu"),
		tf.keras.layers.Dense(1)
	])
	
	model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(lr=1e-7, momentum=0.9))
	```
	+ 8.13 예측 (실제값 8.7)
	
## Sunspots notebooks (Lab 2 & Lab 3)

- jupyter notebook 으로 대체

## Sunspots

- 입력 창의 크기를 60으로 함
- DNN 을 Dense 20, 10, 1 로 학습

## Combining our tools for analysis

- 예시


	```
		window_size = 60
		batch_size = 64
		train_set = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)

		model = tf.keras.models.Sequential([
			tf.keras.layers.Conv1D(filters=32, kernel_size=5, strides=1, padding="casual", activation="relu", input_shape=[None, 1]),
			tf.keras.layers.LSTM(32, return_sequences=True),
			tf.keras.layers.LSTM(32),
			tf.keras.layers.Dense(30, activation="relu"),
			tf.keras.layers.Dense(10, activation="relu"),
			tf.keras.layers.Dense(1),
			tf.keras.layers.Lambda(lambda x: x * 400)
		])

		lr_schedule = tf.keras.callbacks.LearningRateScheduler(lambda epoch: 1e-5 * 10**(epoch / 20))
		optimizer = tf.keras.optimizers.SGD(learning_rate = 1e-8, momentum=0.9)
		model.compile(loss=tf.keras.losses.Huber(), optimizer = optimizer, metrics=["mae"])
		history = model.fit(train-set, epochs=100, callbacks=[lr_schedule])

	```

- batch_size 의 변경 (256) : loss 에 노이즈가 생길 경우 고려해볼 파라미터 
- 하이퍼파라미터를 다양하게 실험해 봐야 함