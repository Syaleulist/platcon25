# 📄 PlatCon-25 논문 정리 — 신조어 탐지를 위한 문맥 인식 개체명 인식(NER)

## 기본 정보

| 항목 | 내용 |
| --- | --- |
| 논문 제목 | Context-Aware Named Entity Recognition for Neologism Detection: A Deep Learning Approach to Bridging Generational Language Gaps |
| 저자 | Junhaeng Lee, HyunYoung Oh, SangWoo Park, Young-myoung Kang* (교신저자) |
| 소속 | 성결대학교 컴퓨터공학과 (Sungkyul University, Anyang, Korea) |
| 학회 | PlatCon 2025 (2025.08.25 ~ 08.27) |
| 형식 | IEEE 템플릿, 6페이지 Full Paper |
| 키워드 | Deep Learning, Generational Communication, Korean Neologisms, Named Entity Recognition, NLP |

---

## 💡 한 줄 요약

> 신조어를 하나의 개체명(NW)으로 정의하고 KoELECTRA 기반 문맥 인식 NER 모델로 7개 개체 유형을 학습했다. 그 결과 신조어 F1-score를 **실제 불균형 분포에서 0.89, Balanced 구성에서 0.91, Uniform 구성에서 0.92**까지 얻었다. 사전·규칙 기반 탐지를 대체할 수 있는 가능성을 보였다.

## 📌 핵심 수치

| 구성 | 신조어(NW) F1 | Macro F1 | Micro F1 |
| --- | --- | --- | --- |
| Balanced | **0.91** | **0.88** | 0.88 |
| Original | 0.89 | 0.86 | **0.90** |
| Uniform Small | **0.92** | 0.80 | 0.79 |

---

## Abstract 요약

- 온라인·SNS에서 신조어가 빠르게 늘면서 **세대 간 언어 격차**가 커졌고, 신조어를 제때 정확하게 탐지하기가 어려워졌다.
- 기존의 **사전·규칙 기반 방법**은 동적이고 문맥에 의존하는 표현을 잡아내지 못한다.
- 본 연구는 **KoELECTRA 기반 문맥 인식 NER 모델**을 제안하고, 개체 분포가 서로 다른 대규모 데이터셋으로 평가했다.
- 신조어 F1-score는 실제 불균형 조건에서 **0.89**, 통제된 균등 조건에서 최대 **0.92**를 기록했다. 다양한 개체 유형에서도 고르게 인식했다.

---

## 1. Introduction

### 연구 배경
- 국가, 인종, 종교, 세대 간 갈등이 전 세계적으로 커지고 있으며, 한국에서는 **세대 갈등**이 핵심 사회 문제가 되었다.
- 2025년 전국 설문에서 응답자의 **약 80%**가 세대 갈등을 심각하다고 답했고, 주요 원인으로 연령대 간 소통 장벽과 협업의 어려움을 꼽았다 [1].
- 이 문제는 가치관 차이를 넘어 **세대별 언어 표현 방식과 소통 방식의 근본적 차이**에서 비롯된다.
- 인터넷과 SNS의 확산으로 신조어의 생성과 전파가 크게 빨라졌다 [2]. 한국에서는 신조어가 대중매체, 방송, 공공 담론에서도 폭넓게 쓰인다 [3].
- 세대 간 신조어 인식·이해 격차는 정보 전달뿐 아니라 **정보 해석과 접근성의 불평등**까지 키운다 [4].

### 기존 방법의 문제
- 기존 신조어 탐지는 주로 **정적 사전 기반 방법**이다. 대상 코퍼스의 단어 빈도를 사전과 비교해 미등재어를 찾는다 [5].
- 언어적으로 안정된 신조어는 잘 찾지만, **일시적이고 문맥에 민감하며 빠르게 변하는 표현**은 잡지 못한다. 실시간 언어 변화에도 대응하지 못한다.

### 본 연구가 정의한 두 가지 핵심 한계
1. 신조어 생성·확산의 **시간적 변동성과 문맥 의존성**을 수용하지 못한다.
2. **세대 간 언어 비대칭**을 키워 소통을 방해하고 정보 접근성 격차를 고착시킨다.

### 기여
- 문맥 민감 환경에서 신조어 탐지에 최적화된 **딥러닝 기반 NER 프레임워크**를 제안했다.
- 신조어 탐지를 **다중 개체 인식 구조(7개 태그)** 안에 통합해 일반화 성능과 실용성을 높였다.
- 데이터 분포에 따른 견고성을 검증하기 위해 **Original / Balanced / Uniform** 세 가지 데이터 구성을 만들었다.
- Balanced 구성에서 **Macro F1 0.88, 신조어 F1 0.91**을 달성했다.

---

## 2. Related Work

### 2.1 신조어 수집 (Neologism Collection)
- 기존 방식은 커뮤니티, 블로그, SNS, 위키백과 등에서 후보어를 추출한다 [4]. 코퍼스 내 저빈도이면서 사전에 없는 단어를 후보로 잡고, 정제된 사전과 비교해 최종 목록을 만든다 [9].
- **한계**
  - 신조어는 생성, 확산, 소멸 주기가 매우 짧고, 의미와 용법이 시기나 커뮤니티에 따라 달라진다.
  - 저빈도 토큰, 의도적 오타, 줄임말, 혼성어, 이모지 결합 등 형태가 다양해 정형화된 패턴 인식으로 잡기 어렵다.
  - 의미 모호성이나 다의성 때문에 딥러닝 학습에 필요한 고품질 주석 데이터를 확보하기 어렵다 [9].

### 2.2 개체명 인식 (NER)
- NER은 비정형 텍스트에서 인물, 장소, 조직, 시간 표현, 도메인 특화 개체(화합물, 의약품 등)를 식별하고 분류하는 NLP의 기본 과제다 [10].
- **규칙 기반**: 수작업 규칙과 도메인 사전을 사용한다. 정밀도는 높지만 확장성이 낮고 재현율이 떨어진다 [11].
- **딥러닝 기반**: 특히 **BiLSTM-CRF**가 양방향 문맥 의존성을 포착하며 표준 구조로 자리 잡았다 [12].
- **사전학습 언어모델·Transformer**: 수작업 특징 설계 없이 문맥 인식 표현을 학습하며, 레이블 데이터가 적어도 높은 성능을 낸다 [13].

---

## 3. Methodology

### 3.1 데이터셋
- **AI Hub 「대규모 웹데이터 기반 한국어 말뭉치」** [6]
  - 약 **10억 어절** 규모의 웹 기반 한국어 코퍼스
  - 사회, 문화, 과학, 경제 등 **17개 주제 카테고리의 뉴스 기사** 중심
  - 자동 저작 도구로 7개 개체명을 주석
  - 링크: https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=624

| 태그 | 개체 유형 |
| --- | --- |
| OG | Organization (조직) |
| AF | Artifact (인공물) |
| CV | Civilization (문명·문화) |
| FD | Field of Study (학문 분야) |
| TR | Theory/Rule (이론·법칙) |
| TM | Term (용어) |
| **NW** | **Neologism (신조어)** |

#### 신조어 정의 및 필터링
원본 NW 태그는 "표준 국어사전에 없지만 일정 빈도 이상 등장한 신규 용어"에 붙어 있었다. 정의가 모호하고 일관성이 부족했기 때문에, 신조어를 다음 **세 범주로 엄격히 한정**했다 [14].

1. **복합 표현 (Compound expressions)**: 두 개 이상의 기존 단어가 결합된 표현
2. **혼성어 (Hybrid terms)**: 부분 단어 결합이나 형태소 혼성으로 만들어진 용어
3. **외래어·의미 확장 (Loanwords / Semantic extensions)**: 외래어이거나 기존 단어의 의미가 확장된 표현

이 범주에 들지 않는 표현은 NW에서 제외했다. 바뀐 기준에 맞춰 전체 데이터에는 **비례 다운샘플링(proportional downsampling)**을 적용했다.

#### Table 1. 필터링 전후 개체명 분포

| Entity | 분포 (%) | 원본 개수 | 필터링 후 개수 |
| --- | --- | --- | --- |
| OG | 37.8 | 38,143,982 | 3,137,400 |
| AF | 6.1 | 6,153,459 | 522,900 |
| CV | 45.8 | 46,215,902 | 3,801,400 |
| FD | 1.6 | 1,614,704 | 132,800 |
| TR | 1.0 | 1,009,190 | 83,000 |
| TM | 7.6 | 7,669,844 | 630,800 |
| **NW** | **0.1** | **100,919** | **8,331** |

- **OG와 CV가 전체의 80% 이상**을 차지한다. 뉴스 코퍼스가 사회문화, 정부, 기업, 대중문화 중심이기 때문이다.
- **NW(0.1%)와 TR(1.0%)은 매우 희소**하다. 따라서 저자원 개체를 불균형 데이터 속에서 정확히 탐지하는 견고한 모델이 필요하다.

### 3.2 모델 — KoELECTRA

**BERT의 한계**
- BERT [15]는 **MLM(Masked Language Modeling)**으로 사전학습한다. 마스킹된 일부 토큰만 학습에 쓰여 데이터 활용이 비효율적이다.
- 사전학습에는 [MASK] 토큰이 있지만 파인튜닝에는 없어서 **분포 불일치**가 생긴다 [8].

**ELECTRA의 RTD (Replaced Token Detection)**
- **Generator**와 **Discriminator** 두 Transformer로 구성된다.
- Generator가 무작위로 고른 토큰을 그럴듯한 다른 토큰으로 바꾼다.
- Discriminator는 각 토큰이 원본인지 교체된 것인지 이진 분류한다.
- [MASK] 토큰이 없어 자연스러운 문맥이 유지되고, **모든 토큰에서 손실을 계산**하므로 학습 효율이 높다. 사전학습과 다운스트림 과제 간 정합성도 좋아진다.

**KoELECTRA 채택 이유**
- ELECTRA의 한국어 최적화 버전이다 [7].
- 뉴스, 백과사전, 나무위키 등 **약 14GB의 한국어 코퍼스**로 사전학습되었다. 정형 문체 위주라 문법적 정확성과 문맥 이해에 강하다.
- 본 연구 데이터셋(뉴스 중심)과 **도메인 유사성이 높아** 백본 모델로 선정했다.

### 3.3 평가 지표
- **Precision**: 모델이 예측한 개체 중 실제로 맞은 비율. 오탐(FP)을 얼마나 억제하는지 보여준다.
- **Recall**: 실제 개체 중 모델이 찾아낸 비율. 미탐(FN)을 얼마나 억제하는지 보여준다.
- **F1-score**: Precision과 Recall의 조화평균으로, NER의 대표 지표다 [16].

---

## 4. Experimental Results

### 4.1 실험 환경

| 하이퍼파라미터 | 값 |
| --- | --- |
| Batch size | 32 |
| Max sequence length | 128 tokens |
| Learning rate | 2 × 10⁻⁵ |
| Max epochs | 100 |
| Dropout | 0.1 |
| Optimizer | AdamW |
| Early stopping | Validation loss 기준, patience 3 |
| 데이터 분할 | Train : Val : Test = 80 : 10 : 10 (층화 샘플링) |
| 토크나이저 | KoELECTRA WordPiece (subword 단위로 OOV·신조어 처리) |
| 태깅 방식 | BIO tagging |

#### 데이터 구성 (3가지)
- **Balanced**: 과대표된 개체를 **최대 100,000개**로 제한해 클래스 불균형을 완화
- **Original Distribution**: 필터링된 개체의 자연 분포를 그대로 유지
- **Uniform Small**: 모든 개체의 샘플 수를 **NW 개수(8,331)에 맞춰** 동일하게 설정해 공정 비교

#### Table 2. 데이터셋 구성

| Entity | Balanced | Original | Uniform |
| --- | --- | --- | --- |
| OG | 100,000 | 3,137,400 | 8,331 |
| AF | 100,000 | 522,900 | 8,331 |
| CV | 100,000 | 3,801,400 | 8,331 |
| FD | 100,000 | 132,800 | 8,331 |
| TR | 100,000 | 83,000 | 8,331 |
| TM | 100,000 | 630,800 | 8,331 |
| NW | 8,331 | 8,331 | 8,331 |
| **Total** | **608,331** | **8,316,631** | **58,317** |

### 4.2 결과

#### Table 3. 개체 유형별 F1-score

| Entity | Balanced F1 | Original F1 | Uniform F1 |
| --- | --- | --- | --- |
| OG | 0.88 | 0.91 | 0.77 |
| AF | 0.84 | 0.81 | 0.70 |
| CV | 0.90 | **0.92** | 0.82 |
| FD | **0.92** | 0.87 | 0.86 |
| TR | 0.85 | 0.76 | 0.81 |
| TM | 0.85 | 0.83 | 0.74 |
| **NW** | **0.91** | **0.89** | **0.92** |
| Micro Avg | 0.88 | **0.90** | 0.79 |
| Macro Avg | **0.88** | 0.86 | 0.80 |

#### 결과 분석

**① Original Distribution: 데이터가 많은 개체가 유리하다**
- 데이터가 풍부한 **CV(0.92)와 OG(0.91)**가 높은 성능을 보였다. 샘플이 많고 언어적으로 다양할수록 성능이 높다는 기존 NER 연구 결과와 같다.
- 반면 데이터가 적은 **TR은 0.76**에 그쳤다. 고빈도 개체의 높은 성능이 **인식 공정성(fairness)**을 희생한 결과임을 보여준다.

**② Balanced: 가장 안정적이고 균형 잡힌 성능**
- 개체당 최대 100,000개로 제한하자 고자원 개체와 저자원 개체 모두 비슷한 F1을 기록했다.
- **NW F1 0.91, Macro F1 0.88(최고)**. 어휘가 다양하고 문맥에 민감한 표현에도 잘 일반화된다.
- 클래스 불균형을 완화하면 모델의 전반적인 견고성이 높아진다. 개체 빈도가 심하게 치우친 실제 NER 환경에 시사점이 있다.

**③ Uniform Small: 데이터 양이 중요하다**
- 기존에 성능이 좋던 개체가 크게 떨어졌다. **OG 0.77, AF 0.70, TM 0.74**. 언어적으로 복잡하거나 형태가 다양한 개체일수록 데이터 양이 중요하다.
- **NW는 이 구성에서 최고치인 0.92**를 기록했다. 다만 이는 실제 일반화 성능이 좋아졌다기보다 **데이터 다양성이 줄어든 효과**일 가능성이 크다.
- 즉, 저자원 조건에서 얻은 성능 향상이 실제 환경의 견고성으로 바로 이어지지는 않는다. **다양성과 공정성을 함께 고려한 데이터셋 설계**가 필요하다.

**종합**
- 신조어처럼 변동성이 크고 수명이 짧으며 저빈도인 개체에서는 **데이터 구성이 성능을 좌우**한다.
- 제안 모델은 불균형 환경과 균형 환경 모두에 적응했으며, 언어 자원이 고르지 않은 실제 상황에서도 쓸 수 있음을 확인했다.
- 향후 데이터 증강, 준지도학습, 지속학습(continual learning)으로 저자원 개체 성능을 더 높일 수 있다.

---

## 5. Conclusion

- 사전·규칙 기반으로 탐지하기 어려웠던 **신조어를 효과적으로 식별하는 딥러닝 NER 모델**을 제안했다.
- 문맥 인식 학습 덕분에 어휘 다양성, 짧은 수명, 문맥 의존성을 가진 신조어도 견고하게 인식했다.
- 여러 개체 유형에서 안정적인 예측 성능을 보였다. 뉴스 같은 정형 도메인을 넘어 **일상 언어와 온라인 플랫폼**에도 적용할 가능성을 확인했다.
- 이 기술은 언어 변화를 제때 인식하는 기반이 되며, **세대 간 언어 격차 해소와 정보 접근의 형평성** 향상에 기여할 수 있다.

### 향후 연구
- 신조어가 가장 활발하고 예측 불가능하게 등장하는 **비공식 언어, 대화체, 구어체**로 확장
- **온라인 커뮤니티, SNS, 일상 대인 소통** 환경에 적용
- **도메인 적응(domain adaptation), 지속학습, 준지도학습** 도입으로 일반화 성능 향상

---

## 🔍 리뷰 노트 (내 생각 정리용)

- **강점**: 신조어를 단독 탐지 과제가 아니라 **다중 개체 NER 안의 한 클래스**로 다뤄 문맥 정보를 활용했다. 세 가지 데이터 구성으로 분포 영향을 체계적으로 분석했다.
- **한계**
  - 학습 데이터가 **뉴스 중심 정형 텍스트**여서 구어체나 SNS 신조어에 대한 일반화는 아직 검증되지 않았다.
  - NW 샘플이 **8,331개**로 적고, Uniform 구성의 NW 0.92는 데이터 다양성 감소 효과일 수 있다(논문에서도 언급).
  - BiLSTM-CRF, BERT, RoBERTa 등 **베이스라인 모델과의 비교가 논문에 포함되지 않았다**. 연구 기록에는 DeBERTa-V3와 RoBERTa 실험이 있으니, 후속 연구에서 비교표로 넣으면 좋다.
- **확장 아이디어 연결**: 아이디어 저장소의 제안 2(개체명 사전 기반 설명 제공)와 제안 4(공감어휘 변환)는 이 모델의 NW 탐지 결과를 입력으로 쓰는 **후속 응용**으로 바로 이어질 수 있다.

---

## 7. 연구 진행 과정 기록

### 주제 선정 — 아이디어 후보

| 제안 | 내용 | 비고 |
| --- | --- | --- |
| 1. 신조어 개체명 인식 모델 | AI Hub 말뭉치로 신조어를 인식하는 NER 모델 학습 | ✅ 본 논문 |
| 2. 개체명 사전을 이용한 번역 | 인식한 개체명에 기성세대용 설명을 함께 제공 | 후속 응용 |
| 3. 연속 대화 기반 세대 갈등 예측 | 대화 흐름의 감정·언어 패턴을 시계열 분석해 갈등 예측 | — |
| 4. 세대 간 공감어휘 추천 | NER로 핵심어를 추출해 상대 세대가 공감할 표현으로 변환 | 후속 응용 |
| 5. NER 기반 블로그 요약 | 블로그·리뷰에서 유의미한 정보를 자동 정리 | — |

### 관련 연구 조사
- [현대 신조어와 그것이 사회에 미치는 영향](https://app.notion.com/p/1f9684ef7804801283c9f85deae76682?pvs=21) → 논문 참고문헌 [3]
- [딥러닝 기반 신조어 이해를 통한 감성 분류 정확도 향상 방법론(표적 마스킹)](https://app.notion.com/p/1f9684ef780480098773c9e9e0fdd8ba?pvs=21)
- [형태소 정보도 입력 데이터로 전달해본 논문](https://app.notion.com/p/202684ef78048006b7e5e96a0dc58e8b?pvs=21)
- [신조어가 포함된 프롬프트에 대형 언어 모델의 대응을 평가](https://app.notion.com/p/202684ef780480cebac8c0685ce1e78e?pvs=21)
- [영어 신조어의 발생 원천에 관한 논문](https://app.notion.com/p/215684ef780480c38600edefa4300363?pvs=21) → 논문 참고문헌 [14] (신조어 3범주 정의 근거)
- [통계 언어 모델 기반의 중국어 신조어 탐지 방법 제안 논문](https://app.notion.com/p/215684ef780480358462da390089747c?pvs=21)

### 실험 기록
- 전처리 실험: Mecab 형태소 분석, [음절 토큰화](https://app.notion.com/p/202684ef780480d796e8f25e099fd1ca?pvs=21)
- 모델 비교(데이터 비율별 학습): [ELECTRA](https://app.notion.com/p/ELECTRA-201684ef780480df9571ea27ee00318d?pvs=21) · [DeBERTa-V3](https://app.notion.com/p/DeBERTa-V3-202684ef7804807f8423e841927c7955?pvs=21) · [RoBERTa](https://app.notion.com/p/ROBERTa-202684ef780480b5a7cac830b4fbe6dc?pvs=21)
- [ELECTRA 제로샷](https://app.notion.com/p/ELECTRA-204684ef780480e69eb2dbda0d6d1c5e?pvs=21)
- [데이터셋의 7개 태그 모두 인식하는 모델 학습](https://app.notion.com/p/7-209684ef780480b990cfcd7650a27ae6?pvs=21) → 최종 논문 실험

### 기타 데이터셋 검토
- [네이버 NLP 데이터셋](https://app.notion.com/p/NLP-1f1684ef780480268e50fcf80cb484a9?pvs=21)
- [국립국어원 개체명 말뭉치](https://app.notion.com/p/1f1684ef780480af89cef1206d1a3cf1?pvs=21)

### 회의록
- [5월 14일 강영명 교수님과 상담](https://app.notion.com/p/5-14-1f4684ef7804800f8c3cc7ea28d74f22?pvs=21)
- [5월 15일 논문 일정 및 진행 계획](https://app.notion.com/p/5-15-1f4684ef780480c78abad66c12e3e922?pvs=21)
- [5월 20일 정기미팅](https://app.notion.com/p/5-20-1f9684ef780480069517f694c5e26107?pvs=21)
- [5월 27일 정기미팅](https://app.notion.com/p/5-27-200684ef78048021b64aef46c60c3cb7?pvs=21)
- [6월 23일 정기미팅](https://app.notion.com/p/6-23-21b684ef780480d1a218c09f4bb27322?pvs=21)

### 일정

| 단계 | 날짜 |
| --- | --- |
| 논문 제출 마감 | 2025.07.14 (월) |
| 저자 통보 | 2025.07.28 (월) |
| 최종 원고·등록 마감 | 2025.08.04 (월) |
| 학회 개최 | 2025.08.25 (월) ~ 08.27 (수) |

---

## 참고문헌

[1] Korea Research, "2025 Generation Perception and Intergenerational Conflict Survey: Weekly Report No. 322-1," HR Consulting Opinion, Mar. 26, 2025. https://hrcopinion.co.kr/en/archives/32460

[2] Z.R. Sultanova, "Neologisms in the Modern World: Language That Changes Reality," *Pedagogik Tadqiqotlar Jurnali*, vol. 3, no. 1, pp. 188–193, 2025. http://www.wosjournals.com/index.php/ptj/article/view/991

[3] I. Aziza and Z. Aktamova, "Modern Neologisms and Their Impact on Society," *Int. J. Multidisciplinary Research and Growth Evaluation*, vol. 6, no. 2, pp. 491–494, 2025. https://interoncof.com/index.php/USA/article/view/9830

[4] S.T. Vacalares et al., "The Intelligibility of Internet Slangs Between Millennials and Gen Zers: A Comparative Study," *Int. J. Science and Research Archive*, vol. 9, no. 1, pp. 400–409, 2023. doi: 10.30574/ijsra.2023.9.1.0456

[5] K.I. Nam, "New Words of 2019 Investigation," National Institute of Korean Language, Dec. 2019. https://www.korean.go.kr/front/reportData/reportDataView.do?mn_id=207&report_seq=1104

[6] National Information Society Agency, "AI Hub: Large Web Data-Based Korean Corpus Dataset," AI Hub, 2022. https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=624

[7] J. Park, "KoELECTRA: Pretrained ELECTRA Model for Korean," GitHub, 2020. https://github.com/monologg/KoELECTRA

[8] K. Clark, M.-T. Luong, Q.V. Le, and C.D. Manning, "ELECTRA: Pre-Training Text Encoders as Discriminators Rather Than Generators," arXiv:2003.10555, 2020.

[9] J.H. Kim, H.C. Kim, and Y.S. Choi, "Feature Generation of Dictionary for Named-Entity Recognition Based on Machine Learning," *Journal of Information Management*, vol. 41, no. 2, pp. 31–46, 2010. doi: 10.1633/JIM.2010.41.2.031

[10] V. Yadav and S. Bethard, "A Survey on Recent Advances in Named Entity Recognition From Deep Learning Models," arXiv:1910.11470, 2019.

[11] J. Li, A. Sun, J. Han, and C. Li, "A Survey on Deep Learning for Named Entity Recognition," *IEEE TKDE*, vol. 34, no. 1, pp. 50–70, 2022. doi: 10.1109/TKDE.2020.2981314

[12] Z. Huang, W. Xu, and K. Yu, "Bidirectional LSTM-CRF Models for Sequence Tagging," arXiv:1508.01991, 2015.

[13] M. Munnangi, "A Brief History of Named Entity Recognition," arXiv:2411.05057, 2024.

[14] Y. Li, "Sources of English Neologisms," *Linguistics and Education*, vol. 42, no. 1, pp. 210–215, 2024. doi: 10.54254/2753-7048/42/20240854

[15] J. Devlin, M. Chang, K. Lee, and K. Toutanova, "BERT: Pre-Training of Deep Bidirectional Transformers for Language Understanding," arXiv:1810.04805, 2018.

[16] A. Mansouri, L.S. Affendey, and A. Mamat, "Named Entity Recognition Approaches," *IJCSNS*, vol. 8, no. 2, pp. 339–344, 2008.
