# test (삭제 예정)

## V1 full

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}, "flowchart": {"wrappingWidth": 400}}}%%
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"]:::done --> RES["이력<br/>경력 항목 단위"]:::todo --> MATCH["매칭<br/>요건별 판정 · 점수"]:::done

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

## V2 no init

```mermaid
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"]:::done --> RES["이력<br/>경력 항목 단위"]:::todo --> MATCH["매칭<br/>요건별 판정 · 점수"]:::done

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

## V3 init without wrappingWidth

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontSize": "14px", "lineColor": "#9a9a92", "clusterBkg": "#f4f4f2", "clusterBorder": "#dcdcd6", "edgeLabelBackground": "#ffffff", "primaryTextColor": "#1f1f1f"}}}%%
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"]:::done --> RES["이력<br/>경력 항목 단위"]:::todo --> MATCH["매칭<br/>요건별 판정 · 점수"]:::done

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
classDef note fill:none,stroke:none,color:#555555
```

## V4 plain

```mermaid
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"] --> RES["이력<br/>경력 항목 단위"] --> MATCH["매칭<br/>요건별 판정 · 점수"]
```

## V5 classes without note classDef

```mermaid
flowchart LR
  REG["내 이력 등록<br/>PoC는 직접 입력"]:::done --> RES["이력<br/>경력 항목 단위"]:::todo --> MATCH["매칭<br/>요건별 판정 · 점수"]:::done

classDef done fill:#e8f0fc,stroke:#2f6fd6,stroke-width:2px,color:#1f1f1f
classDef todo fill:#fff4dc,stroke:#f0a800,stroke-width:2px,color:#1f1f1f
```
