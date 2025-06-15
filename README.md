# IISL 2025 겨울 강화학습 스터디

## 스터디 일정 (예정)
- **2025-02-12**  
  - Ch.3 (Finite MDP), Ch.4 (DP)
- **2025-02-19**  
  - Ch.5 (MC), Ch.6 (TD)
- **2025-02-26**  
  - Ch.13 (Policy Gradient), Policy Gradient Paper
- **2025-03-05**  
  - DQN Paper
- **2025-03-12**  
  - DDPG Paper
- **2025-03-19**
  - TRPO, PPO Paper
- **2025-03-26**
  - SAC Paper

## 파일 구조
```
├── algorithms
│   ├── drl
│   │   ├── deep_rl.py
│   │   └── dqn.py
│   └── tabular
│       ├── base.py
│       ├── dynamic_programming.py
│       ├── monte_carlo.py
│       └── td.py
├── Environment
│   ├── coin_toss.py
│   ├── gym_test.py
│   └── gym_trainer.py
├── experiments
│   ├── run_dqn.py
│   ├── run_dqn_hard_coding.py
│   └── run_tabular.py
├── setup
│   └── dqn_arg.yaml
├── utils
│   └── tabular_utils.py
├── Paper
│   ├── Algorithms
│   └── Supplements
├── LICENSE
└── README.md
```

---

## 실험 실행 방법

프로젝트에는 탭형(표 기반) 알고리즘과 딥러닝 기반 알고리즘 예제가 포함되어 있습니다.

### Tabular 예제

`run_tabular.py` 스크립트를 실행하여 동전 뒤집기 환경에서 다양한 알고리즘을 테스트할 수 있습니다.

```bash
python experiments/run_tabular.py
```

### DQN 예제

딥러닝 기반 DQN 학습은 `run_dqn.py` 스크립트로 실행합니다. 기본 설정은 `setup/dqn_arg.yaml`에 정의되어 있으며, 실행 시 커맨드 라인 인자로 값을 덮어쓸 수 있습니다.

```bash
python experiments/run_dqn.py --config ./setup/dqn_arg.yaml
```

## Environment

### `coin_toss.py`

#### Parameters
- `init_state`: 초기 상태
- `p_head`: 앞면(Head)이 나올 확률
- `r_head`: 앞면(Head)일 때의 보상
- `r_tail`: 뒷면(Tail)일 때의 보상

#### State Space
- `{0, 1}` (0: Head, 1: Tail)

#### Action Space
- `{0, 1}` (0: Flip, 1: Pass)

#### Methods
- `step(action)`:  
  - 주어진 `action`을 수행하고 `(next_state, reward)`를 반환

- `reset(state)`:  
  - 환경을 특정 `state`로 초기화  
  - 만약 `state == None`이면 `init_state`로 리셋

- `true_v(policy)`:  
  - 주어진 `policy`에 대한 이론적 가치 함수 계산

---

## Algorithms

### `dynamic_programming.py`
- kwargs
  - `env`: 환경
  - `gamma`: discount factor
  - `threshold`: $\Delta V < \text{threshold}$이면 중단
  - `max_iter`: 최대 반복 횟수
  - `return_q`: 가치 함수 대신 Q-함수를 반환할지 여부

#### Functions
- `policy_eval(env, policy, history = False)`:
  - Dynamic Programming에 따라 주어진 `env`에서의 policy evaluation을 수행하고 `V(s)`를 반환
  - 만약 `history == True`이면 `V(s)`와 함께 iteration의 `history`를 반환

- `policy_improvement(env, policy, v)`:
  - Dynamic Programming에 따라 주어진 `(env, policy, v(s))`에 대해 policy improvement를 수행하고 새로운 `policy`를 반환

- `policy_iteration(env, init_policy)`:
  - `init_policy`로 부터의 policy iteration을 통해 `v(s), optimal policy`를 반환
  - 만약 `return_q == True`이면 `q(s, a), optimal policy`를 반환

- `value_iteration(env, init_v)`:
  - `init_v`로 부터의 value iteration을 통해 `v(s), optimal policy`를 반환
  - 만약 `return_q == True`이면 `q(s, a), optimal policy`를 반환

---

### `monte_carlo.py`
- kwargs
  - `env`: 환경
  - `gamma`: discount factor
  - `max_episode_num`: rollout의 episode 수
  - `max_episode_length`: rollout의 episode 길이
  - `show_progress`: rollout 상황 출력 여부

#### Functions
- `first_visit_mc_prediction(env, policy)`:
  - Monte Carlo에 따라 주어진 `env`에서의 first visit MC prediction을 수행하고 `v(s)`를 반환

- `off_policy_mc_prediction(env, policy)`:
  - Monte Carlo에 따라 주어진 `env`에서의 off-policy MC prediction을 수행하고 `q(s, a)`를 반환

- `on_policy_first_visit_mc_control(env)`:
  - Monte Carlo에 따라 주어진 `env`에서의 on-policy first visit MC control을 수행하고 `q(s, a), optimal policy`를 반환

- `off_policy_mc_control(env, behavior_policy, Polyak_Ruppert = False)`:
  - Monte Carlo에 따라 주어진 `env`에서의 off-policy MC control을 수행하고 `q(s, a), optimal policy`를 반환
  - 만약 `Polyak_Ruppert == True`이면 `Polyak-Ruppert averaging`을 사용하여 `q(s, a), optimal policy`를 반환

---

### `td.py`
- kwargs
  - `env`: 환경
  - `gamma`: discount factor
  - `alpha`: learning rate
  - `max_episode_num`: 최대 episode 개수
  - `max_episode_length`: episode 길이
  - `epsilon_greedy`: epsilon-greedy policy 사용 여부
  - `epsilon`: epsilon-greedy policy의 epsilon

#### Functions
- `td_prediction(env, policy)`:
  - TD에 따라 주어진 `env`에서의 TD prediction을 수행하고 `v(s)`를 반환

- `sarsa(env)`:
  - TD에 따라 주어진 `env`에서의 SARSA를 수행하고 `q(s, a), optimal policy`를 반환

- `q_learning(env)`:
  - TD에 따라 주어진 `env`에서의 Q-learning을 수행하고 `q(s, a), optimal policy`를 반환

- `double_q_learning(env)`:
  - TD에 따라 주어진 `env`에서의 double Q-learning을 수행하고 `q(s, a), optimal policy`를 반환

---
