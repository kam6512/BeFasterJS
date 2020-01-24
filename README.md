## 1장. 로딩과 실행

### 1 - 1. 스크립트의 위치
__모든 \<script\> 태그들은 페이지의 가장 마지막에, \</body\> 태그의 바로 위에 선언할 것__
__스크립트를 실행하기 이전에 페이지를 모두 표시할 수 있다__

    - 페이지 렌더링 보다 스크립트 로딩을 먼저 할 경우 코드의 로딩 및 실행으로 인해 체감 속도가 떨어진다
    - 최신 브라우저는 다중 스크립트 로딩을 지원 하지만, 다른 이미지와 같은 Asset 들은 그렇지 못하다
    - 허나 일반적인 스크립트 로딩은 <head> 태그에 두는 것이 안전하고 
        해당 스크립트가 페이지 로드중에 실행되야 할 경우 더욱 그렇다

### 1 - 2. 스크립트 묶기
__스크립트를 하나로 묶어 쓸 것 \<script\> 태그가 적을 수록 페이지를 빠르게 불러오고 이용할 수 있다__
__안에 인라인 코드가 있던 외부스크립트던 마찬가지이다__

    - 페이지는 스크립트 로딩을 할 때마다, \<script> 태그를 만날 때마다 렌더링이 차단된다

### 1 - 3. 비 차단 스크립트
__스크립트를 묶더라도 큰 스크립트는 여전히 딜레이가 크다__    

__\<script\> 태그에 defer 속성을 부여__

    - defer 속성은 <script> 태그가 DOM 을 조작하지 않으므로 나중에 실행해도 상관없다는 뜻이다
    - defer 속성을 가진 <script> 태그는 어디서나 선언할 수 있다. 렌더링을 막지 않기 때문이다
    - 하지만 DOM 이 완전히 렌더링 되기까지는 실행이 되지 않는다. (그래도 window.onload 보다는 빨리 실행된다)

__\<script\> 태그를 동적으로 생성해 코드를 내려받아 실행__
    
    - DOM 의 존재로 인해 모든 HTML 요소를 동적으로 만들 수 있으며 <script> 태그도 가능하다

````javascript
var script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'sample.js';
document.getElementByTagName('head')[0].appendChild(script);
````

__XHR 객체로 JS 코드를 내려 받아 페이지에 삽입__

    - 코드를 내려받아도 즉시 실행하지 않아도 된다
    - 예외처리를 하지 않더라도 모든 최신 브라우저에서 지원한다
    - JS 파일과 그 파일을 요청한 페이지가 같은 도메인에 있어야만 한다 == CDN 불가

````javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'sample.js', true)
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4){
        if(xhr.status >= 200 && xhr.status < 300 || xhr.status == 304){ //파일이 유효한지 확인 (304 == 캐시된 응답)
            var script = document.createElement('script');
            script.type = 'text/javascript';
            script.text = xhr.responseText;
            document.body.appendChild(script);
        }
    }
}


````

## 2장 데이터 접근

### 스코프 관리
__함수의 스코프 밖에 있는 값을 한번 이상 사용할 시에는 지역변수에 해당 값을 저장하라__

    - 특정 함수를 실행하는 경우 실행 도중에 변수가 등장할 때 마다 데이터를 어디에 저장할지, 어디서 가져올지 결정하게 된다
    - 이 결정은 식별자를 해석하고 이 변수와 같은 이름의 식별자를 찾기 위해 실행문맥의 스코프 체인을 검색한다
    - 식별자를 찾을 때 까지 상위 스코프 체인을 순회하고, 여기서 오버헤드가 발생한다
    - 모든 브라우저에서 식별자가 스코프 체인의 깊숙히 있을 경우 읽고 쓰는데 오버헤드가 생긴다
    - 리터럴 값과 지역 변수는 매우 빠른 접근을 보장한다, 배열 항목 및 객체멤버는 이보다는 느리다
    - 스코프 밖의 변수는 스코프 체인의 첫번째 변수 객체에 있으므로, 지역변수보다 느리다
    - 전역변수는 스코프 체인의 깊숙히 있는 변수이므로 가장 느리다
    - with 문과 try-catch 는 실행문맥의 스코프 체인을 확장시킨다
    - 중첩된 객체 멤버는 성능에 심각한 영향을 주므로 최소한으로 줄일 것
    - 속성 및 메서드가 프로토타입 체인의 깊이 있을수록 접근이 느려진다
    - 자주 사용하는 객체멤버와 배열항목, 스코프 밖의  변수를 지역변수에 저장하면 대부분의 경우 더 빠른 접근으로 성능을 향상시킬 수 있습니다


## 3장 DOM 컨트롤 스크립팅

__프론트 웹 서비스는 DOM 에 접근하고 조작하는 일이 자주 발생하여, DOM 과 ES 가 서로 상호 작용하는 비용이 크므로 이를 줄여야 한다__

    - DOM 접근을 최소하하고, ES 에서 할 수 있는 일은 가능한 ES 에서 하라
    - 자주 접근하는 DOM 참조를 지역변수에 복사할 것
    - HTML 컬렉션은 동적으로 반영받고 반영되므로 조심히 다룰 것, 특히 DOM 요소의 리스트(컬렉션)는 배열에 복사해 사용 할 것
    - querySelectorAll() / firstElementChild() 같은 빠른 API 를 활용할 것
    - 리페인트 및 리플로우를 항상 조심하라, 스타일 변경은 한번에 묶어서 처리하고, DOM 트리를 조작할 때는 레이아웃의 흐름에 벗어나 분리해서 작업, 레이아웃 정보는 캐시하여 최소한만 사용할 것
    - 애니메이션 중에는 요소에 절대위치를 지정하라
    - 이벤트 위임을 이용하서 이벤트 핸들러 수를 최소한으로 줄여라


## 4장 알고리즘

__ES는 가용할 수 있는 리소스가 상대적으로 적으며 최적화 테크닉을 염두해 두어야 한다__

    - for / while / do-while 등의 루프문의 성능은 서로 거의 동일하다
    - 하지만 객체의 속성에 대해 모를 경우를 제외하면 for-in 문은 사용하지 마라
    - 루프의 성능은 루프내의 반복 작업과 반복 카운트를 줄이는 것이 최선이다
    - 배열의 length 는 복사하여 지역변수에 저장해 사용하는게 좋다
    - 반복문의 index 를 반대로 줄이는 방법도 좋다
    - switch 문이 if-else 보단 빠르지만 그딴건 집어치우고 보기 쉬운걸 해라
    - 배열을 활용한 참조테이블을 쓰는게 좋다
    - if-else 는 여러개이고 순차적인 값을 비교할 경우 depth 를 두어 나누어 비교하면 더 빠른경우도 있다
    - 브라우저의 콜 스택크기에 따라 ES 가 얼마나 재귀가 가능한지 다르다. StackOverflow 시 모든 작동이 멈춘다
    - 재귀의 Stack Overflow 시 반복문으로 바꾸거나 메모이제이션을 이용한 캐싱을 사용하라

## 5장 문자열과 정규식

### 문자열
__문자열 처리는 유연하되, 확실하게__

    - 문자열을 크고 자주 합치는 경우 IE7 이하 에서는 join 을 이용한 배열 병합을 사용할 것
    - 그 외에는 그냥 + 또는 += 연산자를 쓰고 불필요한 중간 단계의 문자열을 없앨 것


## 6장 응답성 좋은 인터페이스

__ES의 동작과 DOM 의 UI 업데이트는 같은 프로세스에서 돌아가며 한번에 한가지만이 돌아간다__

    - ES 코드가 실행중일 때에는 사용자 UI 는 입력에 반응할 수 없고, 그 반대도 마찬가지이다
    - UI 스레드를 효율적으로 관리한다는 건 ES 가 DOM 에 영향을 미치지 않을 정도로 짧게 실행된다는 말이다
    - 어떤 ES 작업도 100ms 이상의 실행은 안된다, UX 에 큰 악영향을 미친다
    - 브라우저는 ES 가 실행되는 동안 UI 입출력에 다르게 반응한다. ES 가 오래 실행되면 브라우저의 반응이 어떻든 간에, UX 는 혼란스럽고, 단절되어 느껴진다
    - 타이머를 활용해 코드를 나중에 실행하도록 예약할 수 있다
    - 웹 워커를 사용하면 ES 를 UI 스레드 외부에서 실행할 수 있다 (UN-LOCK)

## 7장 AJAX

__고성능의 AJAX 구현을 위해서는 환경과 요구사항을 잘 파악해야한다__

    - JS 파일과 CSS 파일을 하나로 합치거나 MXHR 으로 리퀘스트의 수를 줄이자
    - 덜 중요한 파일은 나중에 불러와 체감 속도를 떨이트리지 않게 개선
    - 브라우저에서 지원하지 않더라도 에러 / 미지원 알림 을 내지 말고, 서버에서 문제를 해결하도록 하자
    - Axios 같은 Ajax 라이브러리를 써야할 때와 직접 Ajax 코드를 작성해야 할 때를 구분할 것
