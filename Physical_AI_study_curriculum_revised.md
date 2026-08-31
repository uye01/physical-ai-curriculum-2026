# Physical AI & World Models 스터디 커리큘럼

> 개정 기준일: 2026-08-31  
> 운영 원칙: **주차가 아니라 Chapter와 학습 질문을 기준으로 진행한다.** 휴회, 발표자 수, 논문 발표 속도에 따라 한 Chapter의 세션 수와 진행 기간을 자유롭게 조정한다.  
> 이번 개정: (1) 온보딩을 1세션으로 압축, (2) 논문 없는 토론 전용 세션 제거, (3) 평가 Chapter를 요약형 2세션으로 축소, (4) 종합·연구 제안 Chapter 제거, (5) **core 계보가 아니면 2025-2026년 논문을 우선**하는 최신성 원칙 적용(locomotion 세션 전면 교체 포함), (6) social world model 세션(Ch.13) 1회 추가. 세션당 논문 2~3편 원칙은 유지한다.  
> **본 문서는 연구실 스케일 개정안이다:** 빅테크가 아닌 대학 연구실 규모로 수행 가능한 **2026년 논문**만으로 구성한 Extension 세션 3개 — C05-S04(post-training), C06-S07(데이터 엔진), C14-S03(평가·분석) — 를 분산 추가했다. 목적은 연구실 스케일에서 어떤 연구가 충분히 가능한지를 보여주는 것이다. 총 51세션.

---

## 0. 스터디 개요

### 0.1 무엇을 다루는가

**주제** — 로봇이 세계를 어떻게 관측하고, 표현하고, 예측하며, 행동으로 옮기는가. Vision-Language-Action Model(VLA), 정책 학습, world model, predictive representation, reasoning, 실시간 제어가 만나는 지점을 문제 중심으로 따라간다.

이 스터디는 Physical AI 전 분야를 빠짐없이 훑는 백과사전식 과정이 아니다. 다음 질문에 답할 수 있는 연구자를 만드는 것이 목적이다.

1. **처음 보는 VLA·world model 논문의 좌표를 짚을 수 있는가?**
   - 입력과 출력은 무엇인가
   - action을 어떤 형태로 표현하는가
   - 어느 계보의 어떤 한계를 공격하는가

2. **성능 숫자를 해석하는 데 필요한 실험 조건을 먼저 확인하는가?**
   - 하드웨어와 센서
   - task 정의와 성공 조건
   - 데이터 출처와 수집 비용
   - 제어 주파수와 추론 지연

3. **각 접근이 암묵적으로 가정한 전제를 찾을 수 있는가?**
   - VLM의 semantic prior가 제어로 전이되는가
   - 규모가 구조를 대체할 수 있는가
   - 합성 데이터가 실물 데이터를 대체할 수 있는가
   - 픽셀 생성이 필요한가, latent 예측이면 충분한가
   - 2D 스케일링만으로 기하와 물리가 학습되는가

4. **자기 연구실의 자원으로 검증 가능한 연구 공백을 찾을 수 있는가?**
   - 빅테크 규모의 데이터·컴퓨트가 필요한 문제
   - 공개 모델, 소형 로봇, 시뮬레이터, 기존 데이터로 접근 가능한 문제

SOTA 순위는 빠르게 바뀌지만, **어떤 실패 때문에 다음 방법이 등장했는가**라는 인과 사슬은 오래 남는다. 따라서 실험표 전체를 요약하기보다 설계 근거, 반증 조건, 시스템 제약을 중심으로 읽는다.

---

### 0.2 이번 개정의 핵심 원칙

#### 1. 깊이보다 흐름: 한 세션에 논문 2~3편

이 스터디의 우선 목표는 당장 연구를 시작하는 것이 아니라 **분야를 이해하고 흥미를 얻는 것**이다. 논문 한 편을 실험 세부까지 파고들기보다, 같은 문제를 공유하는 논문 2~3편을 1시간 세션 하나로 묶어 연구 흐름과 인사이트를 얻는다.

- RT-1과 RT-2, ACT와 Mobile ALOHA, Diffusion Policy와 Flow Matching, π 시리즈 2편씩, Cosmos-Predict2.5와 Cosmos 3처럼 **계보 단위로 묶는다.**
- 밀도를 높인 만큼 커리큘럼에 더 많은 주제를 포함한다(FAST tokenizer, locomotion·whole-body control, 합성 데이터 엔진, 시뮬레이션 벤치마크 등).
- **최신 우선:** 문제의 기원을 이해하는 데 필요한 core 계보가 아니면 2025-2026년 논문을 우선 선택한다. 기준은 '2026년 8월 시점에서 다음에 어떤 연구를 할 수 있는가'에 대한 인사이트다. 오래된 원리 논문은 발췌·배경 노트로만 다룬다.
- 여러 논문 세션은 논문별 요약의 나열이 아니라 **하나의 비교 질문**으로 통합한다.
- 시스템 명세(0.4)는 세션의 대표(anchor) 논문 1편에 대해서만 완전하게 작성하고, 나머지 논문은 anchor와 달라진 항목만 명시한다.

#### 2. 문제를 먼저 보고, 도구는 필요할 때 배운다

초반부터 diffusion, flow matching, DiT의 세부 개념을 연속해서 배우지 않는다. 먼저 RT 계보를 통해 VLA가 무엇을 하려는지와 discrete action token의 한계를 확인한다. 이후 continuous action이 필요한 이유가 생겼을 때 Diffusion Policy와 Flow Matching을 학습한다.

#### 3. 하드웨어는 제품 목록으로 선행 학습하지 않는다

첫 세션에서는 Physical AI의 연구 지형과 최소 로봇 리터러시만 다룬다. ALOHA, Franka, Unitree, GelSight, Jetson 등은 실제 논문에 등장하는 Chapter에서 필요한 만큼 학습한다.

#### 4. 단일 서베이를 정답 지도처럼 사용하지 않는다

종합 Embodied AI 서베이, VLA 전용 서베이, world model 전용 서베이를 서로 다른 역할로 사용한다. 공식 프로젝트 페이지, 코드 저장소, 기술 블로그는 최신 릴리스를 갱신하는 보조 자료로 사용하되 taxonomy의 근거로 단독 사용하지 않는다.

#### 5. 고전 원전은 정독보다 발췌와 계보 정렬에 사용한다

2018년 *World Models*와 2022년 LeCun position paper는 독립 정독 세션으로 두지 않는다. 전자는 world model Chapter에서 imagination·rollout 계보의 출발점으로, 후자는 JEPA Chapter에서 predictive architecture의 문제의식으로 발췌한다.

#### 6. 회사가 아니라 문제와 방법론으로 Chapter를 나눈다

Google DeepMind, NVIDIA, Physical Intelligence, Meta 등은 비교 태그로 유지한다. 같은 회사의 논문이라도 서로 다른 문제를 다루면 다른 Chapter에 배치한다.

#### 7. Core / Extension / Update를 구분한다

- **Core** — 계보를 이해하기 위해 반드시 다룰 세션
- **Extension** — 연구실 관심, 발표 인원, 진행 속도에 따라 선택
- **Update** — 발표 1주 전 새 논문·모델·공식 릴리스로 교체하거나 보강하는 슬롯

세션 수는 고정하지 않지만, 새 논문이 나왔다는 이유만으로 Core를 계속 늘리지 않는다. **새로운 문제 정의, 아키텍처, 데이터 방식, 평가 방식이 추가된 경우에만 Core 승격**을 검토한다.

---

### 0.3 운영 방식

- 기본 세션은 1시간으로, 발표 40분 + 토론 15~20분을 권장한다.
- 한 세션은 논문 2~3편을 하나의 비교 질문으로 다루는 것을 기본으로 한다. 논문 1편 세션은 실습·토론 세션 등 예외로만 둔다.
- 하루에 두 세션을 운영할 수 있지만, Chapter 경계와 날짜 경계를 일치시킬 필요는 없다.
- 커리큘럼 문서와 실제 일정표를 분리한다.
  - 본 문서: 학습 순서, 세션 목표, 논문, 산출물
  - 별도 운영표: 날짜, 발표자, 휴회, 세션 병합·분할
- 세션 번호는 Chapter 종속 형식으로 관리한다.
  - 예: `C02-S03`, `C08-S04`
- 여러 논문을 맡은 세션은 논문별 요약이 아니라 하나의 비교 질문으로 통합한다. 논문당 시간을 균등 배분하지 말고, anchor 논문으로 개념을 세운 뒤 나머지는 차이점 중심으로 다룬다.

---

### 0.4 발제의 기본 질문

모든 발제는 가능한 한 다음 세 질문에 답한다.

1. **이전 접근은 무엇에서 막혔는가?**
2. **그래서 이 연구는 무엇을 바꿨는가?**
3. **그 변경이 새로 만든 한계는 무엇인가?**

추가로 반드시 명시할 항목:

- 로봇과 센서
- observation과 action space
- task와 성공 조건
- 데이터의 출처와 규모
- action chunk 길이
- 제어 주파수와 추론 지연
- 공개 가중치·데이터·코드 여부

여러 논문을 다루는 세션에서는 위 항목을 **anchor 논문에 대해서만 완전하게** 작성하고, 나머지 논문은 anchor와 달라진 항목만 명시한다.

산업계 테크리포트는 주장과 데모가 강하고 검증 서술이 약할 수 있다. 다음을 구분한다.

- 실제 실험으로 보인 것
- 저자가 해석한 것
- 마케팅 또는 전망에 가까운 것

---

### 0.5 연구그룹 관점 태그

Chapter를 회사별로 나누지는 않지만, 각 연구를 아래 관점 태그로 표시한다.

| 태그 | 핵심 주장 | 반증 조건 |
| --- | --- | --- |
| **Open/Academic** | 구조와 표현 설계가 데이터 규모를 절약한다 | 충분한 스케일에서 구조 차이가 사라짐 |
| **Google DeepMind** | VLM의 semantic prior를 물리세계 제어로 전이할 수 있다 | dynamics와 contact가 semantic prior로 해결되지 않음 |
| **NVIDIA** | 합성 데이터와 시뮬레이션 인프라가 실물 데이터 부족을 완화한다 | synthetic-to-real gap이 성능 상한을 고정함 |
| **Physical Intelligence** | 대규모 로봇 데이터와 end-to-end 학습이 일반화를 만든다 | 수집 비용이 발산하거나 long-tail을 덮지 못함 |
| **Meta / JEPA** | 픽셀 생성보다 latent prediction이 효율적인 world representation을 만든다 | latent가 planning에 필요한 접촉·기하 정보를 잃음 |
| **Geometry-first** | 명시적 3D·4D 구조가 일반화와 데이터 효율의 핵심이다 | 대규모 2D 비디오 학습이 기하를 암묵적으로 습득함 |

---

## 1. 전체 Chapter 지도

| Chapter | 핵심 질문 | 주요 산출물 |
| --- | --- | --- |
| **Ch.1 온보딩과 좌표계** | Physical AI는 무엇이며 어떤 용어를 구분해야 하는가 | 개념 지도·용어집 |
| **Ch.2 VLA 초기 계보** | 언어모델식 action tokenization과 action chunk는 무엇을 가능하게 하고 무엇을 잃는가 | 1차 VLA anatomy 표 |
| **Ch.3 Action 생성 도구** | 왜 로봇 정책이 diffusion, flow, DiT, tokenizer를 사용하는가 | action 표현 비교표 |
| **Ch.4 Continuous-action VLA** | VLM과 continuous action module을 어떻게 연결하는가 | VLA 3갈래 분류표 |
| **Ch.5 π 계보** | 손재주, open-world generalization, 경험 학습, 실시간 실행은 어떻게 이어지는가 | π 버전별 한계 시인 사슬 |
| **Ch.6 데이터·스케일·embodiment** | 일반화는 모델보다 데이터 엔진과 embodiment 정렬의 문제인가 | 데이터 엔진 비교표 |
| **Ch.7 Predictive representation과 기하** | 픽셀, latent, 3D 중 무엇을 예측해야 하는가 | 표현 선택·반증 조건 표 |
| **Ch.8 World model을 도구로 쓰기** | world model은 시뮬레이터, 데이터 생성기, 정책 평가기 중 무엇인가 | world model 기능 분해표 |
| **Ch.9 행동 전에 생각하기** | 추론은 언어·이미지·action 중 어디에서 수행해야 하는가 | 추론 매체 × 지연 × 해석 가능성 표 |
| **Ch.10 실시간성과 dual system** | 큰 모델의 느린 추론과 빠른 제어를 어떻게 분리하는가 | 모델 크기 × 지연 × 제어 주파수 표 |
| **Ch.11 Locomotion과 whole-body control** | 다리·전신 제어는 manipulation과 어떻게 다른 문제인가 | manipulation vs locomotion 문제 설정 비교표 |
| **Ch.12 촉각과 힘** | 시각 중심 VLA에 고주파·희소 접촉 신호를 어떻게 넣는가 | 촉각·힘 융합 위치 비교표 |
| **Ch.13 Social world model** | 사람과 에이전트의 mental state를 world model의 상태로 어떻게 다루는가 | physical vs social world model 비교 메모 |
| **Ch.14 평가와 물리 일관성** | 그럴듯한 생성과 일관된 world model을 어떻게 구분하는가 | 평가 실패 모드 표 |

---

### 1.1 전체 세션 리스트 (한눈에 보기)

> 총 51세션. 세부 내용은 2장의 각 세션 항목을 본다. `◆` 표시는 이 개정안에서 추가된 연구실 스케일 세션(2026년 논문)이다.

| 세션 | 주제 · 논문 | 구분 |
| --- | --- | --- |
| **Ch.1 온보딩과 좌표계** | | |
| C01-S01 | Physical AI 온보딩 — 연구 지형, 용어, 논문 읽는 법 | Core |
| **Ch.2 VLA 초기 계보** | | |
| C02-S01 | RT-1 + RT-2 — action token의 탄생과 웹 지식 전이 | Core |
| C02-S02 | Open X-Embodiment + OpenVLA — 데이터와 가중치의 공개 | Core |
| C02-S03 | ACT + Mobile ALOHA — action chunking의 초기 계보 | Core |
| **Ch.3 Action 생성 도구** | | |
| C03-S01 | Diffusion Policy + Flow Matching | Core |
| C03-S02 | DiT + FAST — 아키텍처와 tokenizer | Core |
| C03-S03 | 3D Diffusion Policy + iDP3 | Core |
| **Ch.4 Continuous-action VLA** | | |
| C04-S01 | CogACT + Octo — diffusion head의 두 가지 동기 | Core |
| C04-S02 | RDT-1B + Diffusion-VLA — DiT 스케일업과 reasoning 조건화 | Core |
| C04-S03 | 경량·공개 VLA: OpenVLA-OFT + SmolVLA | Extension / Update |
| **Ch.5 π 계보** | | |
| C05-S01 | π0 + π0.5 — flow expert와 open-world 일반화 | Core |
| C05-S02 | π*0.6 / RECAP + Real-Time Chunking — 경험 학습과 실시간 실행 | Core |
| C05-S03 | Hi Robot + Knowledge Insulation — 계층과 학습 레시피 | Extension |
| C05-S04 ◆ | 연구실 스케일 post-training — Continual RL Fine-Tuning + CapVector | Extension |
| **Ch.6 데이터·스케일·Embodiment** | | |
| C06-S01 | Gemini Robotics 1.0 + 1.5 | Core |
| C06-S02 | GR00T N1 + N1.5 | Core |
| C06-S03 | LAPA + DreamGen — 라벨 없는 비디오와 생성 비디오 | Core |
| C06-S04 | MimicGen + DexMimicGen — 시연의 프로그램적 증식 | Extension |
| C06-S05 | VLA 데이터셋·벤치마크·데이터 엔진 | Extension |
| C06-S06 | Cross-embodiment action representation | Extension / Update |
| C06-S07 ◆ | 연구실 데이터 엔진 2026 — RealDexUMI + YOR | Extension |
| **Ch.7 Predictive Representation과 기하** | | |
| C07-S01 | LeCun 발췌 + I-JEPA | Core |
| C07-S02 | V-JEPA 2 + V-JEPA 2.1 | Core |
| C07-S03 | DUSt3R + VGGT — geometry foundation model | Core |
| C07-S04 | 3D-VLA + 4D-VLA | Core |
| **Ch.8 World Model을 도구로 쓰기** | | |
| C08-S01 | World Models 발췌 + Dreamer 4 — imagination 계보 | Core |
| C08-S02 | Genie + Genie 3 — latent action에서 실시간 세계로 | Core |
| C08-S03 | Cosmos 플랫폼 + Cosmos-Transfer | Core |
| C08-S04 | Cosmos-Predict2.5 + Cosmos 3 — 통합 world model 백본 | Core / Update |
| C08-S05 | Ctrl-World + WorldVLA — 분리형과 통합형 | Core |
| C08-S06 | Data engine & policy evaluator — DreamGen 재방문 + Veo | Extension |
| C08-S07 | World-Gymnast와 RL inside world models | Extension / Update |
| **Ch.9 행동 전에 생각하기** | | |
| C09-S01 | Cosmos-Reason + Alpamayo-R1 — ontology와 인과 라벨 | Core |
| C09-S02 | ECoT + CoT-VLA — 언어로/이미지로 생각하기 | Core |
| C09-S03 | ACoT + DualCoT — action-space reasoning과 병렬화 | Core |
| C09-S04 | Reasoning-VLA 최신 업데이트 | Extension / Update |
| **Ch.10 실시간성과 Dual System** | | |
| C10-S01 | Fast-in-Slow + Hume | Core |
| C10-S02 | Reactive Diffusion Policy + TacMamba — 다중 주파수 제어 | Core |
| C10-S03 | Think Twice, Act Once + DeeR-VLA — adaptive inference | Core |
| C10-S04 | Onboard deployment budget | Core / 실습 |
| **Ch.11 Locomotion과 Whole-Body Control** | | |
| C11-S01 | Locomotion RL의 현재 — Perceptive Terrain Locomotion + BeyondMimic | Core (챕터 내) |
| C11-S02 | Humanoid whole-body control — HOVER + ASAP | Core (챕터 내) |
| C11-S03 | 전신 teleop 데이터 수집 — TWIST + TWIST2 | Extension / Update |
| **Ch.12 촉각과 힘** | | |
| C12-S01 | VTLA + ForceVLA — tactile image와 force/torque | Core |
| C12-S02 | Tactile-VLA + TacVLA/TAP-VLA — grounding과 융합 위치 | Core |
| C12-S03 | RDP 재방문 + Tactile-WAM — 시간 특성과 tactile pollution | Extension |
| C12-S04 | 촉각·힘 최신 논문 업데이트 | Update |
| **Ch.13 Social World Model** | | |
| C13-S01 | Social World Models + Building Social World Models with LLMs — 사회적 상태의 world model | Extension |
| **Ch.14 평가와 물리 일관성** | | |
| C14-S01 | 평가 지형 요약 — 벤치마크·평가 방식·함정 한눈에 보기 | Core |
| C14-S02 | PhyGDPO + PhysMaster — 물리성 선호 학습 | Core |
| C14-S03 ◆ | 연구실 스케일 평가·분석 — VLA-REPLICA + SO-101 벤치마크 | Extension |

---

## 2. Chapter별 세부 커리큘럼

### Ch.1 온보딩과 좌표계

> 목표: 제품명과 논문 목록을 외우기 전에 Physical AI의 전체 질문, 핵심 용어, 스터디 진행 방식을 이해한다.

---

#### C01-S01 · Physical AI 온보딩: 연구 지형, 용어, 논문 읽는 법

- **구분:** Core
- **핵심 질문:** Physical AI는 기존 vision-language AI와 무엇이 다르며, 이 분야의 논문과 테크리포트를 어떤 기준으로 읽어야 하는가?
- **다루는 것:**
  - perception → representation → prediction → planning → action의 연결과 closed loop
  - VLA, policy, world model, simulator, predictive representation의 역할 구분
  - 실수 비용, 안전, 데이터 수집 비용, embodiment 차이, 실시간 제어라는 제약
  - 논문 읽는 기준: method / data / system / product claim 구분, claim-evidence 구분, task 정의가 다른 성공률을 직접 비교하지 않기
  - (마지막 5~10분) 하드웨어 지형 훑기: 대표 휴머노이드·매니퓰레이터·센서를 '이런 제품들이 있다' 수준으로만
- **최소 로봇 리터러시:**
  - observation / state / action
  - joint space / end-effector space
  - open-loop / closed-loop
  - single-step action / action chunk
  - teleoperation / demonstration / rollout
  - control frequency / inference latency
- **참고 서베이(정독하지 않음):** Embodied AI 종합 서베이, VLA 서베이, world model 서베이(부록 A.1)는 학기 중 좌표 확인용으로만 반복 참조한다.
- **범위:** 제품 사양·센서 스펙 조사는 하지 않는다. 개별 하드웨어는 해당 논문이 등장하는 Chapter에서 필요한 만큼 다룬다.
- **산출물:** Physical AI problem map 초안, 공통 발제 템플릿 확정, 용어집 v1

---

### Ch.2 VLA 초기 계보: discrete token과 action chunk

> 목표: 생성모델 이론에 들어가기 전에 VLA의 입력·출력·학습 방식, discrete action token과 action chunk의 장단점을 초기 논문 계보로 체감한다.

---

#### C02-S01 · RT-1 + RT-2: action token의 탄생과 웹 지식 전이

- **구분:** Core
- **핵심 질문:** 로봇 action을 언어처럼 token으로 예측하면 무엇이 좋아지고, 웹 규모 VLM의 지식은 어디까지 제어로 전이되는가?
- **논문:**
  - Brohan et al. (2022), *RT-1: Robotics Transformer for Real-World Control at Scale* — anchor
  - Brohan et al. (2023), *RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control*
- **다루는 것:**
  - action discretization과 autoregressive prediction
  - 13만 episode 규모 데이터 엔진과 task·object 조합 일반화 (RT-1)
  - VLM co-fine-tuning, web knowledge transfer, emergent capability 평가 (RT-2)
  - 두 논문 사이에서 유지된 것(action 표현)과 바뀐 것(백본, 데이터, 주장)
- **반드시 확인:** 로봇, 제어 주파수, action dimension, tokenization 방식
- **토론:**
  - 분포를 token probability로 표현하는 것이 continuous multimodality의 해결인가, 우회인가?
  - generalization claim과 실제 평가 설계 사이의 간격
- **산출물:** 산업계 테크리포트 claim-evidence 표 첫 적용

---

#### C02-S02 · Open X-Embodiment + OpenVLA: 데이터와 가중치의 공개

- **구분:** Core
- **핵심 질문:** cross-embodiment 데이터셋과 공개 학습 파이프라인은 왜 그 자체로 연구 기여인가?
- **논문:**
  - Open X-Embodiment Collaboration (2023), *Open X-Embodiment: Robotic Learning Datasets and RT-X Models*
  - Kim et al. (2024), *OpenVLA: An Open-Source Vision-Language-Action Model* — anchor
- **다루는 것:**
  - 서로 다른 로봇의 데이터를 하나의 학습으로 합칠 때의 action space·카메라·주파수 정렬 문제
  - positive transfer는 언제 생기는가 (RT-X 실험)
  - RT 계보의 공개 구현, Open X-Embodiment 위에서의 학습과 fine-tuning
  - discrete action token의 정밀도·주파수·deployment 비용
- **개인 연구자 관점:** 우리 연구실이 실제로 fine-tuning, probing, adapter 연구를 시작할 수 있는 지점은 어디인가?
- **산출물:** 공개성, 하드웨어 요구량, 수정 가능 지점을 포함한 재현 가능성 카드

---

#### C02-S03 · ACT + Mobile ALOHA: action chunking의 초기 계보

- **구분:** Core
- **핵심 질문:** 한 스텝씩 action을 예측하면 왜 실패하며, chunk는 무엇을 해결하고 무엇을 잃는가?
- **논문:**
  - Zhao et al. (2023), *Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware* (ACT / ALOHA) — anchor
  - Fu et al. (2024), *Mobile ALOHA: Learning Bimanual Mobile Manipulation with Low-Cost Whole-Body Teleoperation*
- **다루는 것:**
  - compounding error
  - action chunking과 temporal aggregation
  - chunk가 길수록 반응성이 떨어지는 trade-off
  - 저가 bimanual teleoperation rig의 연구적 의미
  - 베이스 이동을 포함한 whole-body action과 static ALOHA 데이터 co-training 효과 (Mobile ALOHA)
- **하드웨어 학습:** ALOHA, ViperX/WidowX를 이 세션에서 필요한 만큼 다룬다.
- **연결:** 이후 Diffusion Policy, π0, GR00T, Reactive Diffusion Policy의 공통 action 단위. 전신 teleop 데이터 수집은 Ch.11에서 humanoid로 확장된다.
- **산출물:** `single-step / chunk / token` 1차 비교표

---

#### Ch.2 마감 산출물 · VLA anatomy 표 v1

| 축 | 확인할 질문 |
| --- | --- |
| 입력 | 이미지, 언어, proprioception을 어떻게 넣는가 |
| 출력 | discrete token인가 continuous vector인가 |
| 시간 | single step인가 action chunk인가 |
| 학습 데이터 | 단일 로봇, 다중 로봇, 웹 데이터 중 무엇인가 |
| 제어 | 몇 Hz이며 closed-loop로 얼마나 자주 재계획하는가 |
| 공개성 | 코드·가중치·데이터 중 무엇이 공개되는가 |

---

### Ch.3 Action 생성 도구: 필요한 순간에 배우기

> 목표: Diffusion, Flow Matching, DiT, action tokenizer를 독립 생성모델 강의로 다루지 않고, 로봇 action의 multimodality·정밀도·속도·스케일링 문제에 대한 도구로 이해한다.

---

#### C03-S01 · Diffusion Policy + Flow Matching

- **구분:** Core
- **핵심 질문:** 같은 관측에서 여러 action이 모두 정답일 때 MSE 회귀는 왜 실패하며, iterative sampling의 비용은 어떻게 줄이는가?
- **논문:**
  - Chi et al. (2023), *Diffusion Policy: Visuomotor Policy Learning via Action Diffusion* — anchor
  - Lipman et al. (2023), *Flow Matching for Generative Modeling*
- **다루는 것:**
  - 로봇 action의 multimodality
  - observation-conditioned denoising, receding-horizon action execution
  - action chunk와 closed-loop replanning의 관계
  - diffusion과 flow의 관계, vector field와 ODE sampling 직관
  - 적은 sampling step이 제어 주파수에 왜 중요한가
- **기초 범위:** DDPM의 forward/reverse 직관, noise prediction까지만. ELBO 유도와 optimal transport 상세 이론은 생략한다.
- **핵심 장면:** 왼쪽과 오른쪽 회피가 모두 가능한데 평균 action이 장애물 정면을 향하는 사례를 구체화한다.
- **산출물 시작:** 추론 step ↔ latency ↔ control frequency 표
- **연결:** π0의 flow-matching action expert

---

#### C03-S02 · DiT + FAST: 아키텍처와 tokenizer라는 별개의 층위

- **구분:** Core
- **핵심 질문:** 학습 목표(diffusion/flow)와 별개로, 아키텍처와 action tokenizer는 각각 무엇을 결정하는가?
- **논문:**
  - Peebles & Xie (2023), *Scalable Diffusion Models with Transformers* (DiT) — anchor
  - Pertsch et al. (2025), *FAST: Efficient Action Tokenization for Vision-Language-Action Models*
- **다루는 것:**
  - U-Net 대신 Transformer를 사용하는 이유, conditioning 주입 방식, 모델 스케일링 (DiT)
  - naive binning token이 고주파·정밀 action에서 무너지는 이유
  - DCT 기반 압축 action tokenization과 autoregressive VLA 학습 효율 (FAST)
- **범위:** 이미지 생성 FID 순위가 아니라 아키텍처와 표현 선택을 본다.
- **연결:** RDT-1B·GR00T의 DiT 계열 action module, π0-FAST, Ch.2 discrete token 한계의 재평가
- **토론:** tokenizer가 충분히 좋아지면 discrete vs continuous 논쟁의 결론이 바뀌는가?

---

#### C03-S03 · 3D Diffusion Policy + iDP3

- **구분:** Core
- **핵심 질문:** action model을 그대로 두고 입력 표현만 2D에서 3D로 바꾸면 왜 데이터 효율이 달라지며, humanoid·egocentric·onboard 환경으로 옮기면 무엇이 새로 필요한가?
- **논문:**
  - Ze et al. (2024), *3D Diffusion Policy* — anchor
  - Ze et al. (2024), *Generalizable Humanoid Manipulation with 3D Diffusion Policies* (iDP3)
- **다루는 것:**
  - point cloud representation, viewpoint invariance, demonstration efficiency
  - egocentric observation, noisy human data, onboard compute, scene generalization
- **비교축:** Diffusion Policy와 입력 표현 외 요소를 최대한 맞춰 비교
- **반증 조건:** depth sensor와 calibration 비용까지 포함해도 3D 이득이 유지되는가?
- **하드웨어 학습:** 사용한 humanoid와 compute budget을 논문 맥락에서 조사한다.
- **연결:** Ch.7 geometry foundation model, Ch.10 실시간성 예산
- **Update 지침:** 3D 입력 표현의 2025-26 후속 정책 연구가 있으면 발표 시 비교 슬롯으로 추가한다.

---

#### Ch.3 마감 산출물 · Action 표현 비교표

| 축 | 선택지 |
| --- | --- |
| action distribution | regression / token probability / diffusion / flow |
| temporal unit | single step / fixed chunk / adaptive chunk |
| input representation | 2D / depth / point cloud / latent |
| sampling | autoregressive token / compressed token (FAST) / iterative denoise / ODE step |
| 주요 비용 | quantization / sampling latency / sensor dependence / compute |

---

### Ch.4 Continuous-action VLA 아키텍처

> 목표: VLM의 semantic representation과 continuous robot action 사이를 연결하는 구조적 선택을 비교한다.

---

#### C04-S01 · CogACT + Octo: diffusion head를 붙이는 두 가지 동기

- **구분:** Core
- **핵심 질문:** VLM과 continuous action module을 연결할 때, cognition-action 분리와 재사용성·adaptation이라는 서로 다른 설계 동기는 구조를 어떻게 다르게 만드는가?
- **논문:**
  - Li et al. (2024), *CogACT: A Foundational Vision-Language-Action Model for Synergizing Cognition and Action* — anchor
  - Octo Model Team et al. (2024), *Octo: An Open-Source Generalist Robot Policy*
- **다루는 것:**
  - VLM backbone과 diffusion action module의 분리, action transformer, stage-wise training (CogACT)
  - transformer policy, diffusion head, task token, fine-tuning interface (Octo)
- **선수 연결:** Diffusion Policy와 DiT를 이 세션 전에 배치한 이유를 확인한다.
- **토론:**
  - 모듈 분리는 inductive bias인가, scale 부족을 보완하는 engineering choice인가?
  - 공개 모델을 새 sensor·robot에 붙일 때 수정해야 하는 최소 모듈은 무엇인가?

---

#### C04-S02 · RDT-1B + Diffusion-VLA: DiT 스케일업과 reasoning 조건화

- **구분:** Core
- **핵심 질문:** DiT 기반 action model을 대규모 bimanual foundation model로 키우면 무엇이 달라지고, 생성 과정에 self-generated reasoning을 조건으로 넣으면 무엇을 얻고 잃는가?
- **논문:**
  - Liu et al. (2024), *RDT-1B: a Diffusion Foundation Model for Bimanual Manipulation* — anchor
  - Wen et al. (2024), *Diffusion-VLA*
- **다루는 것:**
  - diffusion transformer, heterogeneous bimanual data, unified action representation (RDT-1B)
  - diffusion action head, reasoning condition, interpretability claim (Diffusion-VLA)
- **비교축:** π0와 같은 시기의 continuous action model이지만 diffusion vs flow, 공개 vs 폐쇄라는 차이
- **토론:** 통합 action space는 cross-embodiment 문제를 해결한 것인가, 공통 좌표계로 미룬 것인가?
- **연결:** Ch.9 reasoning의 예고편
- **Update 지침:** bimanual diffusion foundation model의 2025-26 후속 버전을 발표 1주 전 확인해 교체·보강한다.

---

#### C04-S03 · 경량·공개 VLA의 흐름: OpenVLA-OFT + SmolVLA

- **구분:** Extension / Update
- **핵심 질문:** 빅테크 밖의 공개 VLA는 어떤 구조적 차별화로 경쟁하는가?
- **논문:**
  - Kim et al. (2025), *Fine-Tuning Vision-Language-Action Models: Optimizing Speed and Success* (OpenVLA-OFT) — parallel decoding, continuous action head, fine-tuning 레시피
  - Shukor et al. (2025), *SmolVLA: A Vision-Language-Action Model for Affordable and Efficient Robotics* — 경량 VLM, flow expert, community data, async inference
- **Landscape update:** WALL-OSS, AgiBot GO-1, GR-3, RoboVLM 등은 아래 기준을 만족할 때만 짧게 추가한다.
  - action tokenizer / low-resource training / cross-embodiment interface / inference efficiency / 공개 데이터 엔진 중 하나가 다른 모델
- **발표 방식:** 한 편 정독보다 자원 제약이 설계를 어떻게 바꿨는지 비교
- **개인 연구자 관점:** 연구실 GPU에서 fine-tuning 가능한 모델은 무엇이며, 무엇을 포기한 설계인가?

---

#### Ch.4 마감 산출물 · VLA 3갈래 분류표

| 갈래 | 대표 | action 표현 | 강점 | 주요 대가 |
| --- | --- | --- | --- | --- |
| discrete token | RT-1, RT-2, OpenVLA, π0-FAST | quantized/compressed action token | VLM의 next-token interface 재사용 | 정밀도, token latency, 제어 주파수 |
| diffusion head | CogACT, Octo, RDT-1B | continuous iterative denoising | multimodality, 표현력 | sampling cost |
| flow head | π0 계열, SmolVLA | continuous flow trajectory | 적은 step과 빠른 action generation | 학습·구현 복잡도, scale 검증 |

---

### Ch.5 π 계보: 손재주에서 경험 학습까지

> 목표: π0, π0.5, π*0.6과 그 사이의 도구 논문들을 분리된 최신 논문으로 보지 않고, 각 버전이 이전 버전의 한계를 어떻게 시인하고 확장했는지 추적한다.

---

#### C05-S01 · π0 + π0.5: flow expert와 open-world 일반화

- **구분:** Core
- **핵심 질문:** discrete token의 정밀도·속도 한계를 flow-matching action expert가 어떻게 공격하며, dexterity가 확보된 뒤에도 처음 보는 환경과 long-horizon task에서 실패하는 이유는 무엇인가?
- **논문:**
  - Black et al. (2024), *π0: A Vision-Language-Action Flow Model for General Robot Control* — anchor
  - Physical Intelligence (2025), *π0.5: a Vision-Language-Action Model with Open-World Generalization*
- **다루는 것:**
  - VLM backbone, flow matching expert, action chunk, heterogeneous robot data (π0)
  - heterogeneous co-training, web data, semantic subtask, open-world task definition (π0.5)
- **선수 연결:** C03-S01의 Flow Matching을 실제 action head 숫자로 채운다.
- **중점:** 성능 숫자보다 '새 집에서 부엌 정리'와 같은 평가 task를 어떻게 정의했는지 본다.
- **비교축:** OpenVLA, CogACT, RDT-1B와 action representation 및 deployment cost 비교
- **토론:** semantic hierarchy가 실제 control generalization을 만든 것인가, task decomposition을 외부에서 제공한 것인가?

---

#### C05-S02 · π*0.6 / RECAP + Real-Time Chunking: 경험 학습과 실시간 실행

- **구분:** Core
- **핵심 질문:** demonstration만으로 학습한 VLA가 실제 실패에서 배우려면 무엇이 필요하며, 큰 flow policy가 추론 지연 속에서도 끊기지 않고 움직이려면 무엇이 필요한가?
- **논문:**
  - Physical Intelligence (2025), *π*0.6: a VLA That Learns From Experience* (RECAP) — anchor
  - Black et al. (2025), *Real-Time Execution of Action Chunking Flow Policies* (Real-Time Chunking)
- **다루는 것:**
  - experience collection, intervention, on-policy data, advantage-conditioned policy, reward/judge (RECAP)
  - 추론 지연 동안 action chunk를 이어붙이는 inpainting식 비동기 실행 (RTC)
- **쟁점:**
  - 보상을 누가 정의하며 VLM-as-judge를 얼마나 신뢰할 수 있는가?
  - chunk를 어느 구간에서 잘라 이어도 안전한가?
- **연결:** Ch.14의 평가와 reward hacking, Ch.10의 실시간성 예산

---

#### C05-S03 · Hi Robot + Knowledge Insulation: π 생태계의 계층과 학습 레시피

- **구분:** Extension
- **핵심 질문:** 낮은 수준 policy 위에 System 2 VLM을 얹는 계층화와, VLM backbone의 지식을 보호하면서 action expert를 학습시키는 레시피는 각각 어떤 실패를 막는가?
- **논문:**
  - Shi et al. (2025), *Hi Robot: Open-Ended Instruction Following with Hierarchical Vision-Language-Action Models*
  - Driess et al. (2025), *Knowledge Insulating Vision-Language-Action Models*
- **다루는 것:**
  - open-ended instruction, 사용자 개입과 피드백 처리, 고수준-저수준 분업 (Hi Robot)
  - continuous action head의 gradient가 VLM representation을 훼손하는 문제와 차단 레시피 (KI)
- **연결:** Ch.10 dual system, Ch.6 데이터 엔진

---

#### C05-S04 ◆ · 연구실 스케일 post-training: Continual RL과 capability 이식 (2026)

- **구분:** Extension
- **핵심 질문:** robot fleet 없이, 공개 VLA를 배포 후에도 계속 개선하거나 능력을 이식하는 연구는 어디까지 가능한가?
- **논문:**
  - *Towards Long-Lived Robots: Continual Learning VLA Models via Reinforcement Fine-Tuning* (2026) — anchor
  - *CapVector: Learning Transferable Capability Vectors in Parametric Space for Vision-Language-Action Models* (2026)
- **다루는 것:**
  - 시뮬레이터·벤치마크 위에서의 RL fine-tuning과 continual learning의 catastrophic forgetting 문제
  - fine-tuned 모델들 사이의 parameter-space 연산으로 능력을 추출·이식하는 접근
- **비교축:** π*0.6 RECAP(C05-S02)의 fleet 경험 학습과 같은 질문 — '배포 후에도 배우는 로봇' — 을 공개 모델 + 시뮬레이터 + 소수 GPU 규모에서 공격한다.
- **개인 연구자 관점:** OpenVLA·π0 계열 공개 가중치와 LIBERO/RoboTwin급 벤치마크로 시작할 수 있는 첫 실험 설계
- **연결:** Ch.14 평가(시뮬에서 얻은 개선의 실물 이전)

---

#### Ch.5 마감 산출물 · π 계보 한계 시인 사슬

| 버전 | 해결하려는 문제 | 추가한 것 | 다음에 드러난 한계 |
| --- | --- | --- | --- |
| π0 | 정밀한 continuous control | flow action expert | 낯선 환경·long horizon 일반화 |
| π0.5 | open-world generalization | heterogeneous co-training, semantic subtask | 실제 실패에서의 개선 |
| π*0.6 | experience learning | intervention·RECAP·advantage conditioning | reward reliability, data cost, safety |

보조 계보: FAST(tokenizer, C03-S02), Real-Time Chunking(실시간 실행), Hi Robot(계층 지시), Knowledge Insulation(학습 레시피)은 본선 버전 사이를 잇는 도구 논문으로 함께 읽는다.

---

### Ch.6 데이터·스케일·Embodiment

> 목표: VLA 일반화를 모델 아키텍처만의 문제가 아니라 데이터 엔진, embodiment alignment, action label, 합성 데이터의 문제로 본다.

---

#### C06-S01 · Gemini Robotics 1.0 + 1.5

- **구분:** Core
- **핵심 질문:** RT-2의 'semantic knowledge transfer' 주장은 실제 generalist robot system에서 어떻게 확장되고 수정되는가?
- **자료:**
  - Gemini Robotics (2025) 테크리포트 — anchor
  - Gemini Robotics 1.5 (2025) 테크리포트
- **다루는 것:**
  - embodied reasoning(ER) 모델의 분리와 역할
  - motion transfer와 cross-embodiment 학습
  - think-before-acting interface와 thinking budget (1.5)
  - 후속 버전이 이전 버전의 무엇을 보완했는가
- **발표 방식:** 버전별 요약보다 RT-2 → 1.0 → 1.5 계보의 주장과 증거를 비교한다. On-Device 등 파생 릴리스는 노트로만 다룬다.
- **Update 규칙:** 발표 1주 전 최신 공식 버전과 논문을 확인한다.

---

#### C06-S02 · GR00T N1 + N1.5

- **구분:** Core
- **핵심 질문:** humanoid foundation model에서 VLM과 action model을 dual-system으로 나누는 이유는 무엇이며, 버전업에서 무엇이 바뀌었는가?
- **자료:**
  - NVIDIA (2025), *GR00T N1: An Open Foundation Model for Generalist Humanoid Robots* — anchor
  - GR00T N1.5 공식 릴리스(모델 카드·기술 문서) — frozen VLM, 새로운 embodiment head, 합성 데이터(DreamGen) 활용 등 변화 중심
- **다루는 것:** System 2 VLM, System 1 action model(DiT/flow 계열), 실물·합성·웹 비디오로 구성된 data pyramid
- **하드웨어:** 사용 humanoid, bimanual task, onboard/offboard compute를 명시한다.
- **연결:** Ch.10 dual system, Ch.11 whole-body control과의 인터페이스
- **Update:** 이후 공식 릴리스는 구조 변화가 있을 때만 본 세션에 통합한다.

---

#### C06-S03 · LAPA + DreamGen: 라벨 없는 비디오와 생성 비디오

- **구분:** Core
- **핵심 질문:** 'action label이 붙은 실물 teleop 데이터'라는 병목을 우회하는 두 갈래 — latent action과 neural trajectory — 는 각각 어떤 학습 신호를 만들어내는가?
- **논문:**
  - Ye et al. (2024), *Latent Action Pretraining from Videos* (LAPA) — anchor
  - Jang et al. (2025), *DreamGen: Unlocking Generalization in Robot Learning through Neural Trajectories*
- **다루는 것:**
  - latent action discovery, video pretraining, policy adaptation (LAPA)
  - video world model fine-tuning → 합성 trajectory 생성 → policy 학습, generation-to-real gap (DreamGen)
- **쟁점:**
  - 해석 불가능한 latent action을 실제 로봇에서 어떻게 디버깅하고 정렬하는가?
  - 생성된 trajectory는 실제 새 정보인가, 기존 분포의 재조합인가?
- **비교축:** 실물 demonstration, simulator data, generated video trajectory의 비용과 오류
- **연결:** Ch.8 Genie의 latent action, world model as data engine

---

#### C06-S04 · MimicGen 계보: 시연을 프로그램적으로 증식하기

- **구분:** Extension
- **핵심 질문:** 소수의 사람 시연을 시뮬레이터에서 대량의 새 시연으로 증식할 때, 그 diversity는 어디까지 진짜인가?
- **논문:**
  - Jiang et al. (2024), *DexMimicGen* — anchor (humanoid·bimanual dexterous 증식)
  - Mandlekar et al. (2023), *MimicGen* — 원리 발췌만
  - Update: 발표 시점의 2025-26 demonstration synthesis 후속을 확인해 보강한다
- **다루는 것:** object-centric segment 재조합, scene 변형, 증식 데이터의 품질 필터링
- **비교축:** 실물 teleop(ALOHA 계보), 생성 비디오(DreamGen), 프로그램적 증식(MimicGen)의 비용·오류·확장성
- **연결:** GR00T 데이터 파이프라인, Ch.8 world model as data engine

---

#### C06-S05 · VLA 데이터셋·벤치마크·데이터 엔진

- **구분:** Extension
- **핵심 질문:** 현재 VLA 병목은 모델보다 데이터 infrastructure에 있는가?
- **권장 자료:** Open X-Embodiment(재방문), DROID, BridgeData, AgiBot World, LeRobot community data 및 최신 data-centric VLA survey
- **다루는 것:**
  - embodiment diversity
  - action space mismatch
  - temporal alignment
  - task annotation과 language quality
  - real / sim / generated data mixture
- **산출물:** 데이터셋을 episode 수가 아니라 fidelity, diversity, action semantics, reproducibility로 비교

---

#### C06-S06 · Cross-embodiment action representation

- **구분:** Extension / Update
- **핵심 질문:** 서로 다른 관절 구조와 action dimension을 하나의 모델에 어떻게 정렬하는가?
- **비교 후보:** RDT unified action space, Gemini motion transfer, GR00T embodiment head, 최신 canonical action representation
- **발표 방식:** 모델 전체를 소개하지 말고 cross-embodiment interface만 분리해 비교한다.

---

#### C06-S07 ◆ · 연구실 데이터 엔진 2026: 로봇 없이, 저가 하드웨어로

- **구분:** Extension
- **핵심 질문:** 2026년 현재, 대학 연구실이 감당 가능한 비용으로 만들 수 있는 데이터 수집 시스템은 어디까지 왔는가?
- **논문:**
  - *RealDexUMI: A Wearable Universal Manipulation Interface for Dexterous Robot Learning* (2026) — anchor
  - *YOR: Your Own Mobile Manipulator for Generalizable Robotics* (2026)
- **다루는 것:**
  - 로봇 없이 사람 시연을 수집하는 wearable interface와 embodiment gap 처리 — UMI(2024) 계보의 최신 (RealDexUMI)
  - 저비용 자작 mobile manipulator 플랫폼과 그 위에서의 generalist policy 학습 (YOR)
- **그 외 2026 후보:** MEVION(저가 고속 dual-arm 수집 시스템), YUBI(bimanual dexterous 수집 interface), XLeRobot 계열 — 발표 시점에 하나를 골라 짧게 비교한다.
- **비교축:** ALOHA(C02-S03) → UMI 계보 → 2026 시스템으로 이어지는 '데이터 수집 민주화'의 현재 지점. Ch.6 데이터 엔진 비교표에 행을 추가한다.
- **개인 연구자 관점:** 어떤 시스템이 우리 연구실 예산·인력으로 재현 가능한가, 수집한 데이터로 어떤 정책 연구가 열리는가

---

#### Ch.6 마감 산출물 · 데이터 엔진 비교표

| 축 | 확인할 내용 |
| --- | --- |
| 데이터 출처 | 실물 teleoperation / human video / simulator / generated world / 프로그램적 증식 |
| supervision | action label / language / reward / latent action |
| embodiment | 단일 / 다중 / humanoid / canonical representation |
| 비용 | 수집 장비, 인력, compute, post-processing |
| 주요 오류 | label mismatch, temporal drift, sim gap, generation artifact |

---

### Ch.7 Predictive Representation과 기하

> 목표: world model이 무엇을 예측해야 하는지 묻는다. 픽셀 생성, latent prediction, 명시적 3D·4D representation의 전제와 반증 조건을 비교한다.

---

#### C07-S01 · LeCun의 자율지능 아키텍처와 I-JEPA

- **구분:** Core
- **핵심 질문:** 예측 불가능한 픽셀 디테일을 생성하지 않고 latent에서 예측하면 무엇이 달라지는가?
- **자료:**
  - LeCun (2022), *A Path Towards Autonomous Machine Intelligence* — 관련 architecture·predictive learning 절만 발췌
  - Assran et al. (2023), *Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture* — anchor
- **다루는 것:** predictor, target encoder, representation collapse 방지, energy-based 관점의 최소 개념
- **범위:** position paper 전체 정독과 장기 철학 토론을 피하고, I-JEPA 설계로 이어지는 부분만 다룬다.
- **비교:** pixel reconstruction 계열과 latent prediction 계열이 각각 버리는 정보

---

#### C07-S02 · V-JEPA 2 + V-JEPA 2.1

- **구분:** Core
- **핵심 질문:** 인터넷 비디오에서 학습한 latent representation이 실제 prediction과 robot planning에 충분한가?
- **논문:** V-JEPA 2 — anchor; V-JEPA 2.1 등 최신 V-JEPA 2.x 공식 논문
- **다루는 것:** video pretraining, action-conditioned model, planning, real-robot evaluation, dense feature 개선
- **발표 방식:** I-JEPA부터 모든 버전을 순서대로 요약하지 않는다. 다음 변화만 추적한다.
  - image → video
  - global representation → dense spatiotemporal feature
  - understanding → prediction·planning·robot control
- **핵심 질문:** 접촉, 힘, 미세 기하처럼 latent representation이 놓칠 수 있는 정보는 무엇인가?

---

#### C07-S03 · DUSt3R + VGGT: geometry foundation model

- **구분:** Core
- **핵심 질문:** 3D를 얻기 위해 고전적인 calibration·reconstruction pipeline이 반드시 필요한가? 하나의 feed-forward transformer로 camera, depth, pointmap, track을 동시에 추론할 수 있는가?
- **논문:**
  - Wang et al. (2024), *DUSt3R: Geometric 3D Vision Made Easy* — anchor
  - Wang et al. (2025), *VGGT: Visual Geometry Grounded Transformer*
- **다루는 것:** pointmap regression, pairwise geometry, camera-free 3D reconstruction, 단일 feed-forward 모델로의 통합
- **후속 계보:** MASt3R, CUT3R 등은 핵심 변화만 짧게 비교
- **연결:** 3D Diffusion Policy가 필요로 하는 geometry를 더 싸게 얻는 방법
- **범위:** 모든 geometry 후속 논문을 정독하지 않는다.

---

#### C07-S04 · 3D-VLA + 4D-VLA

- **구분:** Core
- **핵심 질문:** 정적 3D representation에 시간을 추가한 4D representation은 VLA에 어떤 정보를 더 주는가?
- **논문:**
  - Zhen et al. (2024), *3D-VLA* — anchor
  - Zhang et al. (2025), *4D-VLA*
- **발표 방식:** 두 논문을 각각 요약하지 않고 `2D → 3D → 4D` 전이에서 새로 필요한 supervision과 compute를 비교한다.
- **쟁점:** 명시적 geometry가 성능을 만드는가, 추가 sensor·annotation·model capacity가 만드는가?

---

#### Ch.7 마감 산출물 · 표현 선택과 반증 조건 표

각 세션의 발제에서 채워 학기 중 완성한다. structure vs scale 논쟁은 별도 토론 세션 없이 이 표의 '반증 조건' 열로 관리한다 — 반증 조건은 진영 선호가 아니라 검증 가능한 조건으로 적는다.

| 표현 | 얻는 것 | 잃는 것 | 추가 비용 | 반증 조건 |
| --- | --- | --- | --- | --- |
| pixel/video | 풍부한 관측 재현 | 불필요한 디테일·생성 비용 | 대규모 compute | 생성 품질이 planning과 무관함 |
| latent | 효율·semantic abstraction | 국소 geometry·contact 손실 가능 | representation validation | latent가 실제 제어에 필요한 정보를 잃음 |
| explicit 3D/4D | geometry·view invariance | sensor·calibration·pipeline 의존 | depth/compute | 2D scale가 같은 일반화를 달성함 |

---

### Ch.8 World Model을 도구로 쓰기

> 목표: world model을 모호한 '세계 이해'가 아니라 정책 학습, planning, 데이터 생성, 평가에 사용되는 구체적 도구로 분해한다.

---

#### C08-S01 · World model 계보: imagination에서 scalable agent까지

- **구분:** Core
- **핵심 질문:** '꿈속에서 학습한다'는 아이디어는 최신 world model agent에서 무엇으로 남았는가?
- **자료:**
  - Ha & Schmidhuber (2018), *World Models* — latent dynamics, imagination, controller 부분 발췌
  - 최신 Dreamer 계열 대표 논문(Dreamer 4) — anchor
- **발표 방식:** 2018년 논문을 역사적 원전으로 정독하지 않는다. 최신 agent architecture의 구성요소가 어디에서 왔는지 추적한다.
- **다루는 것:** representation model, dynamics model, reward prediction, imagined rollout, actor-critic learning
- **토론:** scale이 커진 것 외에 실제로 해결된 instability와 long-horizon 문제가 무엇인가?

---

#### C08-S02 · Genie + Genie 3: latent action에서 실시간 상호작용 세계로

- **구분:** Core
- **핵심 질문:** action label이 없는 비디오에서 제어 가능한 세계를 학습하는 아이디어는 실시간 상호작용 세계까지 어떻게 확장되었는가?
- **자료:**
  - Bruce et al. (2024), *Genie: Generative Interactive Environments* — anchor
  - Google DeepMind (2025), Genie 3 공식 블로그·데모
- **다루는 것:**
  - latent action, video tokenizer, action-controllable generation (Genie)
  - 실시간 720p/24fps 생성, 분 단위 일관성, promptable world event, agent 학습 환경으로서의 쓰임 (Genie 3)
- **비교:** LAPA는 latent action을 policy pretraining에 쓰고, Genie는 world generation에 쓴다.
- **주의:** 공식 데모의 visual quality와 agent training utility를 구분한다. Genie 3는 기술 보고서가 얇으므로 claim-evidence 구분 연습 대상으로 삼는다.

---

#### C08-S03 · Cosmos 플랫폼 + Cosmos-Transfer

- **구분:** Core
- **핵심 질문:** world model을 단일 논문이 아니라 데이터·생성·제어 플랫폼으로 만들면 연구 문제가 어떻게 분해되는가?
- **자료:** Cosmos world foundation model platform — anchor; Cosmos-Transfer 계열 공식 논문·릴리스
- **다루는 것:** Predict / Transfer / Reason 기능 분해, multimodal conditioning, synthetic data pipeline
- **발표 방식:** 모델 이름을 나열하지 않고 플랫폼 기능 분해도 한 장으로 통합한다.
- **개인 연구자 관점:** 거대 모델 학습보다 공개 모델을 이용한 data pipeline·evaluation 설계에서 배울 점

---

#### C08-S04 · Cosmos-Predict2.5 + Cosmos 3: 통합 world model 백본

- **구분:** Core / Update
- **핵심 질문:** video world model 계보는 왜 '용도별 모델 묶음'에서 '통합 flow 모델'로, 다시 'reasoning·generation·action을 한 백본에 넣은 omnimodal 모델'로 이동하는가?
- **자료:**
  - NVIDIA (2025), *World Simulation with Video Foundation Models for Physical AI* (Cosmos-Predict2.5) — anchor
  - NVIDIA (2026), *Cosmos 3: Omnimodal World Models for Physical AI* — Cosmos-Predict 계보의 3세대
- **다루는 것:**
  - Text2World / Image2World / Video2World의 단일 flow 모델 통합, Cosmos-Reason과의 결합, RL 기반 post-training, 데이터 큐레이션 (Predict2.5)
  - mixture-of-transformers, understanding·generation·action의 단일 백본 통합, omnimodal 평가 (Cosmos 3)
- **쟁점:** 통합 백본은 로봇 정책 학습에 실제로 무엇을 주는가 — 표현인가, 시뮬레이터인가, 정책 초기화인가?
- **연결:** C08-S03의 기능 분해도 갱신, Ch.9 Cosmos-Reason, Ch.14 평가

---

#### C08-S05 · Ctrl-World + WorldVLA: 분리형과 통합형

- **구분:** Core
- **핵심 질문:** 정책이 지정한 action을 따르는 미래 관측을 '분리된 controllable world model'로 만들 것인가, policy와 world model을 하나의 autoregressive 모델로 합칠 것인가?
- **논문:**
  - *Ctrl-World: A Controllable Generative World Model for Robot Manipulation* — anchor
  - *WorldVLA: Towards Autoregressive Action World Model*
- **다루는 것:**
  - action conditioning, controllability, temporal consistency, manipulation-specific evaluation (Ctrl-World)
  - observation/action을 하나의 sequence로 modeling, 정책·세계 예측의 상호 개선 주장 (WorldVLA)
- **쟁점:**
  - 생성 결과가 action을 시각적으로 따라가는 것과 실제 dynamics가 정확한 것은 같은가?
  - 통합 objective가 planning 능력을 실제로 보장하는가, 단순 next-token prediction에 머무는가?

---

#### C08-S06 · World model as data engine & policy evaluator

- **구분:** Extension
- **핵심 질문:** world model을 데이터 생성기로 쓸 때와 정책 평가기로 쓸 때, 각각 어디에서 오류가 증폭되는가?
- **자료:**
  - C06-S03의 DreamGen을 world model 관점에서 재분석
  - *Evaluating Gemini Robotics Policies in a Veo World Simulator* 관련 공식 논문
- **다루는 것:**
  - generation artifact → action label error → policy failure의 오류 전파
  - policy evaluation protocol, real-world correlation, red-teaming, coverage (Veo)
- **연결:** Ch.14의 순환 논증 문제
- **산출물:** 데이터 생성·정책 평가 각각의 오류 전파도

---

#### C08-S07 · World-Gymnast와 RL inside world models

- **구분:** Extension / Update
- **핵심 질문:** 생성 world model rollout과 VLM reward로 실제 policy를 개선할 수 있는가?
- **논문:** *World-Gymnast: Training Robots with Reinforcement Learning in a World Model*
- **비교축:** physics simulator vs generative world model
- **토론:** 두 접근의 sim-to-real gap은 어떤 종류로 다른가?

---

#### Ch.8 마감 산출물 · World model 기능 분해표

| 기능 | 입력 | 출력 | 정책과의 결합 | 대표 실패 |
| --- | --- | --- | --- | --- |
| representation | 관측 | latent state | policy input | 필요한 정보 손실 |
| prediction | state/action | future state | planner rollout | compounding error |
| generation | video/action condition | future observation | data augmentation | visual artifact, dynamics error |
| reward/evaluation | rollout/task | score | RL·policy selection | judge bias, circular evaluation |
| interactive simulator | user/policy action | responsive world | online agent training | controllability·consistency trade-off |

---

### Ch.9 행동 전에 생각하기

> 목표: VLA의 reasoning을 'CoT가 있는가'로 분류하지 않고, 추론의 매체·위치·지연·검증 가능성으로 비교한다.

---

#### C09-S01 · Cosmos-Reason + Alpamayo-R1: ontology와 인과 라벨

- **구분:** Core
- **핵심 질문:** physical common sense와 인과를 명시적 ontology·라벨·reasoning supervision으로 만들 수 있는가 — 조작과 자율주행 각각에서?
- **논문:**
  - Cosmos-Reason 계열 대표 논문 — anchor
  - Alpamayo-R1 — 자율주행의 Chain of Causation
- **다루는 것:**
  - physical reasoning taxonomy, data construction, reasoning trace, downstream use (Cosmos-Reason)
  - 인과 라벨 데이터, trajectory decoder와 reasoning의 정렬, latency와 RL alignment (Alpamayo-R1)
- **쟁점:**
  - ontology가 실제 physics를 학습시키는가, 언어적 설명 능력을 평가하는가?
  - 조작 task에서 인과 라벨과 long-tail scenario를 어떻게 싸게 만들 수 있는가?
- **연결:** C08-S04(Reason이 Predict에 결합됨), Ch.10 latency

---

#### C09-S02 · ECoT + CoT-VLA: 언어로 생각하기와 이미지로 생각하기

- **구분:** Core
- **핵심 질문:** action 앞에 자연어 plan을 생성하는 것과 미래 프레임을 생성하는 것은 각각 성능·지연·해석 가능성에 무엇을 주는가?
- **논문:**
  - Zawalski et al. (2024), *Robotic Control via Embodied Chain-of-Thought Reasoning* (ECoT) — anchor
  - Zhao et al. (2025), *CoT-VLA: Visual Chain-of-Thought Reasoning for Vision-Language-Action Models*
- **다루는 것:**
  - OpenVLA 기반 language reasoning, visual grounding, language supervision (ECoT)
  - future image generation, subgoal visualization, action conditioning, generation latency (CoT-VLA)
- **토론:**
  - reasoning trace는 모델을 위한 representation인가, 사람을 위한 interface인가?
  - 미래 이미지는 explanation인가, intermediate plan인가?

---

#### C09-S03 · ACoT + DualCoT: action-space reasoning과 병렬화

- **구분:** Core
- **핵심 질문:** 언어와 이미지를 거치지 않고 action space 안에서 reasoning할 수 있는가, 그리고 autoregressive reasoning의 지연·오류 누적을 병렬화로 줄일 수 있는가?
- **논문:**
  - ACoT-VLA 계열 대표 논문 — anchor
  - DualCoT-VLA 계열 대표 논문
- **다루는 것:**
  - action chain, latent/action reasoning, direct control interface (ACoT)
  - visual-linguistic parallel reasoning, synchronization, final action decoding (DualCoT)
- **trade-off:** 지연 감소와 해석 가능성 손실
- **비교:** visual / language / action reasoning을 같은 task와 latency 기준으로 비교
- **연결:** Ch.10의 latency budget

---

#### C09-S04 · Reasoning-VLA 최신 업데이트

- **구분:** Extension / Update
- **선택 기준:** 다음 중 하나 이상을 새롭게 제시한 논문만 다룬다.
  - 새로운 추론 매체 또는 추론 위치
  - 지연을 줄이는 새로운 메커니즘
  - reasoning trace와 실제 action의 일치를 검증하는 방법
- **발표 방식:** Ch.9 마감 비교표에 행을 추가하는 형식으로 정리한다.

---

#### Ch.9 마감 산출물 · 추론 비교표

| 추론 매체 | 대표 | 장점 | 지연 비용 | 검증 방식 | 주요 실패 |
| --- | --- | --- | --- | --- | --- |
| language | ECoT, Gemini reasoning | 사람이 읽을 수 있음 | token decoding | trace-grounding 일치 | 그럴듯한 설명, action 불일치 |
| image/video | CoT-VLA | 공간 subgoal 표현 | generation cost | future observation consistency | hallucinated future |
| action/latent | ACoT | 빠르고 direct | 내부 step 비용 | action outcome | 해석 불가 |
| ontology/causal label | Cosmos-Reason, Alpamayo | 명시적 구조 | annotation cost | causal consistency | label bias |

---

### Ch.10 실시간성과 Dual System

> 목표: 실시간성을 단순 inference optimization이 아니라 perception, reasoning, action, reflex의 서로 다른 시간 규모를 조정하는 시스템 문제로 본다.

---

#### C10-S01 · Fast-in-Slow + Hume

- **구분:** Core
- **핵심 질문:** 느린 System 2는 빠른 System 1을 생성·조정·선택하는가?
- **논문:** Fast-in-Slow — anchor; Hume 대표 논문
- **발표 방식:** 두 논문을 각각 요약하지 않고 System 2의 역할로 비교한다.
  - 통합형: fast module이 slow model 내부에 위치
  - 탐색형: slow model이 action 후보를 생성하고 value로 선택
- **비교:** GR00T N1의 분리형 구조, Hi Robot(C05-S03)의 계층형 구조
- **주의:** 인간 뇌의 System 1/2 유비를 설계 근거와 사후 설명으로 구분한다.

---

#### C10-S02 · Reactive Diffusion Policy + TacMamba: 다중 주파수 제어

- **구분:** Core
- **핵심 질문:** 느린 시각 계획과 빠른 촉각 반응을 서로 다른 frequency로 실행하려면, 고주파 sensor history를 무엇으로 압축해 느린 reasoning에 전달해야 하는가?
- **논문:**
  - Xue et al. (2025), *Reactive Diffusion Policy* — anchor
  - TacMamba 계열
- **다루는 것:**
  - slow-fast policy, visual-tactile asymmetry, reactive control (RDP)
  - tactile history compression, state-space model, fast reflex adapter (TacMamba)
- **되짚기:** ACT의 chunk가 길수록 반응성이 떨어지는 문제
- **연결:** Ch.12 tactile signal의 시간 특성, multimodal bandwidth mismatch

---

#### C10-S03 · Think Twice, Act Once + DeeR-VLA: adaptive inference

- **구분:** Core
- **핵심 질문:** 모델을 작게 만들지 않고도 추론 횟수, token 수, 사용하는 layer 수를 상황에 따라 줄일 수 있는가?
- **논문:**
  - *Think Twice, Act Once* 계열 — anchor
  - Yue et al. (2024), *DeeR-VLA: Dynamic Inference of Multimodal Large Language Models for Efficient Robot Execution*
- **다루는 것:**
  - token compression, adaptive inference, action reuse, replanning trigger (TTAO)
  - dynamic early-exit, 상황 난이도에 따른 compute 배분 (DeeR-VLA)
- **쟁점:** action을 재사용해도 되는 안정 구간을 어떻게 감지하는가?
- **비교:** fixed chunk, receding horizon, event-triggered replanning

---

#### C10-S04 · Onboard deployment budget

- **구분:** Core / 실습
- **핵심 질문:** 논문의 architecture를 실제 GPU·edge device에서 몇 Hz로 실행할 수 있는가?
- **대상:** OpenVLA, π0 계열 공개 구현, diffusion policy, compact VLA, 연구실 보유 GPU 또는 Jetson급 device
- **다루는 것:**
  - model size와 memory
  - vision encoder frequency
  - action head sampling step
  - network·camera·robot communication latency
  - warm-up, batch size, precision
  - Real-Time Chunking(C05-S02)의 비동기 실행이 예산을 어떻게 바꾸는가
- **산출물:** 실제 또는 공식 수치에 근거한 end-to-end latency budget

---

#### Ch.10 마감 산출물 · 실시간성 예산표

| 단계 | 시간 | 실행 주기 | 줄일 수 있는 방법 | 줄였을 때의 위험 |
| --- | --- | --- | --- | --- |
| camera/sensor acquisition |  |  | downsampling | contact·motion 손실 |
| vision encoding |  |  | frame reuse, smaller encoder | stale representation |
| reasoning |  |  | parallel/latent reasoning | interpretability 감소 |
| action generation |  |  | fewer flow/diffusion steps, async chunking | action quality 저하 |
| communication/control |  |  | onboard execution | compute·thermal 제한 |

---

### Ch.11 Locomotion과 Whole-Body Control

> 목표: manipulation 중심 VLA 계보와 거의 독립적으로 발전해 온 locomotion·whole-body RL 계보의 문제 설정을 이해하고, 두 계보가 humanoid에서 만나는 지점을 본다.
>
> 이 Chapter는 조작 중심인 스터디 주 흐름에서 선택적이며, Ch.12와 순서를 바꾸거나 병렬로 진행할 수 있다. 다만 GR00T·iDP3 등 humanoid 논문의 '하체·전신은 별도 컨트롤러' 구조를 이해하려면 최소 한 세션(C11-S01 또는 C11-S02)을 권장한다.

공통 대비축 (vs manipulation VLA):

- 학습 신호: demonstration이 아니라 시뮬레이터 상호작용과 보상
- 제어 주파수: 수십~수백 Hz의 proprioceptive 제어
- sim-to-real: domain randomization과 시스템 식별이 1급 문제
- 실패 비용: 낙상 — 안전과 하드웨어 파손

---

#### C11-S01 · Locomotion RL의 현재: 지형 locomotion과 agile motion tracking

- **구분:** Core (챕터 내)
- **핵심 질문:** 왜 locomotion은 demonstration 없이 시뮬레이터 RL만으로 학습이 가능하며, 2025-26년의 전선은 어디까지 와 있는가?
- **논문:**
  - *Learning Perceptive Humanoid Locomotion over Challenging Terrain* (2025) — anchor
  - Liao et al. (2025), *BeyondMimic: From Motion Tracking to Versatile Humanoid Control via Guided Diffusion*
- **다루는 것:**
  - perception 기반 지형 locomotion, 보상 설계, curriculum, domain randomization, teacher-student distillation (Perceptive Terrain)
  - 사람 동작 tracking으로 배운 agile 기술을 guided diffusion으로 조합하는 test-time 제어 (BeyondMimic)
- **배경 노트:** 대규모 병렬 시뮬 RL(Isaac Gym 계열, 2021~)과 quadruped parkour 계보는 원리만 한 문단으로 요약하고 원 논문은 정독하지 않는다.
- **토론:** manipulation은 왜 같은 방식으로 풀리지 않는가? (접촉 다양성, 물체 분포, 보상 정의의 어려움)

---

#### C11-S02 · Humanoid whole-body control: HOVER + ASAP

- **구분:** Core (챕터 내)
- **핵심 질문:** 서로 다른 제어 모드를 하나의 whole-body 컨트롤러로 통합할 수 있는가, 그리고 시뮬과 실물의 물리 격차는 어떻게 보정하는가?
- **논문:**
  - He et al. (2024), *HOVER: Versatile Neural Whole-Body Controller for Humanoid Robots* — anchor
  - He et al. (2025), *ASAP: Aligning Simulation and Real-World Physics for Learning Agile Humanoid Whole-Body Skills*
- **다루는 것:**
  - motion tracking 기반 통합 컨트롤러 distillation, command space 통일 (HOVER)
  - delta action model로 dynamics 격차 보정, agile 전신 기술 (ASAP)
- **연결:** GR00T 계열에서 VLA(상체 조작)와 whole-body controller(하체·전신)가 만나는 인터페이스

---

#### C11-S03 · 전신 teleop 데이터 수집: TWIST + TWIST2

- **구분:** Extension / Update
- **핵심 질문:** 사람의 전신 동작을 humanoid 데이터로 바꾸는 retargeting·teleop 시스템은 manipulation 데이터 엔진이 될 수 있는가?
- **논문:**
  - Ze et al. (2025), *TWIST: Teleoperated Whole-Body Imitation System* — anchor
  - *TWIST2: Scalable, Portable, and Holistic Humanoid Data Collection System* (2025)
- **다루는 것:** human motion retargeting, RL+BC 통합 whole-body controller, 이동형 데이터 수집 시스템으로의 확장, 수집 데이터로부터의 autonomous skill 학습
- **비교 노트:** HumanPlus, OmniH2O, ExBody2(2024)는 계보 배경으로 짧게만 짚는다.
- **연결:** Ch.2 ALOHA 계보의 전신 확장, Ch.6 데이터 엔진 비교표에 행 추가

---

#### Ch.11 마감 산출물 · manipulation vs locomotion 문제 설정 비교표

| 축 | manipulation VLA | locomotion RL |
| --- | --- | --- |
| 학습 신호 | demonstration·teleop | 시뮬레이터 RL·보상 |
| 데이터 병목 | 실물 수집 비용 | sim-to-real gap |
| 제어 주파수 | 수 Hz ~ 수십 Hz | 수십 ~ 수백 Hz |
| 일반화 대상 | 물체·task·환경 | 지형·외란·dynamics |
| 실패 비용 | task 실패 | 낙상·파손 |
| 만나는 지점 | humanoid 전신 조작, loco-manipulation | 〃 |

---

### Ch.12 촉각과 힘

> 목표: 촉각과 힘을 단순한 추가 modality가 아니라 시각과 통계적·시간적 성질이 다른 signal로 다룬다.

공통 비교축:

- 센서 종류와 위치
- sampling frequency
- contact가 없을 때 signal의 의미
- VLA에 융합하는 위치
- architecture 변경 여부
- 사전학습과 calibration 요구

---

#### C12-S01 · VTLA + ForceVLA: tactile image와 force/torque

- **구분:** Core
- **핵심 질문:** 시각만으로 원리적으로 풀기 어려운 manipulation task는 어떻게 정의하며, tactile image와 wrist force-torque vector는 같은 modality로 취급할 수 있는가?
- **논문:**
  - Zhang et al. (2025), *VTLA: Vision-Tactile-Language-Action Model with Preference Learning for Insertion Manipulation* — anchor
  - Yu et al. (2025), *ForceVLA*
- **다루는 것:**
  - insertion task, tactile modality, preference learning (VTLA)
  - 6-axis force/torque, force-aware MoE, contact-rich manipulation (ForceVLA)
- **하드웨어:** tactile sensor와 contact setup을 반드시 명시한다.
- **주의:** 두 논문은 센서·task가 다르므로 성공률을 직접 비교하지 않는다.
- **토론:** 촉각의 효과인가, task-specific inductive bias의 효과인가?
- **산출물:** tactile / force / proprioception 신호 구분표

---

#### C12-S02 · Tactile-VLA + TacVLA/TAP-VLA: grounding과 융합 위치

- **구분:** Core
- **핵심 질문:** VLA가 언어와 시각에서 이미 학습한 물성 지식을 tactile signal과 연결할 수 있는가? tactile token을 모델에 직접 넣는 것과 visual prompt로 우회하는 것 중 어느 쪽이 유리한가?
- **논문:**
  - Huang et al. (2025), *Tactile-VLA* — anchor
  - TacVLA, TAP-VLA — `architecture modification vs input transformation` 대조용
- **다루는 것:**
  - physical language instruction, tactile generalization, pretrained VLA knowledge (Tactile-VLA)
  - contact-aware selective gating vs tactile annotation overlay/prompting (TacVLA/TAP-VLA)
- **쟁점:** '부드럽게', '미끄럽다' 같은 언어 개념이 실제 force/contact control로 grounding되는가?
- **개인 연구자 관점:** 기존 VLA 가중치를 수정하지 않고 tactile 정보를 넣는 접근의 재현 가능성

---

#### C12-S03 · Reactive Diffusion Policy 재방문 + Tactile-WAM

- **구분:** Extension
- **핵심 질문:** tactile modality의 가치는 표현 정보보다 빠른 feedback loop에 있는가? 희소·국소·이벤트성 tactile signal은 왜 visual dynamics model을 오염시키는가?
- **자료:**
  - C10-S02를 tactile fusion 위치와 sampling frequency 관점에서 재분석
  - *Tactile-WAM: Touch-Aware World Action Model with Tactile Asymmetric Attention*
- **다루는 것:** tactile pollution, asymmetric attention, world-action modeling
- **되짚기:** JEPA·world model latent가 contact 정보를 얼마나 보존하는가?
- **산출물:** modality별 적정 update frequency 표

---

#### C12-S04 · 촉각·힘 최신 논문 업데이트

- **구분:** Update
- **선택 기준:** 센서 숫자를 늘린 연구가 아니라 다음 중 하나를 새롭게 해결한 연구 (후보: UniTacVLA 등)
  - contact onset detection
  - sensor-free force inference
  - cross-sensor generalization
  - high-frequency control
  - tactile pretraining
  - calibration-free fusion

---

#### Ch.12 마감 산출물 · 융합 위치 비교표

| 접근 | 신호 | 융합 위치 | architecture 변경 | 장점 | 실패 가능성 |
| --- | --- | --- | --- | --- | --- |
| input token | tactile image | 입력단 | 작음/중간 | 단순 | no-contact noise |
| gated fusion | tactile/contact | 중간층 | 큼 | 필요한 때만 사용 | gate failure |
| action-head fusion | force/tactile | action module | 중간 | control 직접 반영 | semantic backbone과 단절 |
| visual overlay | tactile field | 입력 우회 | 없음 | 기존 VLA 재사용 | 정보 압축·왜곡 |
| slow-fast | visual+tactile | policy hierarchy | 큼 | 빠른 반응 | synchronization |

---

### Ch.13 Social world model

> 목표: world model의 시야를 물리 세계에서 사회적 세계로 한 번 확장한다. 사람과 에이전트의 믿음·의도·행동을 상태로 취급하는 social world model이 physical world model과 어떤 구조를 공유하고 무엇이 다른지 확인한다.

---

#### C13-S01 · Social world model — 사람과 에이전트를 상태로 모델링하기

- **구분:** Extension
- **핵심 질문:** physical world model이 물체의 상태와 동역학을 예측하듯, 상호작용하는 에이전트의 mental state와 다음 행동을 예측하는 world model은 어떻게 정형화하고 평가하는가?
- **논문:**
  - *Social World Models* (Zhou et al., 2025/26) — anchor. 사회적 상호작용을 상태·행동·mental state의 구조화된 표현(S3AP)으로 정형화하고, 이를 통해 LLM의 theory-of-mind 추론(FANToM 등)을 크게 개선
  - *Building Social World Models with Large Language Models* (2026) — 사건에 따라 사회적 믿음이 어떻게 갱신되는지를 추적하는 SWM 프레임워크
- **다루는 것:**
  - social world model의 정의 — 관측 뒤에 숨은 mental state(믿음·의도·감정)를 latent 상태로 추적한다는 점에서, partially observable world model과 같은 구조라는 관점
  - S3AP 표현 형식과 theory-of-mind 벤치마크의 평가 방식
  - **데이터셋:** NVIDIA **Nemotron-Personas** — 실제 인구통계·지리 분포에 기반한 다국가 합성 페르소나 컬렉션(7개국 9개 locale, 약 5,300만 개; ko_KR 포함). social simulation의 '인구'를 무엇으로 채우고, 그 대표성·편향을 어떻게 통제하는가
  - physical AI와의 접점: 사람이 있는 환경에서 동작하는 로봇(HRI), 보행자·운전자 의도 예측(autonomous driving), 협업 manipulation
- **토론:** physical world model과 social world model은 하나의 모델로 통합되어야 하는가, 별개 모듈로 두고 인터페이스만 맞추면 되는가?
- **연결:** Ch.8의 world model 기능 분해(상태 공간에 '다른 에이전트의 mental state'를 추가하는 확장), Ch.9의 reasoning 매체 논의, Ch.14 평가(사회적 추론 벤치마크도 shortcut을 측정할 수 있다)

---

#### Ch.13 마감 산출물 · physical vs social world model 비교 메모

| 축 | Physical world model | Social world model |
| --- | --- | --- |
| 상태 | 물체 pose·동역학 | 에이전트의 믿음·의도·감정 |
| 관측 가능성 | 부분 관측 (occlusion) | 근본적으로 비관측 (mental state) |
| 전이 규칙 | 물리 법칙 | 사회 규범·전략적 행동 |
| 평가 | physics benchmark·rollout 일관성 | theory-of-mind 벤치마크·시뮬레이션 |
| 데이터 | 로봇 시연·비디오 | 대화·상호작용 기록·합성 페르소나 (Nemotron-Personas) |

---

### Ch.14 평가와 물리 일관성

> 목표: benchmark score, visual plausibility, internal world consistency, real-robot utility를 분리해서 평가한다.

---

#### C14-S01 · 평가 지형 요약: 벤치마크, 평가 방식, 함정

- **구분:** Core
- **핵심 질문:** 2026년 현재 world model과 VLA를 평가하는 데이터셋·벤치마크·프로토콜에는 무엇이 있고, 각각 무엇을 측정하며 무엇을 놓치는가?
- **발표 방식:** 논문 정독이 아니라 평가 지형의 빠른 요약이다. 아래 네 묶음을 평가 계층표 한 장으로 정리한다.
  1. **생성 모델의 내재 일관성** — Vafa et al. (2024), *Evaluating the World Model Implicit in a Generative Model*: 그럴듯한 생성과 일관된 내부 상태의 구분, counterfactual probe 개념만 발췌
  2. **Physics benchmark** — Physics-IQ, WorldModelBench: 무엇을 judge로 쓰는가(사람 / VLM / programmatic metric) 비교
  3. **정책 평가용 시뮬 벤치마크** — LIBERO, SimplerEnv: real-to-sim 상관과 그것이 무너지는 조건
  4. **실로봇 평가·재현성과 순환 논증** — camera pose, calibration, reset, object set, operator 개입 같은 재현성 변수 + world model로 policy를 평가할 때의 순환 논증(Veo World Simulator, World-Gymnast 재방문)
- **쟁점:** benchmark가 모델의 shortcut을 측정하는 것은 아닌가? evaluator와 policy가 같은 실패를 공유하면 무엇이 남는가?
- **실습(선택):** 공개 VLA 논문 하나의 evaluation protocol을 재현 체크리스트로 변환
- **산출물:** 평가 계층표 완성 + 우리가 실제로 쓸 수 있는 평가 스택 메모

---

#### C14-S02 · Reward model과 preference optimization의 물리성

- **구분:** Core
- **핵심 질문:** 물리 법칙을 직접 모델링하는 대신 '물리적으로 그럴듯함'에 대한 선호를 학습하는 것이 정당한가?
- **논문:** PhyGDPO, PhysMaster 등 대표 논문
- **연결:** π*0.6의 judge, World-Gymnast의 reward, autonomous driving RL alignment
- **토론:** judge가 포착하지 못하는 위반을 정책·생성 모델이 악용할 수 있는가?

---

#### C14-S03 ◆ · 연구실 스케일 평가·분석 연구 (2026)

- **구분:** Extension
- **핵심 질문:** 새 모델을 만들지 않고도, 저가 로봇 벤치마크와 통제된 실증 분석만으로 어떤 기여가 가능한가?
- **논문:**
  - *VLA-REPLICA: A Low-Cost, Reproducible Benchmark for Real-World Evaluation of Vision-Language-Action Models* (2026) — anchor
  - *Benchmarking Vision-Language-Action Models on SO-101: Failure and Recovery Analysis* (2026)
- **다루는 것:**
  - 저비용·재현 가능한 실물 평가 프로토콜의 설계 (VLA-REPLICA)
  - LeRobot SO-101급 저가 암에서의 표준화 평가, 실패 분류 체계(failure taxonomy), recovery 지표 (SO-101 벤치마크)
- **분석 연구 후보(하나를 골라 짧게):** *VLM4VLA*(VLM backbone의 기여를 되묻는 실증 연구), *Do World Action Models Generalize Better than VLAs?*(perturbation 강건성 비교 연구)
- **연결:** C14-S01의 재현성 묶음을 '우리도 만들 수 있는 평가 연구'로 확장
- **개인 연구자 관점:** 벤치마크·실패 분석 논문이 기여로 인정받는 조건 — 프로토콜·코드 공개, 통계적 엄밀성, 다른 연구의 채택 가능성

---

#### Ch.14 마감 산출물 · 평가 계층표

| 계층 | 평가 질문 | 대표 metric | 놓치는 것 |
| --- | --- | --- | --- |
| visual | 영상이 자연스러운가 | FVD, human preference | dynamics correctness |
| physical event | 물리 위반이 있는가 | Physics benchmark | long-horizon state |
| state consistency | 내부 상태가 일관적인가 | probe, counterfactual test | policy utility |
| policy utility | 더 좋은 action을 선택하는가 | success, return | evaluator bias |
| deployment | 실제 시스템에서 안정적인가 | closed-loop success, latency | 환경 다양성 |

---

## 3. 권장 진행 순서

Chapter의 기간은 고정하지 않지만, 선수 관계 때문에 아래 순서는 유지하는 것을 권장한다.

```text
Ch.1 온보딩
  ↓
Ch.2 RT-1·RT-2 → OXE·OpenVLA → ACT·Mobile ALOHA
  ↓
Ch.3 DP·FM → DiT·FAST → 3D policy
  ↓
Ch.4 Continuous-action VLA
  ↓
Ch.5 π 계보
  ↓
Ch.6 데이터·스케일·embodiment
  ↓
Ch.7 JEPA·geometry              Ch.8 World model
          ↘                    ↙
             Ch.9 Reasoning
                    ↓
             Ch.10 Real-time
                    ↓
   Ch.11 Locomotion (선택, Ch.12와 병렬 가능)
                    ↓
             Ch.12 Tactile/Force
                    ↓
        Ch.13 Social WM (시야 확장, 1세션)
                    ↓
             Ch.14 Evaluation
```

- Ch.7과 Ch.8은 발표 인원과 관심사에 따라 순서를 바꾸거나 일부 병렬 운영할 수 있다. 다만 Ch.9 이전에 predictive representation과 world model의 차이를 정리해야 한다.
- Ch.11은 humanoid 관심도에 따라 축소·생략하거나 Ch.12와 병렬로 운영할 수 있다.
- Ch.13은 물리 세계 밖으로 시야를 넓히는 1세션 챕터로, 일정이 부족하면 Ch.14 이후로 미루거나 생략할 수 있다.

---

## 4. 운영용 Core / Extension 요약

### 최소 Core 경로

시간이 부족할 경우 다음 세션을 우선한다.

1. Physical AI 온보딩 (지형·용어·읽는 법)
2. RT-1 + RT-2
3. Open X-Embodiment + OpenVLA
4. ACT + Mobile ALOHA
5. Diffusion Policy + Flow Matching
6. DiT + FAST
7. CogACT + Octo
8. RDT-1B + Diffusion-VLA
9. π0 + π0.5
10. π*0.6 + Real-Time Chunking
11. Gemini Robotics 1.0 + 1.5
12. GR00T N1 + N1.5
13. LAPA + DreamGen
14. LeCun 발췌 + I-JEPA
15. V-JEPA 2.x
16. DUSt3R + VGGT 또는 3D/4D-VLA
17. World Models 발췌 + Dreamer 4
18. Genie + Genie 3
19. Cosmos 플랫폼 + Transfer
20. Cosmos-Predict2.5 + Cosmos 3
21. Ctrl-World + WorldVLA
22. Cosmos-Reason + Alpamayo-R1
23. ECoT + CoT-VLA
24. Fast-in-Slow + Hume
25. onboard latency 실습
26. VTLA + ForceVLA
27. 평가 지형 요약
28. 물리성 선호 학습 (PhyGDPO + PhysMaster)

### Extension 선택 원칙

다음 경우에 Extension을 추가한다.

- 연구실에서 실제 재현 또는 후속 연구를 고려하는 모델
- Core 간 비교표의 빈칸을 채우는 논문
- 새로운 sensor, action representation, evaluation protocol을 제안한 논문
- 최신 버전이 이전 Core의 결론을 실질적으로 뒤집은 경우
- Ch.11 locomotion은 humanoid·전신 제어에 대한 구성원 관심도에 따라 조정
- 연구실 스케일 세션(C05-S04, C06-S07, C14-S03)은 '우리 자원으로 가능한 연구'의 기준점이므로, Extension 중에서 우선 배정을 권장

단순 parameter 증가, benchmark 소폭 향상, 회사의 minor release는 10분 업데이트로 처리한다.

---

## 5. 발표 자료 공통 템플릿

### 1. 한 문장 위치

> 이 연구는 _______ 계보에서 _______ 문제를 해결하기 위해 _______를 바꾼 연구다.

여러 논문을 다루는 세션은 세션 전체에 대해 한 문장을 추가한다.

> 이 세션은 _______라는 질문에 대해 _______와 _______를 비교한다.

### 2. 시스템 명세

anchor 논문에 대해서만 완전하게 작성하고, 나머지 논문은 달라진 항목만 적는다.

| 항목 | 내용 |
| --- | --- |
| Robot / embodiment |  |
| Sensor |  |
| Observation |  |
| Action space |  |
| Action chunk |  |
| Control frequency |  |
| Model size / compute |  |
| Data source |  |
| Task / success definition |  |
| Code / weight / data |  |

### 3. 계보상의 세 질문

- 이전 접근은 어디에서 실패했는가?
- 무엇을 바꿨는가?
- 어떤 새로운 한계가 생겼는가?

### 4. 가장 강한 증거와 가장 약한 증거

- 저자의 주장을 가장 잘 지지하는 실험 1개
- 주장을 뒷받침하기에 부족한 실험 또는 누락된 대조군 1개

### 5. 재현·연구 가능성

- 공개 자원으로 재현 가능한 부분
- 우리 연구실에서 수정 가능한 부분
- 빅테크 자원이 없으면 검증하기 어려운 부분

---

## 6. 최신성 유지 규칙

Physical AI는 모델과 릴리스 주기가 빠르므로 다음 규칙을 적용한다.

1. 세션 논문을 정할 때, core 계보(문제의 기원을 이해하는 데 필요한 논문)가 아니면 2025-2026년 논문을 우선한다. 오래된 원리 논문은 발췌·배경 노트로 강등한다.
2. 발표 1주 전 arXiv 최신 버전, 공식 프로젝트 페이지, 코드 저장소를 확인한다.
3. 기존 Core를 새 논문으로 교체하려면 아래 중 하나를 만족해야 한다.
   - 새로운 action representation
   - 새로운 world model 기능
   - 새로운 data engine
   - 새로운 real-time architecture
   - 새로운 modality fusion
   - 기존 평가의 결론을 뒤집는 benchmark
4. 단순 SOTA 향상은 원 논문의 '후속 업데이트'로 1장에 정리한다.
5. 공식 블로그만 있고 방법·평가가 충분하지 않으면 Core 논문으로 지정하지 않는다.
6. 발표자는 사용한 논문 버전과 확인 날짜를 슬라이드에 기록한다.

---

## 7. 참고 자료의 역할

| 자료 유형 | 사용하는 목적 | 주의점 |
| --- | --- | --- |
| 원 논문 | 방법과 실험 근거 | 버전 확인 필요 |
| 서베이 | taxonomy와 용어 정렬 | 최신 논문 누락 가능 |
| 공식 블로그 | 제품·릴리스·데모 업데이트 | claim과 evidence 구분 |
| 코드·모델 카드 | 재현성과 실제 interface 확인 | 논문 설정과 다를 수 있음 |
| 기업 IR·제품 사양 | 하드웨어·compute 현실 제약 | 연구 성능 근거로 사용 금지 |
| 커뮤니티 정리 | 논문 발견과 탐색 | 핵심 근거로 직접 인용하지 않음 |

---

## 8. 초기 필독 자료

첫 Chapter에서 모든 자료를 정독하지 않는다. 아래 자료는 학기 중 필요할 때 반복해서 참조한다.

- *Aligning Cyber Space with Physical World: A Comprehensive Survey on Embodied AI* — 넓은 분야 지도
- *A Survey on Vision-Language-Action Models for Embodied AI* — VLA taxonomy와 living paper list
- 최신 *World Models for Embodied AI / Robot Learning* survey — world model 기능과 평가 지도
- RT-1, RT-2, Open X-Embodiment, OpenVLA — discrete-token VLA와 공개 데이터·가중치 계보
- ACT / ALOHA, Mobile ALOHA — action chunk와 저가 hardware data collection
- Diffusion Policy, Flow Matching, DiT, FAST — continuous action 도구와 tokenizer
- π0 계열 — flow-based generalist policy와 open-world·경험 학습·실시간 실행
- LeCun position paper 발췌, I-JEPA, V-JEPA 2.x — predictive representation 계보
- Dreamer, Genie, Cosmos 계보 — 서로 다른 world model 역할

---

## 9. 최종 체크리스트

스터디 종료 시 구성원은 다음 질문에 답할 수 있어야 한다.

- [ ] VLA 논문을 discrete/compressed token, diffusion head, flow head로 분류할 수 있다.
- [ ] action chunk 길이와 replanning frequency의 trade-off를 설명할 수 있다.
- [ ] Flow Matching(학습 목표), DiT(아키텍처), FAST(tokenizer)가 서로 다른 층위의 개념임을 설명할 수 있다.
- [ ] π0 → π0.5 → π*0.6의 발전을 '한계 시인 사슬'로 설명할 수 있다.
- [ ] pixel generation, latent prediction, explicit geometry의 전제를 비교할 수 있다.
- [ ] world model을 representation, simulator, data engine, reward/evaluator로 분해할 수 있다.
- [ ] reasoning trace의 매체와 latency·interpretability trade-off를 분석할 수 있다.
- [ ] manipulation VLA와 locomotion RL의 문제 설정 차이(학습 신호, 제어 주파수, sim의 역할)를 설명할 수 있다.
- [ ] 촉각과 힘 신호를 센서·주파수·융합 위치 기준으로 비교할 수 있다.
- [ ] social world model이 physical world model과 상태·전이·평가에서 어떻게 다른지 설명할 수 있다.
- [ ] 논문의 success rate를 hardware·task·control 조건 없이 직접 비교하지 않는다.
- [ ] 2025-2026년 논문 지형에서 다음에 검증해 볼 만한 연구 방향을 최소 하나 말할 수 있다.

---

## 부록 A. 주요 논문·자료 링크

> 링크와 버전은 발표 1주 전에 다시 확인한다. 아래 목록은 커리큘럼의 출발점이며, minor release를 모두 포함하는 고정 목록이 아니다.

### A.1 온보딩·서베이

- [Aligning Cyber Space with Physical World: A Comprehensive Survey on Embodied AI](https://arxiv.org/abs/2407.06886)
- [A Survey on Vision-Language-Action Models for Embodied AI](https://arxiv.org/abs/2405.14093)
- [A Comprehensive Survey on World Models for Embodied AI](https://arxiv.org/abs/2510.16732)
- [World Model for Robot Learning: A Comprehensive Survey](https://arxiv.org/abs/2605.00080)
- [Vision-Language-Action in Robotics: A Survey of Datasets, Benchmarks, and Data Engines](https://arxiv.org/abs/2604.23001)

### A.2 VLA 초기 계보와 action representation

- [RT-1: Robotics Transformer for Real-World Control at Scale](https://arxiv.org/abs/2212.06817)
- [RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control](https://arxiv.org/abs/2307.15818)
- [Open X-Embodiment: Robotic Learning Datasets and RT-X Models](https://arxiv.org/abs/2310.08864)
- [OpenVLA: An Open-Source Vision-Language-Action Model](https://arxiv.org/abs/2406.09246)
- [Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware (ACT/ALOHA)](https://arxiv.org/abs/2304.13705)
- [Mobile ALOHA: Learning Bimanual Mobile Manipulation with Low-Cost Whole-Body Teleoperation](https://arxiv.org/abs/2401.02117)
- [Diffusion Policy: Visuomotor Policy Learning via Action Diffusion](https://arxiv.org/abs/2303.04137)
- [Flow Matching for Generative Modeling](https://arxiv.org/abs/2210.02747)
- [Scalable Diffusion Models with Transformers (DiT)](https://arxiv.org/abs/2212.09748)
- [FAST: Efficient Action Tokenization for Vision-Language-Action Models](https://arxiv.org/abs/2501.09747)
- [3D Diffusion Policy](https://arxiv.org/abs/2403.03954)
- [Generalizable Humanoid Manipulation with 3D Diffusion Policies (iDP3)](https://arxiv.org/abs/2410.10803)

### A.3 Continuous-action VLA와 π 계보

- [CogACT](https://arxiv.org/abs/2411.19650)
- [Octo](https://arxiv.org/abs/2405.12213)
- [RDT-1B](https://arxiv.org/abs/2410.07864)
- [Diffusion-VLA](https://arxiv.org/abs/2412.03293)
- [OpenVLA-OFT: Fine-Tuning Vision-Language-Action Models](https://arxiv.org/abs/2502.19645)
- [SmolVLA](https://arxiv.org/abs/2506.01844)
- [π0](https://arxiv.org/abs/2410.24164)
- [π0.5](https://arxiv.org/abs/2504.16054)
- [π*0.6 / RECAP](https://arxiv.org/abs/2511.14759)
- [Real-Time Execution of Action Chunking Flow Policies](https://arxiv.org/abs/2506.07339)
- [Hi Robot: Open-Ended Instruction Following](https://arxiv.org/abs/2502.19417)
- [Knowledge Insulating Vision-Language-Action Models](https://arxiv.org/abs/2505.23705)

### A.4 데이터·스케일·Embodiment

- [Gemini Robotics](https://arxiv.org/abs/2503.20020)
- [Gemini Robotics 1.5](https://arxiv.org/abs/2510.03342)
- [GR00T N1](https://arxiv.org/abs/2503.14734)
- [GR00T N1.5 (모델 카드·기술 문서)](https://huggingface.co/nvidia/GR00T-N1.5-3B)
- [Latent Action Pretraining from Videos (LAPA)](https://arxiv.org/abs/2410.11758)
- [DreamGen](https://arxiv.org/abs/2505.12705)
- [MimicGen](https://arxiv.org/abs/2310.17596)
- [DexMimicGen](https://arxiv.org/abs/2410.24185)
- [DROID: A Large-Scale In-the-Wild Robot Manipulation Dataset](https://arxiv.org/abs/2403.12945)

### A.5 JEPA와 Geometry

- [A Path Towards Autonomous Machine Intelligence](https://openreview.net/forum?id=BZ5a1r-kVsf)
- [I-JEPA](https://arxiv.org/abs/2301.08243)
- [V-JEPA 2](https://arxiv.org/abs/2506.09985)
- [V-JEPA 2.1](https://arxiv.org/abs/2603.14482)
- [DUSt3R](https://arxiv.org/abs/2312.14132)
- [VGGT: Visual Geometry Grounded Transformer](https://arxiv.org/abs/2503.11651)
- [3D-VLA](https://arxiv.org/abs/2403.09631)
- [4D-VLA](https://arxiv.org/abs/2506.22242)
- [GEM-4D](https://arxiv.org/abs/2605.22882)

### A.6 World Model

- [World Models](https://arxiv.org/abs/1803.10122)
- [Dreamer 4](https://arxiv.org/abs/2509.24527)
- [Genie](https://arxiv.org/abs/2402.15391)
- [Genie 3 (공식 블로그)](https://deepmind.google/discover/blog/genie-3-a-new-frontier-for-world-models/)
- [Cosmos World Foundation Model Platform](https://arxiv.org/abs/2501.03575)
- [Cosmos-Transfer1](https://arxiv.org/abs/2503.14492)
- [Cosmos-Predict2.5: World Simulation with Video Foundation Models for Physical AI](https://arxiv.org/abs/2511.00062)
- [Cosmos 3: Omnimodal World Models for Physical AI](https://arxiv.org/abs/2606.02800)
- [Ctrl-World](https://arxiv.org/abs/2510.10125)
- [WorldVLA](https://arxiv.org/abs/2506.21539)
- [Evaluating Gemini Robotics Policies in a Veo World Simulator](https://arxiv.org/abs/2512.10675)
- [World-Gymnast](https://arxiv.org/abs/2602.02454)

### A.7 Reasoning과 실시간성

- [Cosmos-Reason1](https://arxiv.org/abs/2503.15558)
- [CoT-VLA](https://arxiv.org/abs/2503.22020)
- [ECoT](https://arxiv.org/abs/2407.08693)
- [ACoT-VLA](https://arxiv.org/abs/2601.11404)
- [DualCoT-VLA](https://arxiv.org/abs/2603.22280)
- [Alpamayo-R1](https://arxiv.org/abs/2511.00088)
- [Fast-in-Slow](https://arxiv.org/abs/2506.01953)
- [Hume](https://arxiv.org/abs/2505.21432)
- [Reactive Diffusion Policy](https://arxiv.org/abs/2503.02881)
- [Think Twice, Act Once](https://arxiv.org/abs/2505.21200)
- [DeeR-VLA](https://arxiv.org/abs/2411.02359)
- [TacMamba](https://arxiv.org/abs/2603.01700)

### A.8 Locomotion과 Whole-Body Control

- [Learning Perceptive Humanoid Locomotion over Challenging Terrain](https://arxiv.org/abs/2503.00692)
- [BeyondMimic: From Motion Tracking to Versatile Humanoid Control](https://arxiv.org/abs/2508.08241)
- [HOVER: Versatile Neural Whole-Body Controller for Humanoid Robots](https://arxiv.org/abs/2410.21229)
- [ASAP: Aligning Simulation and Real-World Physics](https://arxiv.org/abs/2502.01143)
- [TWIST: Teleoperated Whole-Body Imitation System](https://arxiv.org/abs/2505.02833)
- [TWIST2: Scalable, Portable, and Holistic Humanoid Data Collection System](https://arxiv.org/abs/2511.02832)
- 계보 배경: [HumanPlus](https://arxiv.org/abs/2406.10454), [OmniH2O](https://arxiv.org/abs/2406.08858), [ExBody2](https://arxiv.org/abs/2412.13196)

### A.9 촉각·힘

- [VTLA](https://arxiv.org/abs/2505.09577)
- [ForceVLA](https://arxiv.org/abs/2505.22159)
- [Tactile-VLA](https://arxiv.org/abs/2507.09160)
- [TacVLA](https://arxiv.org/abs/2603.12665)
- [TAP-VLA](https://arxiv.org/abs/2606.29089)
- [UniTacVLA](https://arxiv.org/abs/2606.31723)
- [Tactile-WAM](https://arxiv.org/abs/2606.26663)

### A.10 Social World Model

- [Social World Models](https://arxiv.org/abs/2509.00559)
- [Building Social World Models with Large Language Models](https://arxiv.org/abs/2606.11482)
- [Nemotron-Personas (Hugging Face collection)](https://huggingface.co/collections/nvidia/nemotron-personas)

### A.11 평가와 물리 일관성

- [Evaluating the World Model Implicit in a Generative Model](https://arxiv.org/abs/2406.03689)
- [Physics-IQ](https://arxiv.org/abs/2501.09038)
- [WorldModelBench](https://arxiv.org/abs/2502.20694)
- [PhyGDPO](https://arxiv.org/abs/2512.24551)
- [PhysMaster](https://arxiv.org/abs/2510.13809)
- [LIBERO](https://arxiv.org/abs/2306.03310)
- [SimplerEnv: Evaluating Real-World Robot Manipulation Policies in Simulation](https://arxiv.org/abs/2405.05941)

### A.12 연구실 스케일 연구 (2026)

- [Towards Long-Lived Robots: Continual Learning VLA Models via Reinforcement Fine-Tuning](https://arxiv.org/abs/2602.10503)
- [CapVector: Learning Transferable Capability Vectors in Parametric Space for VLA Models](https://arxiv.org/abs/2605.10903)
- [RealDexUMI: A Wearable Universal Manipulation Interface for Dexterous Robot Learning](https://arxiv.org/abs/2606.06033)
- [YOR: Your Own Mobile Manipulator for Generalizable Robotics](https://arxiv.org/abs/2602.11150)
- [MEVION: Low-Cost Open-Source Data Collection System for Dual-Arm Manipulation](https://arxiv.org/abs/2607.17970)
- [YUBI: Yielding Universal Bidigital Interface for Bimanual Dexterous Manipulation at Scale](https://arxiv.org/abs/2606.10244)
- [VLA-REPLICA: A Low-Cost, Reproducible Benchmark for Real-World Evaluation of VLA Models](https://arxiv.org/abs/2605.20774)
- [Benchmarking Vision-Language-Action Models on SO-101: Failure and Recovery Analysis](https://arxiv.org/abs/2606.08881)
- [VLM4VLA: Revisiting Vision-Language-Models in Vision-Language-Action Models](https://arxiv.org/abs/2601.03309)
- [Do World Action Models Generalize Better than VLAs? A Robustness Study](https://arxiv.org/abs/2603.22078)
