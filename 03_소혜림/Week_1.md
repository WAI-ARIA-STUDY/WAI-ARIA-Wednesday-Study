# 제 1장 개요

## 1. WAI-ARIA 소개

### 1-1. WAI-ARIA란?

- 웹은 처음엔 문서 연결 중심이었지만, RIA의 등장으로 **애플리케이션 수준의 UX**를 요구하게 됨.  
- 그러나 `<div>`, `<span>` 같은 비의미적 요소 사용과 자동 업데이트 기능은 **보조기기 접근성 문제**를 야기.  
- 이를 해결하기 위해 **W3C**가 **WAI-ARIA(Web Accessibility Initiative – Accessible Rich Internet Applications)** 명세를 발표.  
- WAI-ARIA는 웹 요소에 **역할(Role), 속성(Property), 상태(State)** 정보를 부여해 **스크린리더 등 보조기기 호환성**을 높임.  
- **2014년 W3C 권고안**으로 지정, 일부는 **HTML5 표준에 통합**됨.

---

### 1-2. WAI-ARIA의 목적

- **접근성 및 상호 운용성 향상**  
  - 마크업에 **역할(Role), 속성(Property), 상태(State)** 정보를 추가해 보조기기와의 호환성을 높임.  
  - 추가된 정보는 브라우저에서 해석되어 OS의 **접근성 API**로 변환됨.  

- **보조기기와의 상호작용**  
  - 스크린리더 등은 접근성 API를 통해 **데스크탑 앱과 유사한 방식**으로 웹 애플리케이션을 인식·작동.  
  - 그 결과, 장애인 사용자도 **향상된 UX**를 경험할 수 있음.  

- **지원 현황과 한계**  
  - 최신 브라우저는 WAI-ARIA 기능을 잘 지원하지만, **스크린리더별 지원 차이** 존재.  
    - 예: NVDA·JAWS는 키보드 인터랙션 정상 처리, **센스리더**는 일부 인식 불가.  
    - `aria-live` 속성은 **JAWS 버그**로 특정 상황에서 동작하지 않음.  

- **핵심 목적**  
  - 요소나 컴포넌트에 **누락된 의미 구조를 제공** → 논리적 구조 설계 가능.  
  - 페이지 영역의 **빠른 탐색**, 동적 콘텐츠 식별, 상태 변화 알림 등을 통해 **보조기기 사용성을 개선**.

---

## 2. ARIA Roles & States and Properties

WAI-ARIA는 접근성과 상호 운용성을 높이기 위해 **역할(Role)**, **속성(Property)**, **상태(State)** 기능을 제공한다.

---

### 2-1. Role (역할)
- UI 컴포넌트가 어떤 기능을 하는지 정의.  
- 예: `<a>` 요소로 버튼을 구현하면 스크린리더는 "링크"라고 읽음 → `role="button"` 지정 시 버튼으로 인식.  
- 기본 키보드 인터랙션(스페이스바 등)까지 추가해주면 버튼 역할 완성.  

```html
<a href="/" onclick="playApp()" role="button">재생</a>
```

- Role 카테고리:
  - Document Structure Role (문서구조)
  - Abstract Role (추상)
  - Landmark Role (랜드마크)
  - Widget Role (위젯)

---

### 2-2. Property (속성)
- 요소의 특징이나 상황 정의. aria-* 접두사 사용.
- 예: 입력 필드가 필수 입력임을 알릴 때 aria-required="true" 사용.

```html
<div class="id-area">
  <label for="user-email">아이디</label>
  <input type="email" id="user-email" aria-required="true">
</div>
<div class="pw-area">
  <label for="user-pw">비밀번호</label>
  <input type="password" id="user-pw" aria-required="true">
</div>
```

---

### 2-3. State (상태)
- 요소의 현재 상태를 나타내며, 변화 가능.
- 예: 메뉴가 펼쳐짐/접힘 여부를 aria-expanded로 제공.
```html
<ul id="menu" role="tree">
  <li role="treeitem" aria-expanded="true">
    <a>WAI-ARIA 소개</a>
    <ul role="group">
      <li role="treeitem" aria-expanded="false">
        <a>WAI-ARIA 란?</a>
      </li>
    </ul>
  </li>
  <li role="treeitem" aria-expanded="false">
    <a>WAI-ARIA의 목적</a>
  </li>
</ul>
```

** 단, 의미에 맞는 HTML 요소를 사용하는 것이 우선이며, WAI-ARIA는 보완적 수단으로 활용해야 한다. **

---

## 3. How to Use(WAI-ARIA 작성 규칙)

### 3-1. 랜드마크와 HTML5
- HTML5 섹션 요소와 동일한 역할의 ARIA를 **중복 사용하지 말 것** (충돌 가능).  
- 주요 매핑 예시:

| Landmark Role        | HTML5 섹션 요소 | 비고 |
|----------------------|----------------|------|
| role="application"   | 없음           | `<div>` 등으로 대체 |
| role="banner"        | `<header>`     | 한 페이지 1개 권장 |
| role="navigation"    | `<nav>`        | 메인/서브 메뉴 |
| role="main"          | `<main>`       | 한 페이지 1개만 사용 가능 |
| role="complementary" | `<aside>`      | 부가 콘텐츠 (사이드바 등) |
| role="form"          | `<form>`       | 입력 콘텐츠 전송 영역 |
| role="search"        | 없음           | `<div>` 또는 `<form>` 권장 |
| role="contentinfo"   | `<footer>`     | 한 페이지 1개 권장 |

---

### 3-2. HTML 요소의 기능 변경 제한
- 네이티브 의미를 변경하지 말 것.  
- 예: `<h1 role="button">` → 잘못된 사용.  
- 대신 의미 없는 `<span>` 또는 `<button>` 요소 사용 권장.  

---

### 3-3. 키보드 사용 보장
- 대화형 UI는 반드시 **키보드 접근 가능**해야 함.  
- `tabindex` 활용:
  - `tabindex="0"`: 문서 순서대로 포커스 가능.  
  - 음수 값: 포커스 불가.  

---

### 3-4. 숨김 콘텐츠
- `aria-hidden="true"`를 단순히 화면 숨김용으로 사용하지 말 것.  
- 의미 있는 콘텐츠에는 `role="presentation"`도 지양.  
- 숨김 처리는 `display: none` + `aria-hidden="true"` 함께 사용.  

---

### 3-5. 레이블 제공
- 모든 대화형 UI는 **레이블 제공 필수**.  
- `<label>` 요소 권장, 필요 시 `aria-label`, `aria-labelledby` 사용 가능.  

---

### 3-6. 유효성 검사
- 시맨틱 요소와 충돌하는 role 사용 금지.  
- 예: `<h1 role="button">` → 문법 오류 발생.

---

## 4. Landmark Role

### 4-1. 랜드마크(Landmark Role)에 대해
- 랜드마크는 웹 페이지의 콘텐츠 유형과 역할을 식별할 수 있도록 하는 **표지판 역할**.  
- 기존 “건너뛰기 링크”보다 발전된 개념으로, 영역 제목보다 **명확한 구분** 제공.  
- 주요 스크린리더(JAWS, NVDA, ORCA, ChromeVox, VoiceOver, TalkBack 등)와 **Firefox Add-on**에서 지원.  
- 사용자는 **핫키 탐색**으로 주요 영역을 빠르게 이동 가능.  
  - 스크린리더는 `"역할 이름 + landmark"` 형식으로 알림.  
  - 컨테이너 역할의 랜드마크는 탐색 순서에는 포함되지 않지만 리스트에 포함.  
  - 포커스 시 서브 아이템 닫힘 상태를 알려주며, 오른쪽 화살표 키로 열 수 있음.  

---

### 4-2. 랜드마크(Landmark Role) 사용 방법
- **HTML 컨테이너 요소에 `role` 속성을 지정**하여 콘텐츠의 역할 부여.  

---

### 4-3. 랜드마크(Landmark Role)의 종류
총 8가지 역할 제공: **application, banner, navigation, main, complementary, form, search, contentinfo**

- **application**  
  - 정적 콘텐츠와 구분되는 **웹 애플리케이션 영역**.  
  - `role="application"`을 만나면 보조기기는 브라우저 탐색 키를 앱에 전달.  

- **banner**  
  - 사이트 로고나 제목 등 **헤더 정보**를 포함.  
  - HTML5 `<header>`와 유사, 페이지에 **1개만 사용 권장**.  
  - 광고 배너와 혼동 주의 → 주로 브랜딩·아이덴티티 정보 의미.  

- **navigation**  
  - **내비게이션 링크 모음** 영역.  
  - HTML5 `<nav>`와 동일, 중복 사용 지양.  
  - 여러 개 사용 시 `aria-label`로 구분.  

- **main**  
  - 페이지의 **주요 콘텐츠 영역**, 페이지당 **1개만 선언**.  
  - HTML5 `<main>`과 중복 사용 금지.  

- **complementary**  
  - 메인 콘텐츠를 보완하는 **부가 콘텐츠** 영역.  
  - HTML5 `<aside>`와 동일.  
  - 날씨, 주식 시세 등 독립적으로 의미 있는 정보 포함.  

- **form**  
  - 사용자 입력을 위한 **폼 영역**.  
  - HTML `<form>`과 동일, 중복 사용은 바람직하지 않음.  
  - 단, **검색 폼은 제외**.  

- **search**  
  - **검색을 위한 입력 폼 영역**.  

- **contentinfo**  
  - 저작권, 주소, 연락처, 개인정보 정책 등 **푸터 메타데이터 영역**.  
  - HTML5 `<footer>`와 유사, 페이지에 **1개만 사용 권장**.  

---

## 5. Live Region

### 5-1. Live Region
- **Live Region**은 웹 문서·위젯·애플리케이션의 일부가 **동적으로 변경**될 때,  
  사용자의 조작 없이 변경된 내용과 상태를 알리기 위해 사용되는 속성.  
- 모든 요소에 적용 가능.  
- 주요 속성:
  - **aria-live**  
    - 기본값: `off` → 업데이트 알리지 않음.  
    - `polite`: 사용자 입력이 끝난 뒤 업데이트 내용을 전달.  
    - `assertive`: 즉시 업데이트 내용을 전달.  
  - **aria-atomic**  
    - 업데이트 시 읽어줄 범위 설정.  
    - `false`: 업데이트된 부분만 읽음.  
    - `true`: 전체 내용을 읽음.  
  - **aria-busy**  
    - 업데이트 진행 여부 표시.  
    - `true`: 업데이트 중 → 값이 `false`로 바뀌기 전까지는 안내하지 않음.  
  - **aria-relevant**  
    - 어떤 변화를 사용자에게 알릴지 설정.  
    - 값:
      - `additions text` (기본값): 요소 추가 또는 콘텐츠 변경 시 안내  
      - `additions`: 요소 추가 시 안내  
      - `removals`: 요소 삭제 시 안내  
      - `text`: 콘텐츠 변경 시 안내  
      - `all`: 추가·삭제·변경 모두 안내  
