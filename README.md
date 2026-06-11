# PP/PE 레진 구매 관리 시스템

원재료(PP/PE 레진) 구매 전 과정을 한눈에 추적하는 대시보드입니다.  
단가 네고 → 계약 체결 → 선적 → 입항 → 통관 → 배차 완료까지 모든 단계를 관리합니다.

---

## 파일 구조

```
index.html          ← 대시보드 메인 (브라우저에서 바로 열기)
data/
  data.json         ← 전체 데이터 (오더·재고·시장가)
skills/
  briefing-improver.md  ← 품질 개선 자동화 스킬 (issue-writer → issue-runner → doc-optimizer)
  issue-writer.md       ← 약점 발견 → GitHub 이슈 등록
  issue-runner.md       ← 이슈 → 코드 수정 → 이슈 종료
  doc-optimizer.md      ← 문서 검토 및 개선
README.md           ← 이 파일
```

---

## 실행 방법

`index.html`은 외부 JSON 파일을 읽기 때문에 **반드시 로컬 서버**로 실행해야 합니다.

### Python (권장)
```powershell
$py = "C:\Users\Admin\AppData\Local\Programs\Python\Python312\python.exe"
cd C:\path\to\resin-tracker
& $py -m http.server 8080
```
→ 브라우저에서 `http://localhost:8080` 접속

### VS Code
Live Server 확장 설치 후 `index.html` 우클릭 → **Open with Live Server**

---

## 탭별 기능

| 탭 | 기능 |
|----|------|
| 📊 **대시보드** | 전체 오더 요약, 긴급 알림, 오더별 단계 스테퍼 |
| 💬 **단가 네고** | 품목별 오퍼가·계약가·절감액 요약 + 오더별 상세 |
| 📝 **계약 관리** | 품목별 계약 현황 요약 + 계약 상세 내역 |
| 🚢 **선적·운송** | ETD 임박순 정렬, 14일 내 입항 예정 건 강조 |
| 🏛 **통관·배차** | 통관 + 배차 동시 현황, 진행중 건 강조 |
| 📦 **재고 현황** | 품목별 현재고·안전재고·입출고예정·재고일수(DOI) |
| 📈 **품목 이력** | 월별 시장가 추이 차트 + 오더 이력 전체 |

---

## 데이터 수정 방법

### 오더 추가/수정
`data/data.json` 파일의 `orders` 배열에 항목을 추가하거나 수정합니다.

**오더 단계값 (stage) — 아래 값만 사용 가능, 오타 주의:**
```
네고중 → 계약완료 → 발주대기 → 선적완료 → 항해중 → 통관중 → 배차완료
```
> ⚠️ 위 7개 값 외의 문자열을 입력하면 단계 뱃지 색상이 표시되지 않습니다.

**negotiation.status 허용값:** `진행중` / `완료`

**customs.status / dispatch.status 허용값:** `대기중` / `진행중` / `완료`

**오더 구조 예시:**
```json
{
  "id": "ORD-2026-006",
  "product": "PP Homo",
  "grade": "H380F",
  "supplier": "공급사명",
  "origin": "원산지",
  "quantity": 500,
  "unit": "MT",
  "stage": "네고중",
  "negotiation": {
    "status": "진행중",
    "offerPrice": 1020,
    "counterPrice": 995,
    "contractPrice": null,
    "currency": "USD/MT",
    "marketRef": 1010,
    "date": "2026-06-11"
  },
  "contract": {
    "contractNo": "",
    "signDate": "",
    "paymentTerms": "",
    "incoterms": "CFR Busan",
    "deliveryMonth": "2026-08"
  },
  "shipment": {
    "vessel": "",
    "blNo": "",
    "etd": "",
    "eta": "",
    "portOfLoading": "",
    "portOfDischarge": "부산"
  },
  "customs": {
    "arrivalDate": "",
    "declarationDate": "",
    "clearanceDate": "",
    "status": "대기중"
  },
  "dispatch": {
    "status": "대기중",
    "date": "",
    "destination": ""
  }
}
```

### 재고 수정
`data/data.json`의 `inventory` 배열에서 품목별 수치를 직접 수정합니다.

```json
{
  "product": "PP Homo",
  "currentStock": 320,
  "safetyStock": 200,
  "incomingStock": 500,
  "outgoingPlan": 150,
  "unit": "MT",
  "avgDailyUsage": 18,
  "location": "울산 창고"
}
```

### 최종 업데이트 날짜 수정
`data/data.json` 첫 줄의 `lastUpdated` 값을 변경합니다.
```json
{ "lastUpdated": "2026-06-11", ... }
```

---

## 3인 협업 방법

1. GitHub에서 `data/data.json` 파일 클릭
2. 우측 상단 ✏️ 연필 아이콘 클릭 → 수정
3. **Commit changes** 버튼으로 저장
4. `index.html`을 새로고침하면 변경 내용 반영

> `index.html`은 수정할 필요 없습니다. 데이터만 `data.json`에서 관리합니다.

> ⚠️ **동시 편집 주의**: 3명이 같은 시간에 수정하면 GitHub에서 충돌이 발생할 수 있습니다.  
> 수정 전 항상 최신 버전을 확인하고, 한 명이 commit한 뒤 다음 사람이 편집하세요.

---

## 품목 목록

| 품목 | 안전재고 기준 | 창고 |
|------|-------------|------|
| PP Homo | 200 MT | 울산 |
| PP Copo | 150 MT | 부산 |
| HDPE | 180 MT | 인천 |
| LDPE | 100 MT | 울산 |
| LLDPE | 120 MT | 울산 |
