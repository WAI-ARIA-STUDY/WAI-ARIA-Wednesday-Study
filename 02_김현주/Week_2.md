### 탭(Tab) UI 개요 및 문제점
● 기존 코드의 문제점
- 탭 UI는 **관련 콘텐츠를 한 화면 안에서 구분해 표시**하기 위해 자주 사용된다.
- 그러나 시각장애인이나 스크린리더 사용자에게는 다음과 같은 문제가 생긴다:
  - 시각적으로만 구분되어 있을 뿐, HTML 구조상으로는 단순한 링크로 인식됨.
  - 탭 간 이동이나 콘텐츠 연결 관계를 스크린리더가 파악하기 어려움.
- 즉, 접근성(Accessibility)이 부족한 구조라는 점이 핵심 문제다.

### 잘못된 마크업 예시
```html
<div class="tab-interface">
  <ul class="tab-list">
    <li><a href="/">공지사항</a></li>
    <li><a href="/">자료실</a></li>
  </ul>
  <div class="tab-contents">
    <ul class="notice-list">…</ul>
    <ul class="pds-list">…</ul>
  </div>
</div>
```
- 시각적으로는 탭처럼 보이지만, HTML 상에서는 단순한 `<a>` 링크 구조.
- 사용자는 탭 클릭 시 콘텐츠가 바뀌는 듯 보이지만, 스크린리더는 단순한 하이퍼링크 목록으로만 인식함.따라서 - 탭과 콘텐츠 간의 관계가 명시되지 않아 정보 연결이 단절됨.

### 논리적인 구조로 개선된 예시
(1) 순서 논리 정리
탭의 흐름을 다음과 같은 콘텐츠 구조 순서로 재배치:
> 탭 ➜ 공지사항 콘텐츠 ➜ 자료실 콘텐츠 ➜ 더보기 링크
```html
<div class="tab-interface">
  <div class="tab-contents">
    <div id="notice">…공지사항 내용…</div>
    <div id="pds">…자료실 내용…</div>
  </div>
</div>
```
- 각 콘텐츠 블록(<div id="notice">, <div id="pds">)을 별도로 구분해
탭 클릭 시 해당 콘텐츠만 활성화되도록 함.
- 이렇게 하면 탭–콘텐츠 간의 관계와 논리적 순서가 명확해진다.

### WAI-ARIA를 사용해야 하는 이유

- 단순히 마크업 순서를 조정하는 것만으로는 한계가 있다.
- 스크린리더는 여전히 어떤 탭이 활성화되었는지 알 수 없음.
- 따라서 WAI-ARIA의 role, aria-* 속성을 통해
**“이 요소가 탭이다”, “이 콘텐츠는 탭에 연결된 영역이다”**를 명시적으로 알려야 함.

### 탭 UI용 WAI-ARIA 구조 개념도
| 역할(Role)   | 요소 예시       | 설명                                        |
| ---------- | ----------- | ----------------------------------------- |
| `tablist`  | `<ul>`      | 탭 전체 그룹                                   |
| `tab`      | `<a>`       | 개별 탭 역할 지정 (`aria-selected="true/false"`) |
| `tabpanel` | `<section>` | 탭 콘텐츠 영역 (`aria-labelledby`로 연결)          |

#### 핵심 연결 속성
- aria-controls : 탭이 제어하는 콘텐츠의 id
- aria-labelledby : 콘텐츠가 어떤 탭에 의해 제어되는지 연결
- aria-selected : 현재 선택된 탭 여부 (true / false)
→ 이 구조 덕분에 스크린리더는 “현재 탭 1, HTML 선택됨 / 탭 2, CSS / 탭 3, JavaScript” 식으로 정확히 인식 가능.
### WAI-ARIA 속성 정리표
| 요소      | 역할(Role)       | 속성(Property)      | 설명                   |
| ------- | -------------- | ----------------- | -------------------- |
| `<ul>`  | `tablist`      | —                 | 탭 전체 그룹 지정           |
| `<li>`  | `presentation` | —                 | 리스트의 의미 제거 (시각용만 사용) |
| `<a>`   | `tab`          | `aria-selected`   | 탭의 선택 여부             |
| `<a>`   | `tab`          | `aria-controls`   | 연결된 패널 id            |
| `<div>` | `tabpanel`     | `aria-labelledby` | 연결된 탭의 id            |
| `<div>` | `tabpanel`     | `aria-expanded`   | 현재 패널의 확장 여부         |

### 소스 코드구조(HTML + CSS)
```html
<ul role="tablist" class="tab-list">
  <li><a role="tab" aria-controls="section1" aria-selected="true">HTML</a></li>
  <li><a role="tab" aria-controls="section2" aria-selected="false">CSS</a></li>
  <li><a role="tab" aria-controls="section3" aria-selected="false">JavaScript</a></li>
</ul>

<div class="tab-contents">
  <section id="section1" role="tabpanel" aria-labelledby="tab1">…</section>
  <section id="section2" role="tabpanel" aria-labelledby="tab2" class="unvisual">…</section>
  <section id="section3" role="tabpanel" aria-labelledby="tab3" class="unvisual">…</section>
</div>
```

### jQuery동작 원리
(1) 키보드 제어
```javascript
$('[role="tab"]').keyup(function(e) {
  var keyCode = e.keyCode;
  if (keyCode == 39 || keyCode == 40) { … } // 오른쪽/아래 이동
  if (keyCode == 37 || keyCode == 38) { … } // 왼쪽/위 이동
  if (keyCode == 35) { … } // End 키 → 마지막 탭
  if (keyCode == 36) { … } // Home 키 → 첫 번째 탭
});
```
- 방향키와 Home/End키로 탭 이동 가능.
- aria-selected 속성을 갱신하며 활성 탭을 전환

(2) 클릭 이벤트
```javascript
$('[role="tab"]').on('click', function(e) {
  e.preventDefault();
  $(this).attr('aria-selected', true)
         .siblings().attr('aria-selected', false);
  var id = $(this).attr('aria-controls');
  $('#' + id).removeClass('unvisual')
             .siblings().addClass('unvisual');
});
```
- 클릭 시 해당 탭 활성화 및 연결된 패널만 표시.

### 키보드 인터랙션 정리
| 키     | 동작            |
| ----- | ------------- |
| ← / ↑ | 이전 탭으로 이동     |
| → / ↓ | 다음 탭으로 이동     |
| Home  | 첫 번째 탭으로 이동   |
| End   | 마지막 탭으로 이동    |
| Tab   | 현재 탭의 콘텐츠로 이동 |

## 정리 요약
- 기존 문제: 시각적으로만 탭처럼 보이는 링크 구조 → 스크린리더 접근 불가.
- 해결 방법:
1. 논리적인 콘텐츠 순서 재배치
2. WAI-ARIA 속성(role, aria-selected, aria-controls, aria-labelledby) 적용
3. 키보드 네비게이션 및 포커스 이동 제어
- 결과: 시각장애인, 키보드 사용자도 동일한 UX로 탭 콘텐츠를 탐색 가능.

### 탭(Tab) UI - 접근성(WAI-ARIA) 기반 구현
(1) 구조 개요
- `<ul role="tablist">` : 탭 버튼들을 담는 리스트 컨테이너
- `<li role="tab">` : 각각의 탭 항목. aria-controls로 연결된 패널(tabpanel)을 제어
- `<section role="tabpanel">` : 실제 탭 콘텐츠. aria-labelledby로 탭 버튼과 연동
→ HTML 구조만 봐도 탭과 패널의 관계가 명확히 연결되어 있음.

(2) 스크립트 동작
✅ 키보드 접근성
- 좌우 방향키 (←, →) : 이전/다음 탭으로 포커스 이동
- Home / End 키 : 첫 번째 혹은 마지막 탭으로 이동
- Tab 키 : 탭 내 콘텐츠로 이동
- 키 입력 시 aria-selected 속성을 true/false로 토글,
선택된 탭에 해당하는 패널만 보이게(removeClass('unvisual')) 처리함.

✅ 클릭 이벤트
- [role="tab"] 요소 클릭 시
  - 현재 탭에 aria-selected="true" 지정
  - 나머지 탭은 aria-selected="false" 처리
  - 연결된 tabpanel만 표시되도록 DOM 업데이트

✅ 시각적 표시
- CSS 클래스 unvisual을 이용해 비활성 탭 패널은 숨김 처리.

(3) 키보드 인터랙션 정리 표

| 키     | 동작                         |
| ----- | -------------------------- |
| ← / ↑ | 이전 탭 이동 (첫 번째에서 누르면 마지막으로) |
| → / ↓ | 다음 탭 이동 (마지막에서 누르면 처음으로)   |
| Tab   | 현재 탭 안의 콘텐츠로 이동            |
| Home  | 첫 번째 탭으로 이동                |
| End   | 마지막 탭으로 이동                 |

→ 시각장애인, 키보드 사용자도 동일한 UX를 경험할 수 있도록 설계.

### 자동완성(Auto Complete) UI
(1) 기존 코드 문제
- 일반 HTML 마크업만으로는 겉보기에 정상 동작하지만,
스크린리더 사용자는 추천 검색 목록 존재 여부를 알 수 없음.
- 입력창에서 제안 목록이 생겨도, 이를 탐색하거나 읽을 수 없는 문제가 있음.

(2) WAI-ARIA 필요성
- ARIA는 비시각 사용자(스크린리더)에게 구조적 의미를 전달하기 위한 표준.
- 단순 <ul> 추천 목록이 아닌 역할(Role) 과 속성(Property) 추가로
컴포넌트의 의미와 상태를 명확히 표현해야 함.

(3) 주요 ARIA 역할 구조
| 요소        | 역할(Role)   | 속성(Property) / 상태(State)                          | 설명                 |
| --------- | ---------- | ------------------------------------------------- | ------------------ |
| `<input>` | `combobox` | `aria-autocomplete="list"`                        | 자동완성 가능한 입력 필드     |
|           |            | `aria-haspopup="true"`                            | 연결된 하위 목록 존재       |
|           |            | `aria-activedescendant="list1"`                   | 현재 선택된 항목 ID       |
| `<ul>`    | `listbox`  | —                                                 | 추천 항목 리스트 컨테이너     |
| `<li>`    | `option`   | —                                                 | 각 추천 항목            |
| `<div>`   | `status`   | `aria-live="polite"`, `aria-relevant="additions"` | 추천 항목 수 등 상태 정보 제공 |
→ 스크린리더는 이 구조를 인식해 “자동완성 목록이 열렸습니다, 10개의 항목이 있습니다”처럼 안내 가능.

(4) 상태(Status) 영역의 역할
```javascript
<div id="status"
  role="status"
  aria-live="polite"
  aria-relevant="additions">
  10개의 추천 검색어가 있습니다.
</div>
```
- 실시간으로 바뀌는 추천 검색어의 개수나 상태를 사용자에게 즉시 알림.
- polite : 현재 읽고 있는 내용을 방해하지 않고 부드럽게 알림.
- additions : 새로 추가된 정보만 읽어줌.
(5) 최종 구조 예시
```javascript
<input type="text" id="search"
  role="combobox"
  aria-autocomplete="list"
  aria-haspopup="true"
  aria-activedescendant="list1" />

<ul role="listbox">
  <li id="list1" role="option" class="active">자동차 정비</li>
  <li id="list2" role="option">자동 완성 기능</li>
</ul>
```
→ 입력 필드와 추천 목록의 관계를 명확히 정의해, 시각/비시각 사용자 모두 접근 가능.

### WAI-ARIA 적용 전/후 NVDA 리더 출력 비교
- 적용 전 : “검색창 / 입력 중” 정도만 안내. 추천 목록 인식 불가.
- 적용 후 :
  - “자동완성 목록 열림 / 10개 중 1번째 항목 선택됨” 등 상세한 피드백 제공.
  - 항목 이동 시 즉시 상태 업데이트.
→ 실제 사용자 접근성 개선 효과가 뚜렷함.

### 자동완성 UI 구현 코드 흐름
1. HTML: input + ul + div#status 구조로 시멘틱 마크업
2. 스크립트 초기화:
```javascript
var $autocomplete = $('#autocomplete'),
    $input = $autocomplete.find('input:text'),
    $status = $('<div class="hidden-accessible" role="status" />');
```
→ 입력 필드 탐색, 상태 표시 영역 준비
3. 이벤트 처리:

- keyup: 입력값에 따라 추천 목록 갱신
- aria-activedescendant 갱신으로 현재 선택 항목 표시
- 추천 목록 갯수 변화 시 aria-live 영역 업데이트
4. 결과:
- 입력 시 자동완성 목록이 실시간으로 갱신되고
키보드 탐색 및 음성 피드백이 모두 지원되는 완전한 접근성 UI 완성.

### 자동완성 UI 개요 및 초기 설정
핵심 목적:
브라우저 기본 자동완성 기능을 끄고, 사용자가 입력한 키워드를 기반으로
Ajax로 데이터를 가져와 추천 목록을 표시하는 접근성(WAI-ARIA) 지원 자동완성 UI를 구현한다.
```javascript
$txtField.attr({
  'role': 'combobox',
  'aria-haspopup': 'true',
  'aria-autocomplete': 'list',
  'autocomplete': 'off'
});
```
- role="combobox" : 입력 필드가 선택 가능한 목록을 가진 컨트롤임을 명시.
- aria-haspopup="true" : 보조기기에 “이 필드가 팝업(목록)을 가진다”는 정보 제공.
- aria-autocomplete="list" : 자동완성 제안이 목록 형태로 제공됨을 알림.
- autocomplete="off" : 브라우저의 기본 자동완성 기능 비활성화.
- $status 요소에는 상태 정보(aria-live, aria-relevant)를 설정해
추천 목록 업데이트가 스크린리더에 실시간으로 전달되도록 함.

### 키보드 이벤트 바인딩
```javascript
$txtField.on({
  keyup: updateList,
  keydown: bindKeyEvent
});
```
- keyup → 사용자가 키를 뗄 때 updateList 호출 (API 요청 준비)
- keydown → 방향키, Enter, Esc 등의 키보드 조작을 위한 bindKeyEvent 호출

### updateList 함수
역할:
입력된 키워드로 API 호출을 트리거하되, 불필요한 중복 호출을 방지한다.

주요 기능:
- 키 입력이 없을 경우 return (API 호출 차단)
- Enter, Esc, 방향키 입력 시 기본 동작 유지
- 일정 시간(400ms) 동안 입력이 없으면 callAPI(keyword) 실행
- 이전 입력과 동일하면 API 호출 안 함
```javascript
delayTimer = setTimeout(function() {
  if (keyword === '') { removeList(); return; }
  callAPI(keyword);
}, 400);
```
### callAPI 함수
역할:
실제로 외부 검색 API(Daum 책 검색 API)를 호출해 JSON 데이터를 받아온다.
주요 동작:
```javascript
$.ajax({
  url: 'https://apis.daum.net/search/book',
  data: { apikey: '...', q: keyword, searchType: 'title', ... },
  dataType: 'jsonp'
})
.done(function(data) {
  // 검색 결과 목록 렌더링
  renderList(source);
});
```
### renderList 함수
역할:
검색 결과를 HTML 목록으로 생성하고, 상태 정보(aria)를 업데이트한다.
구조:
- <ul role="listbox"> : 추천 목록 컨테이너
- <li role="option"> : 각 추천 항목
- data-count 속성 : 항목 개수 저장
- $status에 “n개의 추천 검색어가 있습니다.”라는 메시지 추가

예시 생성 결과:
```javascript
<ul class="listbox" role="listbox" data-count="10">
  <li id="item0" role="option">알렉산더 왈츠 같은 것들에 대해</li>
  ...
</ul>
<div role="status" aria-live="polite">10개의 추천 검색어가 있습니다.</div>
```
### bindKeyEvent 함수
역할:
사용자의 키보드 조작에 따라 추천 항목을 탐색하거나 선택하도록 처리.
핵심 제어 키:
| 키     | 기능                      |
| ----- | ----------------------- |
| ↑ / ↓ | 추천 목록에서 이전 / 다음 항목으로 이동 |
| Enter | 선택 항목 확정 후 입력창에 반영      |
| Esc   | 목록 닫기 및 입력값 초기화         |

핵심 로직:
- 현재 선택 항목 ID(aria-activedescendant)를 기준으로 인덱스 계산
- 위/아래 방향키에 따라 selectItem()과 deSelectItem() 호출
- Enter 시 해당 항목 텍스트를 입력창에 적용하고 form.submit()

### selectItem / deSelectItem 함수
(1) selectItem(idx)

- 선택 항목에 .active 클래스 부여
- 입력창($txtField)에 aria-activedescendant 속성 추가
→ 스크린리더가 “현재 선택된 항목”을 인식함
```javascript
$txtField.attr('aria-activedescendant', 'item' + idx);
```
(2) deSelectItem(idx)
- 선택 해제 시 .active 클래스 제거

### removeList 함수
역할:
추천 목록을 DOM에서 완전히 제거하고, 관련 속성을 초기화한다.
```javascript
if ($suggestedList != null) {
  $suggestedList.remove();
  $suggestedList = null;
  $txtField.removeAttr('aria-activedescendant')
           .attr('aria-expanded', 'false');
  $status.empty();
}
```
- 목록이 사라지면 aria-expanded="false"로 변경
- 상태 영역 비움 (화면리더에 “목록이 닫혔다” 알림)

### 최종 동작 구조 요약

- 사용자가 텍스트 입력 → keyup 발생 → updateList() 실행
- 일정 시간 입력 없으면 → callAPI() → 추천 데이터 수신
- renderList() → <ul><li>로 목록 렌더링
- 방향키로 탐색 (bindKeyEvent → selectItem / deSelectItem)
- Enter로 항목 선택 → 입력창에 반영
- Esc로 목록 닫기 (removeList)

### 키보드 인터랙션 요약표

| 키           | 동작                       |
| ----------- | ------------------------ |
| **상/하 방향키** | 추천 목록 탐색 (이전/다음 항목으로 이동) |
| **Enter**   | 현재 선택된 항목을 입력창에 반영 후 제출  |
| **Esc**     | 추천 목록 닫기                 |
