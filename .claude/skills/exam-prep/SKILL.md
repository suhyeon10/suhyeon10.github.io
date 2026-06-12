---
name: exam-prep
description: End-to-end exam/certification prep coordinator. Use this when the user says they need to study for or prepare for an exam/certification (e.g. "시험 준비하자", "AWS 자격증 공부 도와줘", "prep me for CKA") without specifying steps. It drives the full pipeline — plan → understand → flashcards → quiz → reinforce weak areas — and tracks progress, delegating to flashcard-generator, anki-import, and study-coach as needed.
---

# Exam Prep (오케스트레이터)

사용자가 "시험 준비"만 말해도 **전체 학습 루프를 알아서 운영**한다.
세부 단계를 일일이 시키지 않게, 이 스킬이 진입점이 되어 다른 학습 스킬을 호출한다.

## When to use
- "시험 준비하자 / 공부 도와줘 / 자격증 따야 돼", "prep me for <cert>"
- 어떤 단계부터 할지 사용자가 안 정했을 때 (이 스킬이 정한다)

## 운영 원칙
- **알아서 진행**한다. 매 단계 허락을 구하지 말고, 합리적 기본값으로 다음 단계를 이어간다.
  단, 자격증 선택·시험 날짜처럼 사람만 아는 정보는 한 번 묻는다.
- 진행 상태를 `study/<cert>/progress.md`에 기록해 세션이 바뀌어도 이어서 한다.
- 생성물(덱·계획)은 모두 `study/<cert>/` 아래에 저장해 git으로 추적한다.

## 흐름 (자동 실행)

### 0. 셋업 (한 번만 질문)
모르면 묻는다: **어느 자격증인가? 목표 시험일/주당 학습시간은?**
권장 우선순위 기본값: AIF-C01 → MLA-C01 → CKA → Terraform Associate.

### 1. 계획 — `study/<cert>/plan.md` 생성
- 공식 시험가이드의 **도메인별 비중**으로 주차별 계획을 짠다.
- 각 도메인에 4단계(이해→카드→퀴즈→보강) 체크박스를 단다.

### 2. 이해
- 도메인별 핵심 개념을 요약. 깊은 이해가 필요하면 **study-coach** 모드로 전환.
- (외부) 공식 문서·NotebookLM 활용을 안내.

### 3. 카드화 — flashcard-generator / anki-import 로직 적용
- 도메인별로 `study/<cert>/<domain>.tsv` 덱 생성 (Anki 임포트용).
- 사용자가 URL/문서를 주면 anki-import 방식(출처 포함)으로.

### 4. 퀴즈 & 채점 — study-coach 의 quiz 모드
- 도메인 덱에서 문제 출제 → 채점 → 틀린 항목 기록.

### 5. 약점 보강 (루프)
- 틀린 개념을 study-coach 로 다시 가르치고, 약점만 추가 카드로 만들어 루프.
- 도메인 4단계가 다 끝나면 `progress.md` 체크 → 다음 도메인으로.

### 6. 마무리
- 전 도메인 완료 시 **모의시험**(도메인 비중대로 출제) → 점수·약점 리포트.

## progress.md 형식 (예)
```
# <cert> 준비 현황
목표 시험일: 2026-09-01 · 주당: 6h

## 도메인 진행
- [x] Data Prep        이해 / 카드 / 퀴즈 / 보강
- [~] Modeling         이해 / 카드 / [ ]퀴즈 / [ ]보강
- [ ] Deployment
- [ ] Monitoring & Security

## 약점 노트
- SageMaker 배포 옵션 구분 (자주 틀림)
```

## Guardrails
- 사실은 공식 시험가이드 기준. 불확실하면 추측하지 말고 출처 확인을 권한다.
- 사용자가 특정 단계만 원하면(예: "카드만") 그 단계만 수행한다.
- 큰 비용/외부 작업(실습 환경 비용 등)은 진행 전에 알린다.
