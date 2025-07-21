https://www.promptingguide.ai/techniques/cot

Prompt Engineering Guide: Chain-of-Thought (CoT) Prompting 페이지 요약
이 페이지는 Prompt Engineering Guide 사이트의 “Chain-of-Thought Prompting” 섹션으로, 대형 언어 모델(LLMs)의 복잡한 reasoning 능력을 향상시키는 CoT 기법을 소개합니다. 주요 내용은 CoT의 기본 개념, 예시, 변형(Zero-shot CoT, Auto-CoT)을 다루며, few-shot prompting과 결합 시 효과가 크다고 강조합니다. 아래에 섹션별로 정리했습니다. (이미지와 코드 예시는 텍스트로 설명.)
1. Chain-of-Thought (CoT) Prompting 소개
	•	개요: Wei et al. (2022)의 논문에서 제안된 기법으로, 중간 reasoning 단계(intermediate reasoning steps)를 통해 복잡한 작업(예: 수학, 논리 문제)에서 성능을 높입니다. Few-shot prompting과 결합하면 더 나은 결과를 얻을 수 있으며, 충분히 큰 LLM에서 “발현(emerge)“되는 능력입니다.
	•	예시: 홀수 합이 짝수인지 판단하는 문제.
	◦	프롬프트 (Few-shot CoT): 여러 예시에서 홀수 추출 → 합 계산 → True/False 판단 과정을 단계별로 설명.
	▪	예: “The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1. A: Adding all the odd numbers (9, 15, 1) gives 25. The answer is False.” (다른 예시 반복 후 테스트 문제 제시.)
	▪	출력: “Adding all the odd numbers (15, 5, 13, 7, 1) gives 41. The answer is False.”
	◦	효과: 표준 prompting보다 정확. 단 하나의 예시만으로도 작동 가능.
	•	팁: 큰 모델에서 효과적. 사이트는 관련 AI 코스(20% 할인 코드: PROMPTING20)를 홍보합니다.
2. Zero-shot CoT Prompting
	•	개요: Kojima et al. (2022)의 아이디어로, 예시 없이 “Let’s think step by step” 문구를 추가해 reasoning을 유도합니다. Few-shot 예시가 부족할 때 유용.
	•	예시: 사과 계산 문제.
	◦	표준 프롬프트: “I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?”
	▪	출력 (잘못됨): “11 apples”
	◦	Zero-shot CoT 프롬프트: 위 문제 + “Let’s think step by step.”
	▪	출력 (정확): “First, you started with 10 apples. You gave away 2… so you had 6 left. Then… 11 apples. Finally… 10 apples.”
	•	장점: 간단한 추가 문구로 효과적. 예시 없이도 reasoning 유발.
3. Automatic Chain-of-Thought (Auto-CoT)
	•	개요: Zhang et al. (2022)의 접근으로, 수동 예시 작성의 한계를 극복. LLM을 사용해 reasoning 체인을 자동 생성하며, 다양성을 강조해 오류를 줄입니다.
	•	과정:
	◦	Stage 1: Question Clustering: 데이터셋 질문을 클러스터링(예: k-means)으로 분류해 다양성 확보.
	◦	Stage 2: Demonstration Sampling: 각 클러스터에서 대표 질문 선택 → Zero-shot CoT(“Let’s think step by step”)로 reasoning 생성. heuristics(질문 길이 60토큰, reasoning 단계 5개) 적용해 단순/정확한 예시 선택.
	•	이미지 설명: 다이어그램으로 클러스터링 → 샘플링 → CoT 생성 과정을 보여줌.
	•	장점: 수동 노력 최소화, 생성 오류 완화.
	•	코드: GitHub 링크 제공 (https://github.com/amazon-research/auto-cot).
추가 정보
	•	참조 논문: Wei et al. (2022), Kojima et al. (2022), Zhang et al. (2022). 각 논문 arXiv 링크 포함.
	•	기타: 사이트는 AI 코스 홍보와 함께, CoT가 LLM의 emergent ability임을 강조. 코드 예시는 Markdown 형식으로 제시됨.

