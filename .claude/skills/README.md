# 학습 워크플로우 (자격증 대비)

공식 문서 이해 → 카드화 → 간격반복 암기 → 약점 보강의 4단계 파이프라인.
Claude 스킬(저장소 내)과 외부 툴(NotebookLM·Anki)을 함께 사용한다.

```
[1] 이해            [2] 카드화                 [3] 암기          [4] 보강
NotebookLM   →   flashcard-generator   →    Anki        →   study-coach
(공식 문서)        or anki-import (스킬)      (간격반복)        (Learning Mode)
```

## 단계별 사용법

### 1. 이해 — NotebookLM (외부)
- AWS/CNCF **공식 시험가이드·문서 PDF**를 NotebookLM에 업로드.
- 본인이 올린 소스 기반으로만 요약·Q&A → 환각 적게 개념을 먼저 잡는다.

### 2. 카드화 — `flashcard-generator` 또는 `anki-import` (스킬)
- 정리된 노트/주제가 있으면 → **`flashcard-generator`**
  - 예: `AWS MLA-C01 모델 배포 도메인 정리한 노트로 카드 만들어줘`
- URL·문서에서 출처 포함 덱이 필요하면 → **`anki-import`**
  - 예: `이 시험가이드 링크로 출처 포함 Anki 덱 만들어줘`
- 출력은 `study/<topic>.tsv` (Anki 임포트용, Tab 구분).

### 3. 암기 — Anki (외부)
- Anki → File → Import → `.tsv` 선택, 구분자 Tab, 컬럼 매핑(Front/Back/Source/Tags).
- FSRS/SM-2 간격반복으로 매일 복습.

### 4. 보강 — `study-coach` (스킬)
- Anki에서 자주 틀리는 카드 = 약점 개념.
- 예: `CKA 네트워킹에서 자꾸 틀려, 같이 공부하자` → 소크라테스식 가이드 + 모의문제.
- 세션 끝에 약점만 다시 `flashcard-generator`로 카드화 → 루프 반복.

## 대상 자격증 (권장 순서)
1. AWS Certified AI Practitioner (AIF-C01)
2. AWS Certified Machine Learning Engineer – Associate (MLA-C01)
3. CKA (Certified Kubernetes Administrator)
4. HashiCorp Terraform Associate

## 산출물 위치
- 생성된 덱: `study/*.tsv` (git 추적, 어느 기기에서나 재사용)
