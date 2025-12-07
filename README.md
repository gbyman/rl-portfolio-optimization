# rl-portfolio-optimization: SOXL & JEPQ
강화학습(PPO)을 활용하여 고변동성 자산(SOXL)과 고배당 자산(JEPQ)을 혼합한 최적의 포트폴리오 운용 전략을 학습하는 프로젝트입니다.

## Project Overview
변동성이 극심한 **3배 레버리지 ETF(SOXL)**와 안정적인 **커버드콜 ETF(JEPQ)**를 결합하여, 하락장 방어와 수익률 극대화를 동시에 추구하는 AI 에이전트를 개발하는 것을 목표로 합니다.

###  프로젝트 핵심목포
1. **Human Rule vs AI:** 인간의 직관적 전략(1표준편차 활용, 리밸런싱)을 AI가 학습을 통해 넘어설 수 있는지 검증합니다.
2. **현실적인 환경 구축:** 월 적립식 투자, 배당금 재투자 등 실제 투자 시나리오를 환경(Env)에 구현합니다.(거래 수수료 및 세금 제외)
3. **변동성 대응:** 주가의 단순 등락이 아닌, 통계적 지표(Z-Score)를 학습시켜 변동성 국면에서의 대응 능력을 실험합니다.

## 기술 스택
* **Language:** Python 3.10+
* **RL Library:** Stable Baselines3 (PPO), Gymnasium
* **Data Source:** `yfinance` (Yahoo Finance API)
* **Analysis:** Pandas, Matplotlib, NumPy

## 강화학습 환경 설계 (MDP)
AI 에이전트는 'Gymnasium' 라이브러리로 구축된 커스텀 환경에서 상호작용하며 학습합니다.

### 상태 (Observation Space)
에이전트는 총 9개의 차원을 통해 시장과 포트폴리오 상태를 관찰합니다.
* **Portfolio Info:** 현금 잔고, 종목별 보유 수량(SOXL, JEPQ), 현재 포트폴리오 가치, SOXL 수익률
* **Market Data:** 종목별 현재가, **Z-Score(20일 이동평균 대비 이격도)**, RSI

### 행동 (Action Space)
에이전트는 5가지 이산적 행동(Discrete Actions) 중 하나를 선택합니다.
* `0`: **Hold** 관망 (자동 적립 및 배당 재투자는 백그라운드 수행)
* `1`: **Buy SOXL** 고정 금액 $1,000 매수
* `2`: **Rebalance Weak** SOXL 10% 매도 → JEPQ 전환 (약한 리밸런싱)
* `3`: **Rebalance Strong** SOXL 50% 매도 → JEPQ 전환 (위기 대응)
* `4`: **Aggressive Switch** JEPQ 10% 매도 → SOXL 매수 (공격적 투자 전환)

### 보상 (Reward Function)
* **기본 보상:** 포트폴리오의 일일 가치 변동률(Log Return)을 사용하여 자산 증식을 유도합니다.
* **Penalty:** 잦은 거래 방지를 위한 거래 비용 반영 (Optional)

## 실행 방법
본 프로젝트는 학습 속도와 라이브러리 호환성을 위해 **Google Colab** 환경에 최적화되어 있습니다. 별도의 설치 과정 없이 아래 배지를 클릭하여 바로 실행해 보시는 것을 권장합니다.

### 1. Google Colab에서 바로 실행하기 (권장)
아래 버튼을 클릭하면 Google Colab에서 소스 코드를 바로 열고 실행할 수 있습니다.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/gbyman/rl-portfolio-optimization/blob/main/main.ipynb)

1. 위 배지를 클릭합니다.
2. Colab 상단 메뉴에서 **[런타임]** > **[모두 실행]**을 클릭하면 데이터 다운로드부터 학습, 시각화까지 자동으로 진행됩니다.
   *(GPU 런타임을 사용하시면 학습 속도가 더 빠릅니다)*

---

### 2. 로컬 환경에서 실행하기
로컬에서 실행하실 경우 Python 3.10 이상의 환경이 필요합니다.

**Step 1. 저장소 클론 및 라이브러리 설치**
```bash
git clone [https://github.com/gbyman/rl-portfolio-optimization.git](https://github.com/gbyman/rl-portfolio-optimization.git)
cd rl-portfolio-optimization
pip install -r requirements.txt
