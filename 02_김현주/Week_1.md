### 1. WAI-ARIA 소개
- WAI-ARIA (Web Accessibility Initiative – Accessible Rich Internet Applications)
웹 애플리케이션의 접근성과 사용성을 개선하기 위해 만들어진 기술 명세.
- 등장 배경: RIA(리치 인터넷 애플리케이션)의 확산으로 다양한 동적 UI가 생겼지만 스크린리더 등 보조기술에서 이를 제대로 해석하지 못했기 때문.
- 목적: 웹 애플리케이션 요소에 역할(Role), 속성(Property), 상태(State) 정보를 부여해 보조기술이 인식할 수 있도록 함.

### 2. WAI-ARIA의 목적
- 스크린리더, 보조기기 등에서 웹 콘텐츠의 접근성과 상호 운용성을 높이기 위함.
- 자바스크립트 기반 동적 UI도 보조기기에서 올바르게 인식 가능하게 설계.
- 예: 메뉴가 접혔는지/펼쳐졌는지, 입력 필드가 필수인지 여부 등을 명확히 전달.

### 3. WAI-ARIA의 주요 기능 (Roles, States, Properties)
1. Role (역할)
 - 특정 요소의 기능 정의(버튼, 네비게이션, 헤더 등).
 - 예: <a role="button"> → 링크를 버튼으로 인식.
 - 4가지 카테고리:
   - Document Structure Role (문서구조)
   - Abstract Role (추상 역할)
   - Landmark Role (랜드마크)
   - Widget Role (위젯)
2. Property (속성)
  - 요소가 가진 기본 특성/상황 정의.
  - 예: aria-required="true", aria-readonly="true".
3. State (상태)  
  - 요소의 현재 상태 표현.
  - 예: 메뉴 접힘/펼침 → aria-expanded="true/false", 숨김 여부 → aria-hidden="true".

  ### 4. 사용 규칙 (How to Use)
- HTML5 네이티브 요소와 중복 사용 주의
(예: <button> 태그에 굳이 role="button"을 붙이지 않음).
- 의미 없는 요소(div, span)에 역할 부여 가능
(예: <span role="button">).
- 키보드 접근성 보장: tabindex 속성 활용.
- 숨김 콘텐츠 처리: aria-hidden="true"로 지정.
- 유효성 검사 필요: 잘못된 속성 사용 시 문법 오류 발생.

### 5. Landmark Role
- 웹 페이지의 주요 콘텐츠 블록을 명확하게 구분해 보조기기가 탐색하기 쉽게 도움.
- 예:
  - role="banner" → 헤더
  - role="navigation" → 내비게이션
  - role="main" → 본문
  - role="form" → 폼 영역

### 6. 랜드마크 역할(Landmark Role)의 개념
- 웹 페이지 주요 영역을 명확히 구분할 수 있도록 역할(role)을 지정.
- 스크린리더 사용자가 핫키로 영역 간 자유롭게 탐색 가능.
  - NVDA: D (다음), Shift + D (이전)
  - JAWS: ; (세미콜론), Shift + ;
  - 센스리더: J, Shift + J

### 7. 랜드마크 사용 방법
- 콘텐츠를 포함하는 컨테이너(예: <div>)에 role 속성을 부여하면 됨.
- 예시:
```javascript
<div role="banner">banner</div>
<div role="navigation">navigation</div>
<div role="main">main</div>
<div role="application">application</div>
<div role="complementary">complementary</div>
<div role="search">search</div>
<div role="form">form</div>
<div role="contentinfo">contentinfo</div>
```
### 8. 랜드마크 Role의 종류 (8가지)
1. application
 - 웹 애플리케이션 영역을 선언.
 - 스크린리더는 해당 영역을 앱 컨텍스트로 인식.
2. banner
 - 사이트 로고, 제목 등 헤더 정보 포함.
 - 브랜드 아이덴티티를 나타내는 영역.
 - 광고 배너와 혼동하지 않도록 주의.
3. navigation
 - 내비게이션 메뉴, 링크 모음.
 - 한 페이지에 2~3개 이상 사용 지양.
 - 여러 개 사용 시 aria-label로 구분 제공.
4. main
 - 페이지의 주요 콘텐츠.
 - 페이지당 한 번만 사용 가능.
5. complementary
 - 보조 콘텐츠 영역(기사 옆의 날씨, 주식 정보 등).
 - HTML5의 <aside>와 동일한 역할.
6. form
 - 사용자 입력을 위한 폼 영역.
 - 폼을 제외한 폼에 주로 사용.
7. search
 - 검색을 위한 입력 폼 영역.
8. contentinfo  
 - 저작권, 연락처, 개인정보 보호정책 등 문서 하단 정보.
 - HTML5의 <footer>와 동일한 역할.



