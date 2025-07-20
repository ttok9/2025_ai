### 논문 요약: "Rewrite-Retrieve-Read: An Architecture for Retrieval-Augmented Large Language Models" (arXiv:2305.14283v3)

#### Abstract
이 논문은 retrieval-augmented LLM에서 쿼리 재작성(Query Rewriting) 관점의 새로운 프레임워크 "Rewrite-Retrieve-Read"를 제안합니다. 기존 retrieve-then-read 파이프라인과 달리, 입력 텍스트와 필요한 지식 간 격차를 쿼리 생성으로 해결합니다. LLM으로 쿼리 생성 → 웹 검색 엔진으로 컨텍스트 검색 → LLM으로 읽기. 프리즈된 모듈과 쿼리 정렬을 위해 trainable scheme 제안: 소형 언어 모델을 rewriter로 사용, LLM 피드백으로 강화 학습. Open-domain QA와 다중 선택 QA 태스크에서 성능 향상 증명.

#### Introduction
LLM은 few/zero-shot 설정에서 언어 처리 능력이 뛰어나지만, hallucination과 지식 업데이트 문제(예: Yao et al., 2023)로 신뢰성 떨어짐. Retrieval-augmented LLM은 외부(비파라미터) + 내부(파라미터) 지식 결합으로 해결(예: Lewis et al., 2020). 그러나 쿼리 적응(Query Adaptation) 미고려. 제안: Rewrite-Retrieve-Read – 쿼리 재작성으로 입력-검색 갭 브릿지. 인터넷 검색 엔진 사용 + trainable rewriter로 정렬.

#### Methods
- **Rewrite-Retrieve-Read Framework**: 데이터셋 D = {(x, y)_i} (x: 입력, y: 출력)에서 (i) Query Rewrite: LLM few-shot 프롬프트로 쿼리 \(\tilde{x}\) 생성, (ii) Retrieve: 웹 검색 엔진(Bing)으로 컨텍스트 검색, (iii) Read: [doc, x] 이해해 \(\hat{y}\) 예측.
- **Trainable Scheme**: 프리즈 LLM 문제 해결 위해 T5-large(770M 파라미터) rewriter \(G_\theta\) 사용. 두 단계 훈련:
  - **Rewriter Warm-up**: LLM으로 pseudo 데이터 생성, 필터링 후 D_Train = {(x, \tilde{x}) | \hat{y} = y}. Log-likelihood 손실:
    \[
    L_{warm} = -\sum_t \log p_\theta(\hat{\tilde{x}}_t | \tilde{x}_{<t}, x)
    \]
  - **Reinforcement Learning**: MDP(Markov Decision Process)로 모델링(상태 S, 행동 A, 전이 P, 보상 R, 할인 \(\gamma\)). PPO(Proximal Policy Optimization) 최적화:
    \[
    \max_\theta E_{\hat{\tilde{x}} \sim p_\theta(\cdot|x)} [R(x, \hat{\tilde{x}})]
    \]
    Advantage: GAE(Generalized Advantage Estimation):
    \[
    \delta_t = R(s_t, a_t) + V_\phi(s_{t+1}) - V_\phi(s_t), \quad \hat{A}_\theta^t(s_t, a_t) = \sum_{t'=0}^\infty \lambda^{t'} \delta_{t+t'}
    \]
    보상: 태스크 메트릭(Exact Match/F1) + KL 정규화:
    \[
    R(s_t, a_t) = R_{lm}(\hat{\tilde{x}}, y) - \beta KL(\pi_\theta \| \pi_0)
    \]
    손실: Policy + Value Loss.

#### Results
- **데이터셋**: Natural Questions(NQ, Kwiatkowski et al., 2019), TriviaQA(TQA, Joshi et al., 2017), WebQuestions(WebQ, Berant et al., 2013), SQuAD(라자푸르 et al., 2016).
- **성능**: Exact Match(EM)에서 retrieve-then-read 베이스라인 대비 ↑: NQ 45.2% → 50.6%, TQA 69.8% → 72.1%. RAG/REPLUG 등 SOTA 초과. 검색 엔진으로 Bing 사용, 검색 비용 줄임.
- **Trainable 효과**: Warm-up + RL로 EM ↑(NQ 48.1% → 50.6%). 데모/피드백 영향 분석: LLM 피드백으로 rewriter 최적화.
- **시각화/케이스**: 쿼리 재작성 예시(그림: 원본 쿼리 → 재작성 쿼리 → 더 나은 검색 결과).

#### Conclusion
Rewrite-Retrieve-Read는 쿼리 재작성으로 retrieval-augmented LLM 효율 ↑. Trainable rewriter로 프리즈 모듈 적응. QA 태스크에서 SOTA, 확장성 증명. 미래: 더 넓은 태스크/LLM 적용.

#### Limitations
환각 완벽 방지 못 함. 웹 검색 의존으로 프라이버시/신뢰성 문제. RL 훈련 비용 ↑, 소형 rewriter지만 LLM 피드백 필요.
