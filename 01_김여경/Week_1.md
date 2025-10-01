### 1. WAI-ARIA란?
WAI-ARIA(Web Accessibility Initiative - Accessible Rich Internet Applications)는 웹 콘텐츠와 웹 애플리케이션의 접근성을 향상시키기 위한 W3C의 기술 명세이다.
WAI-ARIA는 동적 콘텐츠와 사용자 정의 UI 요소(Custom UI Components)가 보조기기(스크린리더 등)에서 인식될 수 있도록, HTML 요소에 의미(역할), 상태, 속성 정보를 추가해주는 표준이다.

### 2. WAI-ARIA 도입 배경과 목적
- RIA(Rich Internet Applications)에서는 동적 콘텐츠와 복잡한 UI로 인해 보조기기 인식이 어려움.
- 스크린리더 등 보조기기가 동작과 구조를 인식할 수 있도록 돕기 위해 ARIA 도입.
- Ajax, 비동기 자바스크립트, XML 등을 활용한 동적 웹 애플리케이션에서 스크린리더 등 보조기기가 콘텐츠를 제대로 인식하지 못하는 문제 해결.
- 2014년 W3C 권고안으로 발표되어 HTML5에 통합됨.

### 3. WAI-ARIA의 주요 기능 (Roles, Properties, States)
- 역할(Role), 속성(Property), 상태(State)가 있다.
- 역할, 속성, 상태 정보를 HTML 요소에 부여하고 자바스크립트를 통해 동적으로 변경되는 콘텐츠의 상태를 보조기기에 전달.
- 접근성 API를 통해 보조기기와 브라우저 간 통신 지원.



> **1. Role (역할)**
- 역할은 요소의 목적과 기능을 정의 (버튼, 탭, 메뉴 등)
- 예: `<nav role="navigation" aria-label="메인 메뉴">` 이 영역이 네비게이션임을 알림.

- 4가지 카테고리
  - Abstract Role (추상 역할)
    • 개발자가 직접 사용하지 않는 기본 역할
    • 다른 역할들의 기반이 되는 추상 클래스 개념
    • 예: widget, command, composite 등
    • 실제 코딩에서는 사용하지 않으며, 명세를 이해하기 위한 개념

  - Document Structure Role (문서구조)
    • heading: 제목 (`<h1>~<h6>`와 유사)
    • list, listitem: 목록과 목록 항목
    • table, row, cell: 표 구조
    • article, section: 문서의 독립적인 영역
    • separator: 구분선

  - Landmark Role (랜드마크)
    • application: 웹 애플리케이션 영역
    • banner: 사이트 로고나 제목이 있는 헤더 영역 - `<header>` 요소
    • navigation: 네비게이션 영역 - HTML5의 `<nav>` 요소에 해당
    • main: 메인 콘텐츠 영역 - HTML5의 `<main>` 요소
    • complementary: 메인 콘텐츠를 보완하는 부가 콘텐츠 - HTML5의 `<aside>` 요소
    • form: 폼 영역 - HTML5의 `<form>` 요소

  - Widget Role (위젯)
    • UI 컴포넌트의 역할 정의 (button, checkbox, menu 등)

---

> **2. Property (속성)**
- 요소의 특징이나 상황을 의미하며, 값이 자주 변하지 않는다.
속성
- aria-label: 요소에 레이블 제공
- aria-labelledby: 다른 요소의 ID를 참조하여 레이블 지정
- aria-describedby: 추가 설명 요소를 지정
- aria-required: 필수 입력 항목 표시
- aria-roledescription: 사용자 정의 역할 설명을 제공 (비표준 역할일 경우)
- aria-hidden: 요소를 보조기기에서 숨김

`<div role="dialog" aria-labelledby="dialog-title">`
aria-labelledby - 이 요소의 이름(라벨)을 다른 요소의 텍스트로 설정

---

> **3. State (상태)**
- 요소의 현재 상태를 표현
- 체크되어 있는지, 확장되어 있는지, 비활성화 상태인지 등을 보조기기에 전달
  • aria-checked: 체크여부 (체크박스, 토글 등)
  • aria-expanded: 확장/축소 상태 (아코디언, 드롭다운 등)
  • aria-selected: 항목 선택 여부 (탭, 리스트 등)
  • aria-disabled: 비활성화 상태
  • aria-readonly: 읽기 전용 여부


```html
<!-- aria-required (필수 입력 여부)--> 
<label for="user-email">이메일</label>
<input type="email" id="user-email" aria-required="true">

<!-- aria-expanded 확장/축소 상태를 보조기기에 전달-->
<ul id="menu" role="tree"> <!-- 트리 형태의 메뉴 구조 -->
  <li id="menu-item-1" role="treeitem" aria-expanded="true"> <!-- 열림 상태 -->
    <a>WAI-ARIA 소개</a>
    <ul id="sub-menu" role="group">  
      <li id="menu-item-1-1" role="treeitem" aria-expanded="false"> <!-- 항목 닫힘 상태 -->
        <a>WAI-ARIA 란?</a>
      </li>
    </ul>
  </li>
</ul>
```


### 4. WAI-ARIA 사용 시 주의점
- HTML5의 시맨틱 요소를 **우선적으로 사용**하고, WAI-ARIA는 **보완용**으로 활용한다.
- 잘못 사용된 ARIA 속성은 오히려 **접근성을 떨어뜨릴 수 있으므로 주의가 필요**하다.
- HTML 요소가 이미 시맨틱한 의미를 가진 경우, 동일한 역할(Role)을 **중복 정의하지 않는다.**
- `aria-*` 속성이나 `role`을 적용할 땐 반드시 **스크린리더 등 보조기기로 테스트**를 병행한다.
- 의미 있는 요소(`<button>`, `<a>` 등)에 `role="presentation"` 같은 시맨틱 제거 역할을 사용하면 **접근성 문제가 발생**할 수 있다.


#### 예시

```html
<!-- ❌ 잘못된 예 -->
<button role="button">제출</button> <!-- 의미 중복 -->


<!-- ✅ 좋은 예: 시맨틱 요소 그대로 사용 -->
 <button>제출</button>
```


### 5. HTML5와 ARIA의 관계
- HTML5 시맨틱 요소를 우선적으로 사용하여 문서 구조와 의미를 명확히 한다.
- 동적 UI나 복잡한 컴포넌트에서는 ARIA Landmark Role을 추가해 보조기기에 의미를 보완한다.

- Landmark Role과 HTML5 요소 매핑
  • role="application" - 없음 (별도의 시맨틱 요소 없음, 필요 시 사용)
  • role="banner" - `<header>` (페이지나 섹션의 상단 영역)
  • role="navigation" - `<nav>` (내비게이션 영역)
  • role="main" - `<main>` (문서의 주요 콘텐츠 영역)
  • role="complementary" - `<aside>` (보조 콘텐츠 영역)
  • role="form" - `<form>` (사용자 입력 폼)
  • role="search" - 없음 (검색 영역, 시맨틱 요소 없음)
  • role="contentinfo" - `<footer>` (문서나 섹션의 하단 정보 영역)


#### 예시
```html
<div role="banner">header</div>
<div role="navigation">nav</div>
<div role="main">main</div>
<div role="contentinfo">footer</div>
```

### 6. 랜드마크 Role의 종류

1. application
  - 웹 애플리케이션 영역을 선언.
  - 스크린리더는 해당 영역을 앱 컨텍스트로 인식.
  - 페이지 내의 특정 영역을 데스크탑 애플리케이션처럼 동작하도록 처리할 때 사용.
  - 복잡한 키보드 인터랙션이 필요한 위젯에만 제한적으로 사용 (게임, 드로잉 앱, 음악 편집기 등)
  - 전체 페이지에 적용하면 안 됨 (필요한 영역에만 사용)
  - 스크린리더의 브라우즈 모드를 비활성화시킴

2. banner
  - 페이지 상단의 헤더 영역
  - 사이트 로고, 검색, 네비게이션 등이 포함
  - `<header>` 요소가 페이지 최상위에 있을 때 자동으로 banner 역할
  - 문서당 하나의 최상위 banner만 존재해야 함
  - 전체 사이트의 공통 머리말(사이트 전체 헤더)에 사용

3. navigation
  - 사이트나 페이지의 탐색 링크 모음
  - 메인 메뉴, 사이드바 메뉴 등에 사용
  - `<nav>` 요소와 동일한 역할.

4. main
  - 페이지의 주요 콘텐츠 영역
  - 문서당 하나만 존재해야 함
  - `<main>` 요소와 동일한 역할

5. complementary
  - 메인 콘텐츠를 보완하는 부가 정보
  - 사이드바, 관련 링크, 추천 콘텐츠 등에 사용
  - `<aside>` 요소와 동일한 역할

6. form
  - 사용자 입력을 위한 폼 영역
  - `<form>` 요소와 함께 사용되며, 명확한 구분이 필요할 때 aria-labelledby로 제목 지정 가능.

7. search
  - 검색 기능 영역
  - 검색 폼이나 검색 관련 콘텐츠
  - `<form role="search">` 형태로 사용되며, 페이지 어디에 있든 스크린리더가 검색 기능임을 인식할 수 있도록 함.

8. contentinfo
  - 저작권, 연락처, 관련 링크 등 페이지 하단의 푸터 정보
  - `<footer>` 요소와 비슷한 역할
  - 문서당 하나만 존재해야 하며, 사이트 전체에 대한 정보일 때 사용.
  - `<footer>` 요소가 페이지 최상위에 있을 때만 자동으로 contentinfo 역할
  - 섹션 내부의 `<footer>`는 contentinfo 역할이 아님


### 7. 키보드 접근성이란?
- 사용자가 Tab, Enter, Space, 방향키 등 키보드만으로 웹사이트의 모든 기능을 사용할 수 있도록 만드는 것.

- 예시
  - Tab 키로 포커스를 이동
  - Enter 또는 Space 키로 버튼 클릭
  - 방향키로 메뉴 항목 이동
  - Escape로 모달 닫기 등


### 7-1 키보드 접근성과 ARIA의 관계
`role`  
- 예: `<div role="button">`
-  시맨틱하지 않은 요소(div 등)를 버튼처럼 보조기기에 인식시키는 역할을 함
• 하지만 역할만 지정해도 키보드로 작동되지는 않음

`tabindex`
- `tabindex="0"` : 키보드로 포커스 이동 가능하며, 문서 순서대로 탐색
-  tabindex="-1" : 스크린리더는 인식하지만, Tab 키로는 이동 불가 (스크립트로만 포커스 가능)
- 비시맨틱 요소도 키보드로 접근 가능하게 만들어줌

`aria-*`속성 

- 요소의 상태 정보를 스크린리더 등 보조기기에 전달 (예: 열림/닫힘, 선택됨 등)
- 예: `aria-expanded`, `aria-checked`, `aria-disabled` 등
- 시각적으로는 보이지 않아도, 보조기기는 상태를 파악 가능

키보드 이벤트 처리 (JS 필수)
- `Enter`, `Space` 등 키 입력 처리 필요 (ARIA 자체는 동작 X)
- `<div role="button">`의 클릭을 Enter or Space로 동작시키려면 JS 이벤트 핸들링 필요


```html
<!-- ❌ 잘못된 예 -->
<div onclick="doSomething()">클릭하세요</div>


<!-- ✅ 올바른 예 -->
<button onclick="doSomething()">클릭하세요</button>


<!-- 또는 div를 꼭 써야 한다면 -->
<div 
  role="button" 
  tabindex="0"
  onclick="doSomething()"
  onkeydown="if(event.key==='Enter'||event.key===' ') doSomething()">
  클릭하세요
</div>

```
