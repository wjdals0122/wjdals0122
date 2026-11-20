<div align="center">

# 강정민

백엔드, 데이터 파이프라인, AI 에이전트 자동화

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

| 원칙 | 의미 | 프로젝트 사례 |
|---|---|---|
| Think Systematically | 반복되거나 막히는 일은 구조로 해결 | 사이트별 우회 대신 국가와 키워드만으로 끝나는 수집 파이프라인 설계 (global_VOC) |
| Dive Deep | 증상보다 원인을 수정 | 크롤러의 가짜 링크 원인을 봇 차단 페이지에서 찾아 LLM의 링크 생성 차단 (global_VOC) |
| Earn Trust | 확인한 것만 보고 | 근거가 없으면 답을 지어내지 않는 에이전트 구현 (DART_Agent) |

---

## 프로젝트

### 1. Global VoC Research Agent

국가와 제품 키워드 입력만으로 현지 쇼핑몰, 커뮤니티, 유튜브의 소비자 의견을 수집하고 AI로 분류해 DB에 저장하는 에이전트

[![repo](https://img.shields.io/badge/GitHub-gloabl__VOC__-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gloabl_VOC_)

<img src="https://raw.githubusercontent.com/wjdals0122/gloabl_VOC_/main/docs/images/demo.gif" alt="Global VoC 대시보드 사용 흐름" width="100%">

배경
- LG전자 트롬 워시콤보 폴란드 런칭 전략 수립 과정
- 국내 IP 차단으로 현지 VoC 수집률 10% 미만

담당
- 기획, n8n 백엔드 워크플로우, API 설계, 대시보드

구현
- n8n Docker self-hosting, 마스터 워크플로우와 채널별 하위 워크플로우 분리
- 단일 일괄 처리 API를 출처 탐색과 선택 수집 2단계 REST API로 분리, 규격서와 Mock 서버 제공
- 재귀 크롤러의 깊이 상한과 방문 기록으로 탐색 종료 보장
- 봇 차단 페이지 사전 감지, 실패 시 재시도와 대체 경로
- 링크는 코드가 추출하고 LLM은 번호만 선택하는 구조로 가짜 링크 차단
- 채널별 데이터를 공통 VoC 스키마로 정규화해 Supabase 적재
- 용도별 모델 선택: GPT-4o-mini(검색어 생성, 페이지 판정, 분석), Gemini Flash(자막 없는 영상 분석)

성과
- VoC 확보 기간 약 2주에서 약 10분으로 단축

기술
- n8n, Docker, OpenAI, Gemini, ScrapingAnt, Apify, Supabase, JavaScript

---

### 2. DART 공시 질의응답 Agent

DART 공시 4,204건 기반으로 자연어 질문에 근거와 함께 답하는 RAG 에이전트, 제10회 미래에셋증권 AI Festival 출품작

[![repo](https://img.shields.io/badge/GitHub-DART__Agent-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/DART_Agent)

담당
- 팀 프로젝트 중 RAG 검색기 위의 에이전트 모듈(`src/agent/`) 설계와 구현

구현
- HyperCLOVA X function calling 기반 에이전트 루프와 검색 도구 정의
- 회사명, 연도, "작년" 같은 상대 시점을 도구 인자로 바꾸는 질문 재작성 단계
- 시간 제한과 비용 상한, 무한 루프 방지
- 모델이나 도구 실패 시에도 정해진 JSON 형식으로 응답
- 근거가 없으면 답을 지어내지 않고 확인되지 않음으로 응답
- 검색 결과를 모델에 넣을 때 프롬프트 인젝션 차단

서비스 환경
- Ubuntu 24.04 서버의 Python API 서버(uvicorn, systemd)에서 에이전트 호출
- 주최 측의 API 직접 호출 방식으로 채점

데이터 규모
- 70개사, 공시 4,204건, 614,578개 청크
- BM25와 bge-m3 하이브리드 검색

기술
- Python, HyperCLOVA X, bge-m3, BM25, uvicorn, systemd, Ubuntu

---

### 3. FIND.INC, AI 에이전트가 운영하는 마케팅 전략 시스템

마케팅 에이전시의 전략 업무를 AI 코딩 에이전트(Codex, Claude Code, Gemini)가 수행하도록 규칙, 스킬, 자동화를 설계한 저장소

[![repo](https://img.shields.io/badge/GitHub-find__inc-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/find_inc)
[![dashboard](https://img.shields.io/badge/Dashboard-find--inc.web.app-FFCA28?style=flat-square&logo=firebase&logoColor=black)](https://find-inc.web.app/)

담당
- 전체 설계와 구현

구현
- `AGENTS.md`를 규칙의 단일 기준으로 두고 `CLAUDE.md`와 `GEMINI.md`에서 불러오는 구조
- 브랜드, 상품, 경쟁사, 고객 조사부터 마케팅 브리프와 대시보드까지 업무 스킬 12종
- 산출물 검증, 대상 파일만 커밋, push, 원격 반영 확인, 카카오톡과 Slack과 이메일 알림까지 자동화
- 강제 push 금지, 허용한 호스트로만 인증 정보 전달, 결정 이유 날조 금지 규칙
- 전략 대시보드 Firebase Hosting 배포, Firebase Authentication 로그인과 2단계 회원가입
- Playwright 인증 흐름 E2E 테스트, 알림 스크립트 단위 테스트 8개

협업 흐름
- GitHub Issue, 작업 브랜치, PR 순서로 처리
- 결정 기록 13건

기술
- Codex, Claude Code, Gemini, Python, Firebase, Playwright

---

### 4. 건설용 자갈 암석 이미지 분류

자갈 사진 38만 장을 7개 암석 클래스로 분류하는 이미지 모델, DACON 경진대회 참가작

[![repo](https://img.shields.io/badge/GitHub-gravel--rock--classifier-181717?style=flat-square&logo=github)](https://github.com/wjdals0122/gravel-rock-classifier)

담당
- 전체 설계와 구현

구현
- Ubuntu 24.04와 CUDA 환경에서 ConvNeXt-Base 혼합 정밀도 학습
- 5폴드 교차 검증과 TTA 앙상블로 제출 파일 생성
- 환경 설정, 학습, 단일 이미지 추론, 폴드별 평가를 Bash 스크립트 하나(`scripts/rock.sh`)로 통합
- 정확도, macro F1, 클래스별 지표, 혼동행렬로 평가

데이터 규모
- 학습 이미지 380,020장, 테스트 이미지 95,006장

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
