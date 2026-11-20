<div align="center">

# 한 번 막힌 병목을 다시 손으로 풀지 않도록, 구조로 바꾸는 개발자

**백엔드 · 데이터 파이프라인 · AI 에이전트 자동화**

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

| 원칙 | 의미 | 프로젝트에서 드러난 방식 |
|---|---|---|
| **Think Systematically** | 반복되거나 막히는 지점은 다시 일어나지 않도록 구조로 만든다 | 사이트별 우회 대신 국가·키워드만 넣으면 끝나는 수집 파이프라인 설계 (global_VOC)<br>세션마다 반복하던 지시를 저장소 규칙과 스킬로 고정 (find_inc) |
| **Dive Deep** | 증상이 아니라 원인을 고친다 | 크롤러가 없는 링크를 만들던 원인을 봇 차단 페이지까지 추적하고, LLM이 링크를 지어낼 경로 자체를 제거 (global_VOC) |
| **Earn Trust** | 확인한 것만 말한다 | 근거를 찾지 못하면 답을 지어내지 않는 에이전트 (DART_Agent)<br>가짜 진행률 없이 실제 상태만 표시, 응답에 없는 값은 "미확인" (global_VOC) |

---

## 프로젝트

### 1. Global VoC Research Agent
> 국가와 제품 키워드만 넣으면 현지 쇼핑몰·커뮤니티·유튜브의 소비자 의견을 수집하고, AI로 분류해 DB에 쌓는 에이전트

[![repo](https://img.shields.io/badge/GitHub-gloabl__VOC__-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gloabl_VOC_)

<img src="https://raw.githubusercontent.com/wjdals0122/gloabl_VOC_/main/docs/images/demo.gif" alt="Global VoC 대시보드 사용 흐름" width="100%">

- **배경**: LG전자 트롬 워시콤보 폴란드 런칭 전략 중, 국내 IP 차단으로 현지 VoC 수집이 10% 미만에 그침
- **역할**: 기획, n8n 백엔드 워크플로우, API 설계, 대시보드
- **핵심 구현**
  - n8n 워크플로우를 Docker로 self-hosting, 마스터 워크플로우가 채널별 하위 워크플로우를 호출하는 구조
  - 단일 일괄 처리 API를 **출처 탐색 / 선택 수집** 2단계 REST API로 분리하고 규격서와 Mock 서버 제공
  - 재귀 크롤러의 종료 보장(깊이 상한·방문 기록), 봇 차단 감지, 재시도와 대체 경로
  - 링크는 코드가 추출하고 LLM은 번호만 고르게 해서 환각 차단
  - 채널이 달라도 하나의 VoC 스키마로 정규화해 Supabase에 적재
  - 용도별 모델 선택: GPT-4o-mini(검색어 생성·페이지 판정·분석), Gemini Flash(자막 없는 영상 분석)
- **성과**: VoC 확보 기간 약 2주 → 약 10분
- **기술**: n8n, Docker, OpenAI, Gemini, ScrapingAnt, Apify, Supabase, JavaScript

---

### 2. DART 공시 질의응답 Agent
> DART 공시 4,204건에서 자연어 질문에 근거와 함께 답하는 RAG 에이전트 · 제10회 미래에셋증권 AI Festival

[![repo](https://img.shields.io/badge/GitHub-DART__Agent-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/DART_Agent)

- **구성**: 팀 프로젝트
- **역할**: RAG 검색기 위에 올린 **에이전트 모듈** 설계·구현 (`src/agent/`)
- **핵심 구현**
  - HyperCLOVA X function calling 기반 에이전트 루프와 검색 도구 정의
  - 질문 재작성 단계: 회사명·연도·"작년" 같은 상대 시점을 도구 인자로 변환
  - 시간 제한과 비용 상한, 무한 루프 방지
  - 모델이나 도구가 실패해도 항상 정해진 JSON 형식으로 응답
  - 근거를 찾지 못하면 답을 지어내지 않고 확인되지 않았다고 응답
  - 검색 결과를 컨텍스트로 넣을 때 프롬프트 인젝션 차단
- **서비스 환경**: Ubuntu 24.04 서버의 Python API 서버(uvicorn, systemd)가 에이전트를 호출하고, 주최 측이 API를 직접 호출해 채점
- **데이터 규모**: 70개사 · 4,204문서 · 614,578청크, BM25 + bge-m3 하이브리드 검색
- **기술**: Python, HyperCLOVA X, bge-m3, BM25, uvicorn, systemd, Ubuntu

---

### 3. FIND.INC — AI 에이전트가 운영하는 마케팅 전략 시스템
> 마케팅 에이전시의 전략 업무를 AI 코딩 에이전트(Codex · Claude Code · Gemini)가 수행하도록 규칙·스킬·자동화를 설계한 저장소

[![repo](https://img.shields.io/badge/GitHub-find__inc-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/find_inc)
[![dashboard](https://img.shields.io/badge/Dashboard-find--inc.web.app-FFCA28?style=flat-square&logo=firebase&logoColor=black)](https://find-inc.web.app/)

- **역할**: 전체 설계·구현
- **핵심 구현**
  - `AGENTS.md`를 규칙의 단일 기준으로 두고 `CLAUDE.md`·`GEMINI.md`가 이를 불러오게 해서 도구가 달라도 같은 규칙 적용
  - 브랜드·상품·경쟁사·고객 조사부터 마케팅 브리프, 대시보드까지 업무 스킬 12종
  - 산출물 검증 → 대상 파일만 커밋 → push → 원격 반영 확인 → 카카오톡·Slack·이메일 알림 자동화
  - 강제 push 금지, 시크릿을 허용된 호스트로만 전달, 결정 이유를 지어내지 않는 기록 규칙
  - 전략 대시보드를 Firebase Hosting으로 배포하고 Firebase Authentication 로그인·2단계 회원가입 연결
  - Playwright 인증 E2E 테스트, 알림 스크립트 단위 테스트 8종
- **협업 흐름**: GitHub Issue → 작업 브랜치 → PR, 결정 기록 13건
- **기술**: Codex, Claude Code, Gemini, Python, Firebase, Playwright

---

### 4. 건설용 자갈 암석 이미지 분류
> 자갈 사진 38만 장을 7개 암석 클래스로 분류하는 이미지 모델 · DACON 경진대회

[![repo](https://img.shields.io/badge/GitHub-gravel--rock--classifier-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gravel-rock-classifier)

- **역할**: 전체 설계·구현
- **핵심 구현**
  - Ubuntu 24.04 + CUDA 환경에서 ConvNeXt-Base 학습 (혼합 정밀도)
  - 5폴드 교차 검증과 TTA 앙상블로 제출 파일 생성
  - 환경 설정, 학습, 단일 이미지 추론, 폴드별 평가를 Bash 스크립트 하나로 통합 (`scripts/rock.sh`)
  - 정확도, macro F1, 클래스별 지표, 혼동행렬로 평가
- **데이터 규모**: 학습 380,020장, 테스트 95,006장
- **기술**: Python, PyTorch, CUDA, timm, Albumentations, OpenCV, Bash, Ubuntu

---

## 역량별 프로젝트

| 역량 | global_VOC | DART_Agent | find_inc | gravel |
|---|:---:|:---:|:---:|:---:|
| 리눅스 환경 개발·운영 | | ✅ | | ✅ |
| Python 서버·백엔드 로직 | | ✅ | | |
| REST API 설계 | ✅ | | | |
| Docker | ✅ | | | |
| 클라우드 서비스 배포·연동 | ✅ | ✅ | ✅ | |
| 대용량 데이터 처리 | ✅ | ✅ | | ✅ |
| Git 협업 (브랜치·PR) | | ✅ | ✅ | |
| AI 코딩 도구로 개발·운영 자동화 | ✅ | | ✅ | |
| 테스트 | ✅ | | ✅ | |
| 문서화 | ✅ | ✅ | ✅ | ✅ |

---

## 기술 스택

| 영역 | 기술 |
|---|---|
| 언어 | Python, JavaScript, Bash |
| 백엔드·워크플로우 | n8n, uvicorn, REST API, Webhook |
| 인프라 | Ubuntu, Docker, Docker Compose, systemd, Firebase Hosting |
| 데이터 | Supabase(PostgreSQL), BM25, bge-m3 |
| AI | OpenAI GPT, Google Gemini, HyperCLOVA X, PyTorch, timm |
| 수집 | ScrapingAnt, Apify, Bright Data |
| 테스트 | Playwright, unittest |
| 협업·도구 | Git, GitHub, Claude Code, Codex, Gemini, Google Antigravity |
