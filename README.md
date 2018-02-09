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