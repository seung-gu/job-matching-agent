# 10/20 회의 안건

[메인](README.md) · [BE](BE.md) · [LLM](LLM.md) · [FE](FE.md) · [DevOps](DevOps.md) · [Test](Test.md)

2026-10-15 · 승구

## 전체 구조

*전체 구조, 라벨은 회의에서 정할 안건의 페이지* <sub>(파랑 확정 · 주황 미정(라벨 = 안건 페이지) · 주황 점선 추후(PoC 이후) · 흰색 외부 서비스)</sub>

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart TB
  FE["[FE] FE 화면<br/>프레임워크 미정"]:::todo
  API["API 서버<br/>FastAPI"]:::done
  RUN["[BE] 실행 방식<br/>PoC: API 안에서 비동기 실행"]:::todo
  WR["worker + Redis<br/>(추후)"]:::later
  PORT["[LLM] 포트 match(): 구현 3개 비교<br/>범위는 회의에서 (일 단위 · 역할 단위)"]:::todo
  I1["직접 구현<br/>SDK + 코드"]:::done
  I2["pydantic-ai<br/>타입 기반 agent"]:::done
  I3["LangGraph<br/>그래프 워크플로"]:::done
  MODEL["[LLM] 모델: LiteLLM 프록시<br/>쓸지, 키는 개인 · 공용"]:::todo
  TOOL["[LLM] 도구: MCP 서버<br/>둘지"]:::todo
  PROV["Claude · OpenAI 등"]:::ext
  SITE["공고 사이트 · API"]:::ext
  DB["[DevOps] DB"]:::todo
  FE -->|요청| API
  API -->|매칭 결과| FE
  API --> RUN
  RUN -.-> WR
  RUN --> PORT
  PORT -->|매칭 결과| API
  PORT --> I1 & I2 & I3
  I1 & I2 & I3 --> MODEL
  I1 & I2 & I3 --> TOOL
  MODEL --> PROV
  TOOL --> SITE
  TOOL --> DB

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef later fill:#fffbf2,stroke:#f0a800,stroke-width:2px,stroke-dasharray:6 4,color:#1f1f1f
classDef ext fill:#ffffff,stroke:#b5b5ad,color:#1f1f1f
classDef code fill:#ececea,stroke:#d6d6d2,color:#1f1f1f
classDef fake fill:#ffffff,stroke:#b5b5ad,stroke-dasharray:5 4,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

모든 그림은 같은 규칙을 따름. 파랑은 확정, 주황은 회의에서 정할 것(라벨 = 안건이 있는 페이지), 주황 점선은 추후(PoC 이후). 회의에서 정해지면 주황을 파랑으로 바꿈.

회의에서 이 페이지의 공통 안건(1\~4)을 먼저 정하고, 이어서 BE · LLM · FE · DevOps · Test 페이지 순서로 정함.

## 1. 첫 구현 범위

*사이트 목록을 먼저 정하면, 업로드부터 평가까지 한 번에 이어짐* <sub>(파랑 확정(4\~6은 자동 파이프라인) · 주황 점선 추후(PoC 이후))</sub>

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart TB
  subgraph USER["사용자 (FE 화면)"]
    S1["1단계 · 검색할 사이트 목록 설정<br/>미리 등록 · 사이트·API"]:::done
    S2["2단계 · 이력서 업로드 (추후)<br/>PDF · Word 파일 선택"]:::later
    S3["3단계 · 대화로 정보 보충 (추후)<br/>채팅 화면에서 질문에 답함"]:::later
    POC["PoC: 내 이력 직접 등록<br/>2·3단계 대신 미리 입력"]:::done
    S7["7단계 · 결과 확인<br/>순위별 공고 목록 + 근거"]:::done
  end
  subgraph SERVER["서버 (BE)"]
    PARSE["이력서 파싱 (추후)<br/>파일 → 이력 데이터"]:::later
    CHAT["대화 agent (추후)<br/>질문 생성 · 답을 이력에 반영"]:::later
    S4["4단계 · 검색 & 추출<br/>공고 목록 → JD 정리"]:::done
    S5["5단계 · 비교 & 매칭<br/>요건별 판정 + 근거 이력"]:::done
    S6["6단계 · 평가<br/>적합도 점수 · 순위"]:::done
  end
  S1 -.-> S2 -.-> S3
  S1 -->|PoC| POC
  S2 -.->|파일| PARSE
  S3 <-.->|대화| CHAT
  CHAT -.-> S4
  POC -->|실행| S4
  S4 --> S5 --> S6
  S6 -->|결과| S7

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef later fill:#fffbf2,stroke:#f0a800,stroke-width:2px,stroke-dasharray:6 4,color:#1f1f1f
classDef ext fill:#ffffff,stroke:#b5b5ad,color:#1f1f1f
classDef code fill:#ececea,stroke:#d6d6d2,color:#1f1f1f
classDef fake fill:#ffffff,stroke:#b5b5ad,stroke-dasharray:5 4,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

- 흐름: 검색할 사이트 목록을 먼저 정해 두면, 이력서 업로드 → 대화 보충 → 검색·추출 → 매칭 → 평가가 중간에 멈추지 않고 한 번에 이어짐. 결과는 다시 화면으로 돌아감
- PoC 범위: 1단계(사이트 목록 설정) 후, 미리 등록해 둔 내 이력으로 4\~7단계를 돌림
- 추후: 2단계 이력서 업로드·파싱, 3단계 대화 보충. 파싱은 Markdown 변환(markitdown 등) + LLM 구조화가 후보
- 용어: 사용자 단계까지 포함한 전체는 워크플로, 그 안에서 사람 개입 없이 이어지는 4\~6단계는 파이프라인
- 제외: 구인 사이트 알림 메일(Gmail) 수집
- 남은 질문: 내 이력을 어떤 형식으로 등록할지(JSON 파일, 입력 폼), 사이트 목록을 누가 어떻게 등록할지

## 2. 스키마

모듈 사이를 오가는 데이터는 JD, 이력, 매칭 결과 세 가지임. 만드는 쪽과 쓰는 쪽을 다른 사람이 맡을 가능성이 높아서, 형식을 먼저 맞춰야 나중에 붙일 수 있음.

*모듈 사이를 오가는 데이터 3가지를 먼저 합의함* <sub>(주황 = 형식을 회의에서 정할 데이터(스키마) · 파랑 = 확정된 처리 단계)</sub>

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"]:::done --> RES["이력<br/>경력 항목 단위"]:::todo --> MATCH["매칭<br/>요건별 판정 · 점수"]:::done
  SRC["사이트 · API 조사<br/>등록해 둔 사이트 목록"]:::done --> JD["JD<br/>요건 · 스택 · 근무지"]:::todo --> MATCH
  MATCH --> MR["매칭 결과<br/>요건별 충족 여부 + 근거"]:::todo --> OUT["DB 저장 · 화면 표시"]:::done

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef later fill:#fffbf2,stroke:#f0a800,stroke-width:2px,stroke-dasharray:6 4,color:#1f1f1f
classDef ext fill:#ffffff,stroke:#b5b5ad,color:#1f1f1f
classDef code fill:#ececea,stroke:#d6d6d2,color:#1f1f1f
classDef fake fill:#ffffff,stroke:#b5b5ad,stroke-dasharray:5 4,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

| 스키마 | 만드는 곳 | 쓰는 곳 | 들어갈 내용 (초안) |
| --- | --- | --- | --- |
| JD | 사이트·API 조사 | 매칭, 화면 | 회사, 직무명, 원문 링크, 근무지, 필수 요건, 우대 요건, 기술 스택, 언어, 최소 연차 |
| 이력 | 이력서 업로드(파싱) | 매칭, 화면(수정) | 경력 항목(회사, 기간, 역할, 한 일), 기술 스택, 언어 |
| 매칭 결과 | 매칭 | DB 저장, 화면 | 요건별 판정(충족·부분 충족·미충족)과 근거 이력 항목, 점수, 언어·근무지·연차 필터 결과 |

항목은 초안이고 회의에서 확정함. 같이 정할 것: 협업 성향 같은 소프트 요건을 따로 나눌지, 점수를 퍼센트로 보여줄지.

공고 구조화 단계 (추후): PoC는 매칭 agent가 공고 원문을 바로 읽음. 나중에 공고를 JD 스키마로 구조화하는 단계를 따로 두고, 매칭은 구조화된 JD를 읽게 할지 정함. 구조화하면 저장·필터링에 쓸 수 있지만 공고마다 LLM 호출이 한 번 더 늘어남.

## 3. 협업 규칙

CONTRIBUTING의 규칙(브랜치 이름, PR 병합 조건, semi-linear 히스토리, 설명할 수 있는 코드)은 이미 main에 있음. 새로 정할 것은 아래 4가지임.

*기존 PR 흐름(파랑)은 그대로, ①\~④를 새로 정함* <sub>(파랑 확정(CONTRIBUTING) · 주황 미정(회의에서 정함))</sub>

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart TB
  subgraph FEAT["기능 코드 (CONTRIBUTING)"]
    direction LR
    subgraph C1[" "]
      direction TB
      B1["작업 브랜치<br/>feat/… · fix/…"]:::done --- R2["② 커밋 금지 대상<br/>이력서 · 키 · 실행 결과"]:::todo
    end
    subgraph C2[" "]
      direction TB
      B2["PR<br/>템플릿 · 400줄 권장"]:::done --- R3A["③ 체크리스트 확인<br/>리뷰어가 볼지"]:::todo
    end
    subgraph C3[" "]
      direction TB
      B3["리뷰 + CI<br/>승인 1명 · CI 통과"]:::done --- R3B["③ CI 검사 범위<br/>web/ 포함 여부"]:::todo
    end
    subgraph C4[" "]
      direction TB
      B4["main<br/>merge commit만"]:::done --- R4["④ 문서·회의록 위치<br/>docs/ 또는 외부 문서"]:::todo
    end
    C1 --> C2 --> C3 --> C4
  end
  subgraph DEMO["PoC · 데모 · ① 어디에 둘지 정함"]
    direction LR
    PB["개인 브랜치<br/>PoC · 데모 코드"]:::done --> PA["A. 링크로만 공유<br/>main에 넣지 않음"]:::todo
    PB --> PP["B. poc/ 폴더로 PR<br/>기능 코드와 같은 규칙으로 병합"]:::todo
  end
  FEAT ~~~ DEMO

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef later fill:#fffbf2,stroke:#f0a800,stroke-width:2px,stroke-dasharray:6 4,color:#1f1f1f
classDef ext fill:#ffffff,stroke:#b5b5ad,color:#1f1f1f
classDef code fill:#ececea,stroke:#d6d6d2,color:#1f1f1f
classDef fake fill:#ffffff,stroke:#b5b5ad,stroke-dasharray:5 4,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

| 정할 것 | 지금 | 선택지 | 결정 |
| --- | --- | --- | --- |
| ① PoC·데모 코드 위치 | PoC는 `feat/poc-mcp-agent` 브랜치에만 있고 PR 없음 | A. 개인 브랜치에 두고 링크로 공유 / B. `poc/` 폴더로 PR해서 main에 넣음 |  |
| ② 커밋하면 안 되는 것 | `.env`와 `poc/private/`를 gitignore로 막음. 저장소가 공개라 올리면 누구나 봄 | 이력서·키·실행 결과를 둘 공통 경로 / 실수로 올렸을 때 처리(키 폐기, 히스토리 정리) |  |
| ③ PR 규칙 적용 방식 | 400줄 권장과 체크리스트는 작성자가 스스로 체크함. CI는 Python lint·test만 돌려서 `web/`은 검사 안 함 | 리뷰어가 체크리스트를 확인할지 / 병합되는 코드는 모두 CI가 검사할지 |  |
| ④ 문서·회의록 위치 | main에 문서 폴더 없음. PR #2(닫힘)에 `docs/meetings/` 회의록 초안이 있었음. 이 안건 문서는 외부 문서임 | 저장소 `docs/` / 외부 문서 |  |

## 4. 역할 분담

README의 역할(BE1·BE2·LLM1·LLM2·FE·DevOps·PO)은 Gmail 알림을 수집하는 흐름을 기준으로 나눈 것임. 다른 안건에서 할 일이 바뀌어서, 사람을 배정하기 전에 역할 구분부터 다시 정함.

*README 역할은 Gmail 수집 기준이라 지금 할 일과 어긋남* <sub>(파랑 README에 있는 역할 · 주황 담당 미정(회의에서 정함) · 주황 점선 추후 · 스키마(2번)는 README대로 전원 합의)</sub>

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart LR
  subgraph README["README 역할"]
    BE1["BE1<br/>src/ingest · src/api"]:::done
    BE2["BE2<br/>src/scraper"]:::done
    LLM12["LLM1 · LLM2<br/>src/llm"]:::done
    FER["FE<br/>web/"]:::done
    DEVOPS["DevOps<br/>infra/ · CI"]:::done
    PO["PO<br/>eval/ (LLM2와 함께)"]:::done
  end
  subgraph NOW["지금 할 일 (안건 페이지)"]
    ALERT["알림 메일 수집<br/>1번에서 제외 · 추후"]:::later
    APIX["API · 실행 방식<br/>BE"]:::todo
    ADP["공고 소스 어댑터<br/>BE"]:::todo
    MCP["MCP 서버<br/>LLM · 새로 생김 · 담당 없음"]:::todo
    AGENT["agent 구현 · 포트<br/>LLM · 검색도 agent가 함"]:::todo
    WEB["web<br/>FE"]:::todo
    INFRA["인프라 · CI<br/>DevOps"]:::todo
    EVAL["평가 셋 · 라벨<br/>Test"]:::todo
  end
  BE1 -.-> ALERT
  BE1 --> APIX
  BE2 --> ADP
  LLM12 --> AGENT
  FER --> WEB
  DEVOPS --> INFRA
  PO --> EVAL

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef later fill:#fffbf2,stroke:#f0a800,stroke-width:2px,stroke-dasharray:6 4,color:#1f1f1f
classDef ext fill:#ffffff,stroke:#b5b5ad,color:#1f1f1f
classDef code fill:#ececea,stroke:#d6d6d2,color:#1f1f1f
classDef fake fill:#ffffff,stroke:#b5b5ad,stroke-dasharray:5 4,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

- **빠진 일:** BE1의 `src/ingest`(알림 메일 수집)는 1번에서 제외됨. BE1에는 API만 남음
- **새로 생긴 일:** MCP 서버(LLM-4)와 agent 구현·포트(LLM-2·3)는 README에 없음. PoC에서는 공고 검색도 agent가 도구로 함
- **바뀐 일:** `src/scraper`(ATS 어댑터)는 MCP를 두면 도구 뒤의 공고 소스 어댑터가 됨(LLM-4, BE-3)
- **인원:** README 역할은 7개, 팀은 6명임

| 정할 것 | 선택지 | 참고 | 결정 |
| --- | --- | --- | --- |
| 역할 구분 | A. README 역할 유지 / B. 지금 할 일(위 그림 오른쪽) 기준으로 다시 나눔 / C. 구현 3개를 한 명씩 맡고 공통 부분을 나눔 | C는 LLM-3 가정(구현 3개 비교) 기준 |  |
| 담당자 | 역할 구분이 정해진 뒤 배정 | 스키마(2번)는 README대로 전원 합의 |  |

## 참고 자료

- [PoC 브랜치 feat/poc-mcp-agent](https://github.com/ai-agent-study-group/job-matching-agent/tree/feat/poc-mcp-agent): pydantic-ai agent + MCP 서버(Greenhouse 공고 조회) + LiteLLM 프록시. 포트·어댑터 구조로 되어 있음
- [PR #2 sample docs & web folder update](https://github.com/ai-agent-study-group/job-matching-agent/pull/2): Next.js web 데모, 회의록, STRUCTURE.md
- [첫 회의록 (2026-10-13)](https://github.com/ai-agent-study-group/job-matching-agent/blob/feat/create_sample/docs/meetings/2026-10-13.md): Phase 1·2 범위와 미정 항목
- [STRUCTURE.md](https://github.com/ai-agent-study-group/job-matching-agent/blob/feat/create_sample/docs/STRUCTURE.md): 지금 구조와 정해야 할 연결 지점
- [CONTRIBUTING.md](https://github.com/ai-agent-study-group/job-matching-agent/blob/main/CONTRIBUTING.md): 브랜치·PR 규칙
