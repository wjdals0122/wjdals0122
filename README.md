<div align="center">

# 한 번 막힌 병목은 다시 손으로 풀지 않도록 구조로 바꿉니다

백엔드, 데이터 파이프라인, AI 에이전트 자동화를 만듭니다.

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)](#기술-스택)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)](#기술-스택)
[![Linux](https://img.shields.io/badge/Ubuntu-E95420?style=flat-square&logo=ubuntu&logoColor=white)](#기술-스택)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](#기술-스택)
[![n8n](https://img.shields.io/badge/n8n-EA4B71?style=flat-square&logo=n8n&logoColor=white)](#기술-스택)
[![Firebase](https://img.shields.io/badge/Firebase-DD2C00?style=flat-square&logo=firebase&logoColor=white)](#기술-스택)
[![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat-square&logo=pytorch&logoColor=white)](#기술-스택)

</div>

---

## 일하는 원칙

| 원칙 | 의미 | 프로젝트에서 한 일 |
|---|---|---|
| Think Systematically | 반복되거나 막히는 일은 다시 생기지 않게 구조로 만듭니다. | 사이트마다 우회하지 않고 국가와 키워드만 넣으면 끝나는 수집 파이프라인을 설계했습니다(global_VOC). |
| Dive Deep | 증상보다 원인을 고칩니다. | 크롤러가 없는 링크를 만들던 원인을 봇 차단 페이지에서 찾아 LLM이 링크를 지어내지 못하게 했습니다(global_VOC). |
| Earn Trust | 확인한 것만 말합니다. | 근거를 찾지 못하면 답을 지어내지 않는 에이전트를 만들었습니다(DART_Agent). |

---

## 프로젝트

### 1. Global VoC Research Agent

국가와 제품 키워드만 넣으면 현지 쇼핑몰, 커뮤니티, 유튜브의 소비자 의견을 모아 AI로 분류하고 DB에 쌓는 에이전트입니다.

[![repo](https://img.shields.io/badge/GitHub-gloabl__VOC__-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gloabl_VOC_)

<img src="https://raw.githubusercontent.com/wjdals0122/gloabl_VOC_/main/docs/images/demo.gif" alt="Global VoC 대시보드 사용 흐름" width="100%">

배경
- LG전자 트롬 워시콤보의 폴란드 런칭 전략을 세우던 중 국내 IP가 막혀 현지 VoC를 10%도 모으지 못했습니다.

맡은 일
- 기획, n8n 백엔드 워크플로우, API 설계, 대시보드를 맡았습니다.

구현
- n8n을 Docker로 직접 띄우고 마스터 워크플로우가 채널별 하위 워크플로우를 호출하도록 나눴습니다.
- 한 번에 모든 단계를 처리하던 API를 출처 탐색과 선택 수집 두 단계로 나누고 규격서와 Mock 서버를 만들었습니다.
- 재귀 크롤러에 깊이 상한과 방문 기록을 두어 탐색이 반드시 끝나게 했습니다.
- 봇 차단 페이지를 먼저 걸러 내고 수집에 실패하면 재시도한 뒤 다른 경로로 들어갑니다.
- 링크는 코드가 뽑고 LLM은 번호만 고르게 해서 없는 링크를 만들지 못하게 했습니다.
- 채널마다 다른 데이터를 하나의 VoC 스키마로 맞춰 Supabase에 저장합니다.
- 검색어 생성, 페이지 판정, 분석에는 GPT-4o-mini를 쓰고 자막 없는 영상 분석에는 Gemini Flash를 씁니다.

성과
- VoC 확보 기간을 약 2주에서 약 10분으로 줄였습니다.

기술
- n8n, Docker, OpenAI, Gemini, ScrapingAnt, Apify, Supabase, JavaScript

---

### 2. DART 공시 질의응답 Agent

DART 공시 4,204건에서 자연어 질문에 근거와 함께 답하는 RAG 에이전트입니다. 제10회 미래에셋증권 AI Festival에 출품했습니다.

[![repo](https://img.shields.io/badge/GitHub-DART__Agent-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/DART_Agent)

맡은 일
- 팀 프로젝트에서 RAG 검색기 위에 올리는 에이전트 모듈(`src/agent/`)을 설계하고 구현했습니다.

구현
- HyperCLOVA X function calling으로 에이전트 루프와 검색 도구를 만들었습니다.
- 회사명, 연도, "작년" 같은 상대 시점을 도구 인자로 바꾸는 질문 재작성 단계를 넣었습니다.
- 시간 제한과 비용 상한을 두고 무한 루프를 막았습니다.
- 모델이나 도구가 실패해도 항상 정해진 JSON 형식으로 응답합니다.
- 근거를 찾지 못하면 답을 지어내지 않고 확인되지 않았다고 응답합니다.
- 검색 결과를 모델에 넣을 때 프롬프트 인젝션을 막았습니다.

서비스 환경
- Ubuntu 24.04 서버의 Python API 서버(uvicorn, systemd)가 에이전트를 호출합니다.
- 주최 측이 이 API를 직접 호출해 채점했습니다.

데이터 규모
- 70개사의 공시 4,204건을 614,578개 청크로 나누고 BM25와 bge-m3 하이브리드 검색으로 찾습니다.

기술
- Python, HyperCLOVA X, bge-m3, BM25, uvicorn, systemd, Ubuntu

---

### 3. FIND.INC, AI 에이전트가 운영하는 마케팅 전략 시스템

마케팅 에이전시의 전략 업무를 Codex, Claude Code, Gemini 같은 AI 코딩 에이전트가 하도록 규칙과 스킬, 자동화를 설계한 저장소입니다.

[![repo](https://img.shields.io/badge/GitHub-find__inc-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/find_inc)
[![dashboard](https://img.shields.io/badge/Dashboard-find--inc.web.app-FFCA28?style=flat-square&logo=firebase&logoColor=black)](https://find-inc.web.app/)

맡은 일
- 설계부터 구현까지 혼자 했습니다.

구현
- `AGENTS.md` 하나를 규칙의 기준으로 두고 `CLAUDE.md`와 `GEMINI.md`가 이를 불러오게 해서 어떤 도구를 써도 같은 규칙을 따르게 했습니다.
- 브랜드, 상품, 경쟁사, 고객 조사부터 마케팅 브리프와 대시보드까지 업무 스킬 12종을 만들었습니다.
- 산출물을 검증하고 대상 파일만 커밋해 push한 뒤 원격 반영을 확인하고 카카오톡, Slack, 이메일로 알립니다.
- 강제 push를 금지하고 인증 정보는 허용한 호스트로만 보내며 결정 이유를 지어내지 않도록 규칙을 정했습니다.
- 전략 대시보드를 Firebase Hosting에 배포하고 Firebase Authentication으로 로그인과 2단계 회원가입을 붙였습니다.
- Playwright로 인증 흐름을 E2E 테스트하고 알림 스크립트에 단위 테스트 8개를 작성했습니다.

협업 흐름
- GitHub Issue를 작업 브랜치에서 처리해 PR로 합쳤고 결정 13건을 기록으로 남겼습니다.

기술
- Codex, Claude Code, Gemini, Python, Firebase, Playwright

---

### 4. 건설용 자갈 암석 이미지 분류

자갈 사진 38만 장을 7개 암석 클래스로 분류하는 이미지 모델입니다. DACON 경진대회에 참가했습니다.

[![repo](https://img.shields.io/badge/GitHub-gravel--rock--classifier-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gravel-rock-classifier)

맡은 일
- 설계부터 구현까지 혼자 했습니다.

구현
- Ubuntu 24.04와 CUDA 환경에서 ConvNeXt-Base를 혼합 정밀도로 학습했습니다.
- 5폴드 교차 검증과 TTA 앙상블로 제출 파일을 만들었습니다.
- 환경 설정, 학습, 단일 이미지 추론, 폴드별 평가를 Bash 스크립트 하나(`scripts/rock.sh`)로 묶었습니다.
- 정확도, macro F1, 클래스별 지표, 혼동행렬로 모델을 평가했습니다.

데이터 규모
- 학습 이미지 380,020장과 테스트 이미지 95,006장을 다뤘습니다.

기술
- Python, PyTorch, CUDA, timm, Albumentations, OpenCV, Bash, Ubuntu

---

## 역량별 프로젝트

| 역량 | 관련 프로젝트 |
|---|---|
| 리눅스 환경 개발과 운영 | DART_Agent, gravel-rock-classifier |
| Python 서버와 백엔드 로직 | DART_Agent |
| REST API 설계 | global_VOC |
| Docker | global_VOC |
| 클라우드 서비스 배포와 연동 | global_VOC, DART_Agent, find_inc |
| 대용량 데이터 처리 | global_VOC, DART_Agent, gravel-rock-classifier |
| Git 협업(브랜치, PR) | DART_Agent, find_inc |
| AI 코딩 도구로 개발과 운영 자동화 | global_VOC, find_inc |
| 테스트 | global_VOC, find_inc |
| 문서화 | global_VOC, DART_Agent, find_inc, gravel-rock-classifier |

---

## 기술 스택

| 분야 | 기술 |
|---|---|
| 언어 | Python, JavaScript, Bash |
| 백엔드와 워크플로우 | n8n, uvicorn, REST API, Webhook |
| 인프라 | Ubuntu, Docker, Docker Compose, systemd, Firebase Hosting |
| 데이터 | Supabase(PostgreSQL), BM25, bge-m3 |
| AI | OpenAI GPT, Google Gemini, HyperCLOVA X, PyTorch, timm |
| 수집 | ScrapingAnt, Apify, Bright Data |
| 테스트 | Playwright, unittest |
| 협업과 도구 | Git, GitHub, Claude Code, Codex, Gemini, Google Antigravity |
