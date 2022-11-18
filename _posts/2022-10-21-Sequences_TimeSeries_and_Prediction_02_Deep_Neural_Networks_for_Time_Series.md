---
title: Sequences, Time Series and Prediction - 02. Deep Neural Networks for Time Series
date: '2022-10-21 14:00:00'
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

# Deep_Neural_Networks_for_Time_Series

## Preparing features and labels

- 시계열에서의 Input 값과 Label 값
	+ Features : 입력되는 값
		* 예 : 이전 30개의 시점의 값 
	+ Labels : 예측할 값 (정답)
		* 예 : 미래 시점의 값
		
```
	# Generate a tf dataset with 10 elements (i.e. numbers 0 to 9)
	dataset = tf.data.Dataset.range(10)

	# Window the data but only take those with the specified size
	dataset = dataset.window(5, shift=1, drop_remainder=True)

	# Flatten the windows by putting its elements in a single batch
	dataset = dataset.flat_map(lambda window: window.batch(5))

	# Create tuples with features (first four elements of the window) and labels (last element)
	dataset = dataset.map(lambda window: (window[:-1], window[-1]))

	# Shuffle the windows
	dataset = dataset.shuffle(buffer_size=10)

	# Create batches of windows ( 한번에 여러 데이터 처리를 위함 )
	dataset = dataset.batch(2).prefetch(1)

	# Print the results
	for x,y in dataset:
	  print("x = ", x.numpy())
	  print("y = ", y.numpy())
	  print()
```

## Feeding windowed dataset into neural network

```
	def windowed_dataset(series, window_size, batch_size, shuffle_buffer):
	    """Generates dataset windows

	    Args:
	      series (array of float) - contains the values of the time series
	      window_size (int) - the number of time steps to include in the feature
	      batch_size (int) - the batch size
	      shuffle_buffer(int) - buffer size to use for the shuffle method

	    Returns:
	      dataset (TF Dataset) - TF Dataset containing time windows
	    """
	  
	    # Generate a TF Dataset from the series values
	    dataset = tf.data.Dataset.from_tensor_slices(series)
	    
	    # Window the data but only take those with the specified size
	    dataset = dataset.window(window_size + 1, shift=1, drop_remainder=True)
	    
	    # Flatten the windows by putting its elements in a single batch
	    dataset = dataset.flat_map(lambda window: window.batch(window_size + 1))

	    # Create tuples with features and labels 
	    dataset = dataset.map(lambda window: (window[:-1], window[-1]))

	    # Shuffle the windows 
	    dataset = dataset.shuffle(shuffle_buffer) // shuffle buffer 의 크기만큼 이동하면서 buffer 내에서 무작위로 하나씩 선택 (선택속도증가)
	    
	    # Create batches of windows
	    dataset = dataset.batch(batch_size).prefetch(1)
	    
	    return dataset
```

## Single layer neural network

```
	# Generate the dataset windows
	dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)

	# Build the single layer neural network
	l0 = tf.keras.layers.Dense(1, input_shape=[window_size])
	model = tf.keras.models.Sequential([l0])

	# Print the initial layer weights
	print("Layer weights: \n {} \n".format(l0.get_weights()))

	# Print the model summary
	model.summary()

	# Set the training parameters
	model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(learning_rate=1e-6, momentum=0.9))

	# Train the model
	model.fit(dataset,epochs=100)

	# Print the layer weights
	print("Layer weights {}".format(l0.get_weights()))
```

## Machine learning on time windows

- jupyter notebook 자료

## Prediction

- jupyter notebook 자료

## More on single layer neural network

- jupyter notebook 자료

## Deep neural network training, tuning and prediction

```

	dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)
	model = tf.keras.models.Sequential([
		tf.keras.layers.Dense(10, input_shape=[window_size], activation="relu")
		tf.keras.layers.Dense(10, activation="relu")
		tf.keras.layers.Dense(1)
	])

	model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(learning_rate=1e-6, momentum=0.9))
	model.fit(dataset, epochs=100, verbose=0)

```

- 우리가 선택한 학습률이 아니라 최적의 학습률을 선택할 수 있다면 더 좋은 결과가 나올 것
	+ callback 을 활용하는 기법
	
		```
		
			dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)
			model = tf.keras.models.Sequential([
				tf.keras.layers.Dense(10, input_shape=[window_size], activation="relu")
				tf.keras.layers.Dense(10, activation="relu")
				tf.keras.layers.Dense(1)
			])
		
			// 각 epoch 종료 시마다 callback 에서 호출, epoch 숫자값을 기준으로 학습률을 값으로 변경
			// 
			lr_schedule = tf.keras.callbacks.LearningRateScheduler(
				lambda epoch: 1e-8 * 10**(epoch / 20))
		
			optimizer = tf.keras.optimizers.SGD(learning_rate=1e-8, momentum=0.9)
		
			model.compile(loss="mse", optimizer=optimizer)
			
			history = model.fit(dataset, epochs=100, callbacks=[lr_schedule])
		
			// 트레이닝을 마치고 나면 epoch 당 학습률에 대한 epoch 당 오차를 플로팅
			// x 축은 learning rate, y축은 epoch 의 손실
			lrs = 1e-8 (10 ** (np.arange(100) / 20))
			plt.semilogx(lrs, history.history["loss"])
			plt.axis([1e-8, 1e-3, 0, 300])
		
		```

	+ 위에서 구한 learning_rate (7e-6) 로 재훈련
	
		```

			window_size = 30
			dataset = windowed_dataset(x_train, window_size, batch_size, shuffle_buffer_size)
			model = tf.keras.models.Sequential([
				tf.keras.layers.Dense(10, input_shape=[window_size], activation="relu")
				tf.keras.layers.Dense(10, activation="relu")
				tf.keras.layers.Dense(1)
			])

			optimizer = tf.keras.optimizers.SGD(learning_rate=7e-6, momentum=0.9)
			model.compile(loss="mse", optimizer=optimizer)
			model.fit(dataset, epochs=500)

			// 훈련 도중 산출한 손실 플로팅 코드
			loss = history.history['loss']
			epochs = range(len(loss))
			plt.plot(epochs, loss, 'b', label='Training Loss')
			plt.show()

			// 초기손실 (왜곡값) 자르기
			loss = history.history['loss']
			epochs = range(10, len(loss))
			plot_loss = loss[10:]
			print(plot_loss)
			plt.plot(epochs, plot_loss, 'b', label='Training Loss')
			plt.show()

			// mean absolute error 값 확인
			tf.keras.metrics.mean_absolute_error(x_valid, results).numpy()

		```

## Deep neural network

- jupyter notebook 참조