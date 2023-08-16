---
title: Sample-based Learning Methods - 03. Week 3. Temporal Difference Learning Methods for Control
date: '2023-07-12 10:00:00'
tags:
- Coursera
- Alberta Machine Intelligence Institute
- 앨버타 대학교
- 강화학습
- Martha White
- Adam White
- Temporal Difference Learning
- TD Learning
related: true
categories:
- AI
- Reinforcement Learning
toc: false
use_math: true
---

## 관련 자료 (RLbook Pages 129-134)

- Sarsa: On-policy TD Control

	+ 개요
		* 제어 문제를 위한 TD 예측 방법
			- 평소와 같이 GPI (generalized policy iteration) 패턴을 따른다.
			- 이번에는 평가 혹은 예측 부분을 TD 방식을 이용한다.
			- 몬테카를로 방식과 마찬가지로 탐색과 이용사이의 등가교환 문제에 직면하게 된다.
				+ 이에 대한 2가지 접근 방법 : On-policy, Off-policy
			- 이 장에서는 On-policy TD 제어 방법을 활용한다.
		* On-policy TD 제어
			- 첫 단계로 상태가치함수가 아닌 행동가치함수를 학습해야 한다.
			- On-policy 방식에서는 모든 상태 $s$ 및 동작 $a$ 에 대해 현 정책 $\pi$ 에 대한 $q_\pi (s,a)$ 를 추정해야 한다.
				+ 이는 위 $v_\pi$ 학습의 TD 방식과 거의 동일하게 수행할 수 있음.
					* 에피소드는 상태와 상태-행동 쌍의 번갈아가는 순서로 구성되는 것을 기억한다.
					
			![6_4_1_td_sequence](/assets/images/posts/6_4_1_td_sequence.png)
			
			- 이전 상태가치함수에 대한 TD 예측문제와의 비교
				+ 이전 섹션에서는 상태에서 상태로의 전이를 고려하고 상태의 가치를 학습했다면 이제는 상태-동작 쌍에서 상태-동작 쌍으로의 전이를 고려해서 상태-동작 쌍의 가치를 학습한다.
				+ 위 두 경우의 형식은 동일하다 : 보상 프로세스가 있는 Markov chain
				+ TD(0)에 따른 상태 값의 수렴을 보장하는 정리는 행동 값에 대한 알고리즘에도 적용된다.
			
			![6_4_2_td_algorithm_for_action_value](/assets/images/posts/6_4_2_td_algorithm_for_action_value.png)

			- 업데이트 식과 Sarsa 알고리즘에 대해
				+ 이 업데이트는 비종료 상태 $S_t$ 로부터 모든 전이 후에 수행된다.
				+ 만약 $S_{t+1}$ 이 종료 상태라면, $Q(S_{t+1}, A_{t+1})$ 은 0으로 정의된다.
				+ 이 규칙은 상태-동작 쌍에서 다음 상태-동작 쌍으로의 전이를 이루는 이벤트인 $(S_t, A_t, R_{t+1}, S_{t+1}, A_{t+1})$ 의 모든 요소를 사용한다.
				+ 이러한 다섯 요소에서 알고리즘의 이름 Sarsa 가 유래되었다.
			
			![6_4_3_sarsa_backup_diagram](/assets/images/posts/6_4_3_sarsa_backup_diagram.png)

			- Sarsa 예측 방법 기반 정책 제어 알고리즘
				+ 모든 On-policy 방법과 마찬가지로 행동 정책 $\pi$ 에 대해 $q_\pi$ 를 지속적으로 추정하고 동시에 $\pi$ 를 $q_\pi$ 에 대한 탐욕 정책으로 변경한다.
				
			- Sarsa 알고리즘과 수렴 속성
				+ Sarsa 알고리즘의 수렴은 정책이 Q에 의존하는 특성에 따라 달라진다.
					* 예 : $\varepsilon$-greedy, $\varepsilon$-soft 정책 등
				+ Sarsa는 모든 상태-행동 쌍이 무한한 횟수로 방문되고 정책이 한계에서 탐욕적인 정책으로 수렴하는 경우 (예를들어 $\varepsilon$-greedy 정책에서 $\varepsilon = \frac{1}{t}$) 확률 1로 최적 정책과 동작-가치함수로 수렴한다. 

			![6_4_4_sarsa_psuedo_code](/assets/images/posts/6_4_4_sarsa_psuedo_code.png)

	+ 예제 6.5: Windy Gridworld
		* 문제의 정의
			- 일반적인 그리드월드 (시작 상태, 목표 상태 존재) 에서 한 가지 다른 점이 존재
				+ 그리드 중간을 통해 위로 향하는 크로스윈드가 존재함
					* 바람의 세기는 열마다 다름.
					* 각 열 아래에 바람의 세기가 표시되어 있음.
			- 행동 : 상, 하, 좌, 우
				+ 중간 지역에서는 바람에 의해 위로 이동됨
					* 예를 들어, 목표 상태의 오른쪽 셀에서 왼쪽으로 이동하면 목표 상태의 바로 위의 셀로 이동함
			- 할인이 없는 에피소드 기반의 작업
			- 목표에 도달할 때까지 보상은 -1 로 제공
		* 결과
		
			![6_4_5_example_6_5_windy_gridworld](/assets/images/posts/6_4_5_example_6_5_windy_gridworld.png)

			- 위 그래프는 $\varepsilon$-greedy Sarsa 를 적용한 결과를 보여준다. ($\varepsilon = 0.1, \alpha = 0.5$, $Q(s,a,)=0$ (초기값, 모든 s, a 에 대해))
			- 그래프의 증가하는 기울기는 시간이 지남에 따라 목표에 더 빨리 도달하였음을 보여준다.
			- 8000 번째 타임 스텝에서는 탐욕 정책이 이미 최적이었음 (inset 에서 해당 경로가 표시됨)
			- 지속적인 $\varepsilon$-greedy 탐사로 인해 평균 에피소드 길이는 약 17단계로 유지되었으며, 최소값인 15보다 2단게 더 많았다.
			- Monte Carlo 방법은 모든 정책에 대해 종료가 보장되지 않기 때문에 여기에 쉽게 사용할 수 없음.
				+ 에이전트가 같은 상태에 머무르는 것을 유도하는 정책을 찾은 경우 다음 에피소드는 결코 종료되지 않음.
			- Sarsa 와 같은 온라인 학습방법은 이러한 문제가 없다.
				+ 에피소드 동안 빠르게 학습하여 이러한 정책이 좋지 않음을 배우고 다른 정책으로 전환한다.
			
- Q-learning: Off-policy TD Control

	+ 개요
		* 강화학습의 초기 돌파구 중 하나는 Q-learning (Watkins, 1989) 으로 알려진 Off-policy TD Control 알고리즘의 개발이었다.
		
		![6_5_1_q_learning_algorithm](/assets/images/posts/6_5_1_q_learning_algorithm.png)
		
		* 위 경우, 학습된 행동가치함수인 Q는 따르는 정책과는 독립적으로 최적의 행동가치함수인 $q_\pi$ 를 직접 근사함
			- 이것은 알고리즘의 분석을 크게 단순화하고 초기 수렴 증명을 가능하게 함
			- 정책은 여전히 영향을 미치며, 어떤 상태-행도 쌍이 방문되고 업데이트되는지를 결정함.
				+ 그러나 올바른 수렴을 위해 필요한 것은 모든 쌍이 계속해서 업데이트 되는 것 뿐임
				+ 이것은 일반적인 경우에 최적의 행동을 찾기 위해 보장된 어떤 방법이든 필요한 최소한의 요구사항임
			- 위 가정과 일반적인 확률 근사 조건의 변형에 따라, Q는 확률 1로 $q_\pi$ 에 수렴한다는 것이 증명되었음.
		* 아래는 Q-러닝 알고리즘의 psuedo code 이다.
		
		![6_5_2_q_learning_psuedo_code](/assets/images/posts/6_5_2_q_learning_psuedo_code.png)
		
	+ 예제 6.6: Cliff Walking
		* 이 그리드월드 예제에서는 Sarsa와 Q-러닝을 비교하여 On-policy (Sarsa) 및 Off-policy (Q-learning) 방법의 차이를 강조한다.
		
		![6_5_3_example_6_6_cliff_walking](/assets/images/posts/6_5_3_example_6_6_cliff_walking.png)
		
		* 문제의 정의
			- 표준적인 할인되지 않은 에피소드 형태
			- 시작 상태와 목표 상태가 존재
			- 행동 : 위, 아래, 오른쪽, 왼쪽
			- "Cliff" 로 표시된 영역으로의 이동을 제외한 모든 전이의 보상은 -1
			- "Cliff" 로 표시된 영역으로 진입하면 -100의 보상을 받고 즉시 시작지점으로 되돌아감.
		
		![6_5_4_example_6_6_cliff_walking_result](/assets/images/posts/6_5_4_example_6_6_cliff_walking_result.png)

		* 결과
			- 위 그래프는 $\varepsilon$-greedy 행동 선택 ($\varepsilon = 0.1$) 정책에 대한 Sarsa 와 Q-learning 방법의 성능을 보여준다.
				+ 초기 이후에 Q-러닝은 최적 정책인 절벽 가장자리를 따라 이동하는 경로의 값을 학습한다.
				+ 그러나 $\varepsilon$-greedy 정책 때문에 가끔 절벽에서 떨어지는 경우가 발생한다.
				+ 반면 Sarsa 는 행동 선택을 고려하여 멀지만 더 안정적인 경로를 학습한다.
				+ Q-러닝은 사실 최적의 정책을 학습하지만 온라인 성능은 Sarsa 보다 나쁘게 된다. (Sarsa 는 우회정책을 학습한다.)
				+ $\varepsilon$ 이 점차적으로 감소한다면 두 방법 모두 최적 정책으로 수렴하게 된다.
		
- Expected Sarsa

	+ 개요
		* Q-러닝과 동일하지만 다음 상태-행동 쌍에 대한 최대값 (Q-러닝) 대신 현재 정책에 따라 각 행동의 확률을 고려하여 예상값을 사용하는 학습 알고리즘을 가정해보자.
		* 즉 업데이트 법칙의 알고리즘은 아래와 같지만, 나머지 부분은 Q-러닝의 영역을 따른다고 가정한다.
		
		![6_6_1_expected_sarsa_algorithm](/assets/images/posts/6_6_1_expected_sarsa_algorithm.png)
		
		* 다음의 상태 $S_{t+1}$ 이 주어지는 측면에서 알고리즘은 결정론적으로 Sarsa 와 같은 움직임 이나 예상값을 사용하는 측면에서 Expected Sarsa 라고 부른다.

		![6_6_2_expected_sarsa_backup_diagram](/assets/images/posts/6_6_2_expected_sarsa_backup_diagram.png)
		
		* Expected Sarsa 의 백업 다이어그램은 위와 같다.
		* Expected Sarsa 는 계산적으로 Sarsa 보다 복잡하다.
			- 그러나 결과에서 $A_{t+1}$ 의 무작위 선택에 따른 분산을 제거해준다.
			- 즉, 같은 양의 학습경험을 할 경우 Sarsa 보다 성능이 조금 더 나으리라 기대할 수 있고, 실제로도 그렇다.
		
		![6_6_3_expected_sarsa_cliff_walking_result](/assets/images/posts/6_6_3_expected_sarsa_cliff_walking_result.png)	
		
		
		* 위의 결과는 cliff-walking 문제에서 Expected Sarsa 와 Sarsa, Q-learning 간 결과의 비교값이다.
			- Expected Sarsa는 이 문제에서 Sarsa에 비해 상당한 이점을 유지한다.
			- 또한, Expected Sarsa는 step-size 파라미터 $\alpha$ 의 다양한 값 범위에서 Sarsa에 비해 상당한 개선을 보여준다.
			- Cliff walking 에서 상태 전이는 모두 결정론적이며, 모든 무작위성은 정책으로부터 발생한다.
				+ 이와 같은 경우, Expected Sarsa는 $\alpha = 1$ 로 안전하게 설정할 수 있으며, 한계적인 성능 저하 없이 수렴 성능을 유지할 수 있습니다.
				+ 반면, Sarsa는 작은 $\alpha$ 값에서만 잘 동작하며, 이 경우 학습 초기 성능(Interim Performance)이 낮다.
					* Asymptotic Performance : 알고리즘이 충분한 학습을 진행한 후에 얻게 되는 최종적인 성능을 나타냄. 이는 알고리즘이 수렴하여 최적 정책과 가치 함수를 얻었을 때의 성능을 의미.
					* Interim Performance : 알고리즘이 학습 도중에 어떤 성능을 보이는지를 나타냄. 즉, 알고리즘이 아직 수렴하지 않은 초기 학습 단계에서의 성능을 평가하는 지표.
			- 이 Cliff walking 결과에서 Expected Sarsa 는 On-policy 로 사용되었지만, 목표 정책 $\pi$ 와 다른 정책을 사용하여 동작을 생성할 수 있으며, 이 경우 Off-policy 알고리즘이 된다.
			- 예를 들어, $\pi$ 가 탐욕 정책이가 동작이 더 탐색적인 경우, Expected Sarsa 는 정확히 Q-learning 이 된다.
				+ 이런 의미에서 Expected Sarsa 는 Q-learning 을 포괄하고 일반화하며, Sarsa 에 비해 신뢰할 수 있는 개선을 제공한다.
		* 적은 추가적인 계산 비용만 제외하면, Expected Sarsa 는 더 잘 알려진 TD 제어 알고리즘인 두 알고리즘을 완전히 압도할 수 있다.

## TD for Control

- Sarsa: GPI with TD

	+ 학습목표
		* GPI (generalized policy iteration) 을 TD 와 함께 사용하여 개선된 정책을 찾는 법 설명하기.
		* Sarsa 제어 알고리즘 서술하기.
		
	+ GPI (generalized policy iteration) 에 대한 복기
		* GPI : policy iteration (policy evaluation $\to$ policy improvement)
		* GPI in Monte-carlo : 에피소드 진행 후 policy evaluation $\to$ policy improvement)
			- 몬테카를로의 경우 정책개선 이전에 완전한 정책평가를 하지 않는다. (에피소드마다 정책평가, 정책개선을 한다.)

	+ TD within GPI
		* GPI 에 TD 를 적용하기 위해서는 행동가치함수를 학습해야 한다.
			- 이전 섹션에서 TD 를 상태가치함수로 학습 (상태-상태)
				+ 여기에서는 행동가치함수 (상태,행동-상태,행동) 의 값을 학습 : 이것을 Sarsa 예측이라고 함.
				
	+ Sarsa
		
		![sarsa_algorithm](/assets/images/posts/sarsa_algorithm.png)	
		
		* Sarsa 의 약어는 업데이트에 쓰인 데이터 요소를 뜻함
			- $S_t$ : state
			- $A_t$ : action
			- $R_{t+1}$ : reward
			- $S_{t+1}$ : next state
			- $A_{t+1}$ : next action

		* Sarsa 의 업데이트식은 상태가치함수를 업데이트하는 TD 식과 유사
			- 단, Sarsa 는 상태-행동 쌍에 대한 행동가치함수 $Q(S_t,A_t)$ 에 대해 업데이트함.
		* 위의 그림은 정책평가에 대한 내용만 담겨 있다. (고정된 정책에 대한 행동가치함수의 학습)
			- 그러나 GPI 의 요소를 이용, 제어 알고리즘으로 변환할 수 있다. (예: $\varepsilon$-greedy)
				+ Sarsa 제어 : TD 학습을 적용한 GPI 의 한 예

- Sarsa in the Windy GridWorld

	+ 학습목표
		* Sarsa 제어 알고리즘이 예제 MDP 에서 작동하는 방식 이해하기.
		* 학습 알고리즘의 성능 분석 방법을 경험하기
		
	+ The Windy Gridworld
		
		* 문제의 정의
		
			![sarsa_example_windy_gridworld_1](/assets/images/posts/sarsa_example_windy_gridworld_1.png)	

			- State : 각각 하나의 시작과 종료상태가 존재
				+ 열에 따라 바람이 위쪽 방향으로 불어, 의도한 행동의 결과와 다른 상태전이가 일어남
					* 예를 들어 위 그림에서 에이전트는 왼쪽으로 움직이는게 아니라 좌측 상단 (대각선) 으로 움직임
				+ 가장자리에 부딪힐 경우 아무 일도 일어나지 않음
			- Action : 에이전트는 4개의 방향으로 이동이 가능
			- Reward : 모든 상태에서 -1
				+ 에이전트가 최대한 빨리 종료상태에 도달하도록 동기부여함
			- Discount factor $\gamma$ : 1 (에피소딕)
			
		* 문제에 Sarsa 를 적용하고 그 결과를 해석
		
			![sarsa_example_windy_gridworld_2](/assets/images/posts/sarsa_example_windy_gridworld_2.png)	

			- Sarsa 설정값

				+ 정책 : $\varepsilon$-greedy action selection
					* $\varepsilon = 0.1$
				+ $\alpha = 0.5$
				+ 초기값 = 0
					* optimistic initial values : 초기 탐색 장려

			- 결과 해석
				+ 그래프 : 각 스텝 별로 몇 번의 에피소드를 끝냈는지를 의미
					* 위 결과는 각각 100번의 시행의 평균 값임
					* 첫 몇 번의 에피소드는 2000 스텝을 진행하고 종료됨
					* 그래프의 기울기는 점점 가파르게 상승하고 이는 짧은 스텝으로 에피소드를 종료함을 뜻함
					* 7000 스텝 즈음 기울기는 더이상 상승하지 않는다. (탐욕 정책의 개선 종료-최적화)
				+ 몬테카를로 방법은 위 방식에 맞지 않다.
					* 많은 정책이 종료상태까지 도달하지 못함
					* 몬테카를로 방식은 에피소드가 종료되어야 학습을 시작함
						- 따라서 결정론적 정책은 함정에 빠지고, 좋은 정책을 배우지 못하게 됨
							+ 예를 들어 초기 정책이 왼쪽으로 이동하는 것이라면 에피소드가 영원히 끝나지 않게 됨
				+ Sarsa 는 에피소드를 진행하며 현재의 정책이 안좋은 정책이라는 것을 학습하고, 정책을 바꾸게 된다.
			
## Off-policy TD Control : Q-learning

- What is Q-learning?

	+ 학습목표
		* Q-learning 알고리즘 서술하기.
		* Q-learning 과 벨만 최적 방정식 (Bellman Optimality equations) 간 관계 설명하기.

	+ The Q-learning algorithm
		* Q 러닝은 1989년에 개발되었고, 강화학습 알고리즘 중 첫번째 메인 온라인 학습 알고리즘이다.
		
		![q_learning_psuedo_code](/assets/images/posts/q_learning_psuedo_code.png)	

		* 위 그림은 Q-learning 알고리즘의 psuedo code 이다.
			- 에이전트는 상태에서 행동을 선택하고, 다음 상태와 보상을 관측한다.
			- 이후 업데이트를 진행하고 사이클이 반복된다.
			- 타 알고리즘과의 차이점은 업데이트 규칙에 있다.

		![q_learning_sarsa_bellman_equation](/assets/images/posts/q_learning_sarsa_bellman_equation.png)	
		
		* 위 그림은 벨만방정식과 Sarsa, Q-learning 간의 수식 비교이다.
			- Sarsa : 벨만방정식의 행동가치 식과 유사
				+ Sarsa 는 벨만방정식의 행동가치 식을 풀기 위한 샘플기반의 알고리즘이다.
				+ Sarsa 는 샘플기반의 정책 반복 (policy iteration) (벨만 방정식 행동가치함수 사용)
			- Q-learning : 벨만최적방정식의 행동가치 식과 유사
				+ Q-learning 또한 환경으로부터의 샘플을 이용해 벨만 방정식을 풀지만, 벨만 방정식 대신 벨만 최적 방정식을 사용한다.
				+ 즉, $q_*$ 를 바로 학습함으로서, 정책평가와 정책개선 단계를 번갈아 가며 진행할 필요가 없게 된다.
				+ Q-learning 은 샘플 기반의 가치 반복 (value iteration) (벨만 최적 방정식 행동가치함수 사용) 
		
		* Q-learning 은 Value iteration 과 마찬가지로 최적가치함수에 수렴한다. (에이전트가 모든 상태-행동 쌍에 대해 지속적으로 탐색한다는 가정)


- Q-learning in the Windy Grid World

	+ 학습목표
		* Q-learning 이 예제 MDP 에서 작동하는 방식 이해
		* 단일 MDP 에서 여러 학습 알고리즘의 성능을 비교하는 방식 경험
		* Q-learning 과 Sarsa 의 차이점 이해하기.

	+ The Windy Gridworld
	
		![q_learning_sarsa_windy_grid_world_1](/assets/images/posts/q_learning_sarsa_windy_grid_world_1.png)	

		* Sarsa 와 Q-learning 간 비교 ($\alpha = 0.5$)
			- Q-learning 이 Sarsa 보다 뛰어난 결과를 보여준 이유를 명확히 설명할 수 없지만, 아마 update target 이 더 stable 했기 때문으로 추정함.
				+ Sarsa 의 경우 탐색에 해당하는 샘플의 업데이트가 큰 영향을 줬을 수도 있음. (탐색적인 행동을 할 때마다 update target 이 변경될 수 있음)
				+ Q-learning 의 경우 max 값을 이용해 업데이트 하기 때문에, 한 행동이 이전 행동보다 더 낫다는 것을 학습할 때에 update target 이 변경됨. 
		
		![q_learning_sarsa_windy_grid_world_2](/assets/images/posts/q_learning_sarsa_windy_grid_world_2.png)	
		
		* Sarsa 와 Q-learning 간 비교 (Salsa : $\alpha = 0.1$)

			- 더 나은 Sarsa 의 결과를 위한 파라미터의 변경
				+ $\alpha = 0.1$
				+ 더 많은 Time Steps 의 진행
			- Sarsa 와 Q-learning 모두 동일한 정책으로 수렴함
			
	+ 강화학습은 파라미터의 값에 따라 다른 결과물을 보여줌.
		* $\alpha$, $\varepsilon$, initial values, length of the experiments


- How is Q-learning off-policy?

	+ 학습목표
		* Q-learning 이 importance sampling 없이 off-policy 로 동작할 수 있는 이유를 이해하기.
		* On-policy 기반과 Off-policy 기반 학습이 각각 제어 성능에 어떤 영향을 주는지 서술하기.
		
	+ On-policy 와 Off-policy 관점에서 Sarsa 와 Q-learning 비교
	
		![q_learning_sarsa_on_policy_off_policy](/assets/images/posts/q_learning_sarsa_on_policy_off_policy.png)	
		
		- On-policy 와 Off-policy 복습
			+ 에이전트는 목표정책(target policy) 의 예상 리턴값을 토대로 가치함수를 추측한다.
			+ 에이전트는 행동정책(behavior policy) 에 따라 실제 행동한다.
			+ 목표정책과 행동정책이 같다면, 에이전트가 On-policy 학습을 한다 고 한다.
			+ 목표정책과 행동정책이 다르다면, 에이전트는 Off-policy 학습을 한다 고 한다.

		- Sarsa : On-policy algorithm
			+ 에이전트가 다음에 수행할 작업의 값을 부트스트래핑 한다.
				* 다음 수행할 작업의 값은 행동정책 ($\pi$) 에 의해 샘플링한 값이다.
			+ 즉 Sarsa 는 On-policy 학습이다.
		- Q-learning : Off-policy algorithm
			+ 에이전트의 다음상태에 해당하는 행동값 중 가장 큰 값을 부트스트래핑한다.
				* 다음상태의 행동값 중 가장 큰 값은 탐욕정책(최적정책) ($\pi_* \noteq \pi$) 에 의해 샘플링된 값이다.
			+ 즉 Q-learning 은 Off-policy 학습이다.
			
	+ Q-learning 의 Behavior policy 와 Target policy
	
		![q_learning_behavior_policy_target_policy](/assets/images/posts/q_learning_behavior_policy_target_policy.png)	
		
		- 강화학습에서의 자연스러운 질문 : 목표정책(target policy) 과 행동정책(behavior policy)은 무엇인가?
			+ Q-learning 에서의 Target policy : 현재 값에 대한 탐욕 정책
			+ Q-learning 에서의 Behavior policy : 모든 상태-행동 쌍에 접근할 수 있는 어떠한 정책도 될 수 있음 (예: $\varepsilon$-greedy)
		- 위에서 볼 수 있듯 Target policy 와 Behavior policy 가 다르므로 Off-policy 라 할 수 있다.
		

	+ Q-learning with No importance sampling
	
		![q_learning_no_importance_sampling](/assets/images/posts/q_learning_no_importance_sampling.png)	

		- Q-learning 이 importance sampling 이 필요없는 이유
			+ 에이전트가 정해지지 않은 정책으로부터 행동값을 추정하고 있기 때문
				* importance sampling ratio 로 행동 선택의 차이를 수정할 필요가 없다.
		- 상태가치함수 추정의 예시
			+ 행동가치함수는 주어진 상태에서 각 행동의 리턴값을 나타낸다.
			+ 에이전트의 target policy 는 주어진 상태에서 각각의 행동을 할 확률을 나타낸다.
			+ 즉 에이전트는 주어진 상태에서 target policy 를 따를 때 예상되는 리턴을 위 2개의 항목을 결합하여 나타낼 수 있다. 
		- Q-learning 의 target policy - greedy policy
			+ 가장 큰 리턴값을 가지는 행동을 선택. 즉, 다른 행동은 확률 0 을 가지게 됨.
			+ 해당 상태에서의 리턴 예상값은 행동값의 최대 값과 동일하다.
			
	+ Q-learning 이 성능에 영향을 주는 경우
		* Q-learning 은 정책반복 (정책평가와 정책개선) 을 하지 않고 바로 최적값을 직접 학습한다.
		* 최적가치함수와 정책을 직접 학습하는 것은 효율적으로 보이나, 가끔 미묘한 경우가 존재한다.
		
			![q_learning_bad_case](/assets/images/posts/q_learning_bad_case.png)
			
			- 예 : $\varepsilon$-greedy 정책의 Q-learning 의 경우 Cliff walking 문제에서 Sarsa 보다 안좋은 결과를 보여준다.
		

## Expected Sarsa

- Expected Sarsa

	+ 학습목표
		* Expected Sarsa 알고리즘 설명
		
	+ 벨만방정식 (action-values) 과 Sarsa 알고리즘
	
		![expected_sarsa_bellman_equation_sarsa](/assets/images/posts/expected_sarsa_bellman_equation_sarsa.png)	

		- 위 벨만방정식을 살펴보면, 해당 상태-행동 쌍의 온전한 값을 구하기 위해 상태전이에 의한 다음상태의 합계와 정책에 의한 행동에 대한 합계를 구하는 것을 알 수 있다.
		- Sarsa 의 업데이트 식의 경우 Error 를 구하는 부분에서 다음 상태의 값을 샘플링하여 계산하는데, 이 샘플링 데이터는 상태전이와 정책에 의한 행동 요소가 들어가 있다.
			+ 이 때, 에이전트는 이미 정책을 알고 있으므로, 이 부분을 샘플링 데이터에 의지하지 않고 계산을 하면 어떻게 될까?
			
	+ Expected Sarsa 알고리즘
	
		![expected_sarsa_algorithm_1](/assets/images/posts/expected_sarsa_algorithm_1.png)	

		- 명시적으로 다음 행동의 예측값을 계산하는 것이 Expected Sarsa 의 핵심 아이디어이다.
		- Expected Sarsa 또한 벨만 방정식 (action-values) 에 기반을 두고 있기에 업데이트식의 형식은 타 알고리즘과 유사하다.
			+ 차이점은 TD error 값을 샘플링을 통한 부트스트래핑 값이 아닌 다음 행동의 예측값으로서 정책과 부트스트래핑 값을 이용해 계산을 해낸다는 점이다.
				* 이것의 의미는 매 타임스텝 마다 정책 하의 예측 값(부트스트래핑)을 계산해야 한다는 의미이다.
		
		![expected_sarsa_algorithm_2](/assets/images/posts/expected_sarsa_algorithm_2.png)			

		- Expected Sarsa 는 Sarsa 에 비해 더 안정적인 update target 을 가지게 된다.
			+ Sarsa 의 경우 샘플에 따라 잘못된 방향 (target) 으로 업데이트가 될 수 있다.
				* 물론 많은 업데이트를 통해 참 값으로 수렴하게 된다.
			+ Expected Sarsa 의 경우 곧바로 참 값 (target) 으로 업데이트가 이루어진다.
				* 이는 분산이 작아지는 효과를 가져온다.

		- Expected Sarsa 의 단점은 컴퓨팅 리소스 부분이다.
			+ 행동의 가지수가 늘어날 수록 계산 가격이 비싸진다. (특히 매 타임스텝마다 계산되는 부분이므로 더 부담스럽다.)

- Expected Sarsa in the Cliff World

	+ 학습목표
		* Expected Sarsa 의 행동을 예시 MDP에서 설명
		* Expected Sarsa 와 Sarsa 의 경험적 비교
	

	+ Cliff Walking 환경에서의 Sarsa 와 Expected Sarsa 비교 ($\varepsilon = 0.1$, 100회 학습, 50000회 독립시행 평균결과)
	
		![expected_sarsa_cliff_world_result_1](/assets/images/posts/expected_sarsa_cliff_world_result_1.png)	

		* Expected Sarsa 는 Sarsa 에 비해 더 큰 $\alpha$ 값을 사용할 수 있다.
			- 정책의 무작위성을 명시적 평균을 통해 해결
			- 이 문제에서의 상태이동은 결정론적이기에 다른 무작위성이 없다.
			- 즉 위 두 조건으로 Expected Sarsa 의 업데이트는 결정론적인 값이 된다.
		* Sarsa 의 업데이트의 경우 다음 행동에 크게 영향을 받게 된다.
		
	+ Cliff Walking 환경에서의 Sarsa 와 Expected Sarsa 비교 ($\varepsilon = 0.1$, 100000회 학습, 50000회 독립시행 평균결과)
		
		![expected_sarsa_cliff_world_result_2](/assets/images/posts/expected_sarsa_cliff_world_result_2.png)	
		
		* Expected Sarsa 는 $\alpha$ 값에 영향 없이 같은 결과를 보여줌
			- 업데이트가 결정론적이기 때문
			- 위 경우 step size 는 얼마나 목표 값에 빠르게 수렴하는지에만 영향을 줌
		* Sarsa 의 경우 $\alpha$ 값에 큰 영향을 받음
			- $\alpha$ 값이 크면 수렴에 실패하며, $\alpha$ 값이 작으면 오랜 시간 학습 끝에 Expected Sarsa 와 거의 동일한 결과를 보여줌

- Generality of Expected Sarsa

	+ Sarsa, Q-learning, Expected Sarsa 간 비교 (도입부)
		* Sarsa 와 Expected Sarsa 는 동일한 벨만 방정식으로부터 유도되었다는 점에서 유사성이 있음.
		* Q-learning 과 Expected Sarsa 간에는 어떠한 관계가 있을까?

	+ 학습목표
		* Expected Sarsa 가 importance sampling 없이 off-policy 학습을 할 수 있는 이유 설명
		* Expected Sarsa 가 Q-learning 의 일반화 버전인 이유
		
	+ Off-policy Expected Sarsa
	
		![expected_sarsa_off_policy](/assets/images/posts/expected_sarsa_off_policy.png)	

		* (On-policy 의 경우를 가정) On-policy 의 경우에도 Expected Sarsa 는 실제 다음 선택된 행동 관련 업데이트를 하는 것이 아닌 정책 $\pi$ 에 관한 업데이트를 진행함
			- 즉, $\pi$ 가 behavior policy 와 같을 필요가 없다고 볼 수 있음. 
			- 위의 관점은 Expected Sarsa 가 Q-learning 과 같이 importance sampling 없이 Off-policy 학습이 가능함을 보여줌.
			
	+ Greedy Expected Sarsa
	
		![expected_sarsa_greedy_q_learning_1](/assets/images/posts/expected_sarsa_greedy_q_learning_1.png)
		
		* 만약 학습할 target policy 가 탐욕정책이라면 어떻게 될까?
			- 이는 다음 상태에서 최대값을 이끌어내는 행동을 수행하는 것이고, Q-learning 과 동일한 방식이다.
			
			![expected_sarsa_greedy_q_learning_2](/assets/images/posts/expected_sarsa_greedy_q_learning_2.png)
			
			- 즉, Q-learning 은 Expected Sarsa 의 특별한 케이스이다.

- Week 3 Summary

	+ TD control and Bellman equations
	
		![td_control_and_bellman_equations](/assets/images/posts/td_control_and_bellman_equations.png)
		
		* TD 제어는 벨만 방정식에 기반을 둔다.
			- Sarsa
				+ 벨만 방정식의 샘플 기반 버전
				+ $q_\pi$ 를 학습
				+ On-policy 알고리즘 (현재 정책의 action value 를 학습)
			- Q-learning
				+ 벨만 최적 방정식을 이용
				+ $q_*$ 를 학습
				+ Off-policy 알고리즘 (현재 정책과 무관하게 optimal action value 를 학습)
			- Expected Sarsa
				+ Sarsa 와 동일한 벨만 방정식을 이용
				+ 그래나 Sarsa 와 샘플링을 하는 방식이 다름
					* 다음 행동가치의 예측값 (Sum) 을 활용함.
				+ On-policy, Off-policy 둘다 가능 
					* behavior policy 가 deterministic 하다면 On-policy 가 된다.
					* behavior policy 가 탐욕정책이라면 Q-learning 과 동일한 알고리즘이 되어버린다. (Q-learning 은 Expected Sarsa 의 특이 케이스)
					
	+ Subtleties with off-policy control (Off-policy control 의 미묘함)
	
		![subtleties_with_off_policy_control_1](/assets/images/posts/subtleties_with_off_policy_control_1.png)
		
		* Online 학습에서 Q-learning 은 Sarsa 보다 값이 안좋게 나오는데, 이는 탐색정책의 랜덤성 때문이다.
		
		![subtleties_with_off_policy_control_2](/assets/images/posts/subtleties_with_off_policy_control_2.png)

		* Expected Sarsa 의 경우 Online 학습에서 Sarsa 와 동일한 결과를 보여주면서, 동시에 더 좋은 학습률을 보여준다.
			- 학습한 정책이 동일하다.
			- 다음 행동가치의 예측값으로 학습하기 때문에, behavior policy 에 학습의 영향을 받는 Sarsa 보다 더 안정적인 학습을 한다.



- Chapter summary

	+ TD learning
		* 예측 : TD 방식은 예측 문제 해결을 위한 Monte Carlo 방법의 대안임.
		* 제어 : 두 경우 모두 제어 문제에 대한 확장은 동적 프로그래밍에서 추상화한 일반화된 정책 반복(GPI)의 아이디어를 통해 이루어짐
			- 근사 정책과 가치 함수가 둘 다 최적을 향해 움직이는 방향으로 상호 작용해야 한다는 아이디어임
			- 가치 함수를 구동하여 현재 정책의 수익을 정확하게 예측 (예측의 문제)
			- 현재 가치 함수와 관련하여 정책을 국지적으로 개선(예: e-greedy)하도록 유도 (정책개선)

	+ TD 제어 방법의 분류
		* 경험을 기반으로 하는 경우에서의 탐색 문제가 발생한다.
		* On-policy : Sarsa
		* Off-policy : Q-learning, Expected Sarsa
		* 이 외에 행위자 비평 방법 (Actor-Critic) 이 있는데 이러한 방법은 13장에서 다룸.


	+ TD learning 의 장점
		* 가장 널리 사용되는 강화학습 방법
			- 매우 단순
				+ 최소한의 계산으로 환경과의 상호 작용에서 생성된 경험에 온라인으로 적용할 수 있음
				+ 작은 컴퓨터 프로그램으로 구현할 수 있는 단일 방정식으로 거의 완벽하게 표현
			- 단순한 알고리즘을 확장하여 약간 더 복잡하고 훨씬 강력하게 만들 수 있음
				+ 본질은 TD 의 것 그대로임. 
					* 적은 계산으로 온라인경험 처리 가능
					* TD 오류에 의해 구동됨

	+ 이 장에서 다룬 TD 학습법의 특징
		* 1 Step
		* Tabular
		* 모델이 없는 TD 방법

	+ 앞으로 배울 내용
		* n단계 형식(Monte Carlo 방법 관련)과 환경 모델을 포함하는 형식(계획 및 동적 프로그래밍 관련)으로 확장
		* 테이블(딥 러닝 및 인공 신경망 관련)이 아닌 다양한 형태의 함수 근사치로 확장

	+ 일반적인 방법으로서의 TD
		* 강화 학습의 문제 맥락에서 TD 방법에 대해 살펴보았지만 실제로 TD 방법은 이보다 더 일반적임
			- 동적 시스템에 대한 장기 예측을 학습하기 위한 일반적인 방법
			- 강화학습이 아닌 순수한 예측의 방법으로 분석
				+ 재무 데이터, 수명, 선거 결과, 날씨 패턴, 동물 행동, 발전소에 대한 수요, 고객 구매 예측 등의 문제에도 쓰일 수 있음
		* 아직 TD 학습 방법의 잠재적 응용은 광범위하게 탐색되지 않음.
