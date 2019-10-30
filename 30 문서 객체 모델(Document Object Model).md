# 문서 객체 모델(Document Object Model)

## DOM (Document Object Model)

브라우저의 렌더링 엔진이 웹 문서를 로드한 후, 파싱하여 문서를 브라우저가 이해할 수 있는 구조로 구성하여 메모리에 적재

모든 요소와 요소의 어트리뷰트, 텍스트를 각각의 객체로 만들고, 이들 객체를 (부자 관계를 표현할 수 있는) 트리 구조로 구성

자바스크립트를 통해 동적으로 변경 가능 (변경된 DOM은 렌더링에 반영)

프로그래밍 언어가 자신에 접근하고 수정할 수 있는 방법을 제공

HTML, ECMAScript에서 정의한 표준이 아닌 별개의 W3C의 공식 표준



기능

1. HTML 문서에 대한 모델 구성
   - 브라우저는 HTML 문서를 로드한 후 해당 문서에 대한 모델을 메모리에 생성
   - 모델은 객체의 트리(DOM tree)로 구성
2. HTML 문서 내의 각 요소에 접근 / 수정
   - 모델 내의 각 객체에 접근하고 수정할 수 있는 프로퍼티와 메소드를 제공
   - DOM이 수정되면 브라우저를 통해 사용자가 보게 될 내용 또한 변경됨



DOM API(Application Programming Interface)

- 웹 문서를 동적으로 변경
- 일반적으로 프로퍼티와 메소드를 갖는 자바스크립트 객체로 제공
- 자바스크립트는 DOM을 통해 웹페이지를 조작할 때 필요한 수단을 제공



## DOM tree

브라우저가 HTML 문서를 로드한 후 파싱하여 생성하는 모델

객체의 트리로 구조화되어 있음

DOM에서 모든 요소, 어트리뷰트, 텍스트는 하나의 객체이며 Document 객체의 자식

요소의 중첩관계는 객체의 트리로 구조화해 부자관계를 표현



진입점(Entry point): document 객체 (문서 노드)

최종점: 요소의 텍스트를 나타내는 객체 (텍스트 노드)



구성 노드

- 문서 노드(Document Node)
  - 트리의 최상위에 존재
  - 각각 요소, 어트리뷰트, 텍스트 노드에 접근할 때 문서 노드를 통해야 함
  - DOM tree에 접근하기 위한 시작점(entry point)
- 요소 노드(Element Node)
  - HTML 요소를 표현
  - 중첩에 의해 부자 관계를 가지며, 이 부자 관계를 통해 정보를 구조화
  - 문서의 구조를 서술
  - 어트리뷰트, 텍스트 노드에 접근할 때 먼서 요소 노드를 찾아 접근해야 함
  - 요소별 특성을 구현하기 위해 HTML Element 객체를 상속한 객체로 구성
- 어트리뷰트 노드(Attribute Node)
  - HTML 요소의 어트리뷰트를 표현
  - 해당 어트리뷰트가 지정된 요소의 자식이 아니라 해당 요소의 일부로 표현
  - 해당 요소 노드를 찾아 접근하면 어트리뷰트를 참조, 수정할 수 있음
- 텍스트 노드(Text Node)
  - HTML 요소의 텍스트를 표현
  - 요소 노드의 자식 (자신의 자식 노드를 가질 수 없음)
  - DOM tree의 최종단



DOM을 통해 웹페이지 조작(manipulate)

1. 조작하고자 하는 요소를 선택 또는 탐색
2. 선택된 요소의 콘텐츠 또는 어트리뷰트를 조작



## DOM Query / Traversing (요소에의 접근)

### 하나의 요소 노드 선택(DOM Query)

document.getElementById(id)

- id 어트리뷰트 값으로 요소 노드를 한 개 선택

- 복수개가 선택된 경우, 첫번째 요소만 반환

- HTMLElement를 상속받은 객체를 반환

  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <style>
        .red  { color: #ff0000; }
        .blue { color: #0000ff; }
      </style>
    </head>
    <body>
      <div>
        <h1>Cities</h1>
        <ul>
          <li id="one" class="red">Seoul</li>
          <li id="two" class="red">London</li>
          <li id="three" class="red">Newyork</li>
          <li id="four">Tokyo</li>
        </ul>
      </div>
    </body>
  </html>
  ```
  
  ```javascript
  const elem = document.getElementById('one');
  elem.className = 'blue';
  
  console.log(elem);
  console.log(elem.__proto__);
  console.log(elem.__proto__.__proto__);
  ```



document.QuerySelector(cssSelector)

- CSS 셀렉터를 사용하여 요소 노드를 한 개 선택

- 복수개가 선택된 경우, 첫번째 요소만 반환

- HTMLElement를 상속받은 객체를 반환

- IE8 이상

  ```javascript
  const elem = document.querySelector('li.red');
  elem.className = 'blue';
  ```




### 여러 개의 요소 노드 선택(DOM Query)

document.getElementsByClassName(class)

- class 어트리뷰트 값으로 요소 노드를 모두 선택

- 공백으로 구분하여 여러 개의 class를 지정할 수 있음

- HTMLColletcion을 반환 (live)

  - 반환값이 복수인 경우, HTMLElement의 리스트를 담아 반환하기 위한 객체로 배열과 비슷한 사용법을 가지고 있지만 배열은 아닌 유사배열
  - 실시간으로 Node의 상태 변경을 반영 (live HTMLCollection)
    - loop가 필요한 경우 주의가 필요

- IE9 이상

  ```javascript
  // 예상대로 동작하지 않음
  const elems = document.getElementsByClassName('red');
  
  // elems.length만큼 loop가 실행됨
  for (let i = 0; i < elems.length; i++) {
    elems[i].className = 'blue';
  }
  
  // 해결 1. 반복문을 역방향으로 돌림
  const elems = document.getElementsByClassName('red');
  
  for (let i = elems.length - 1; i >= 0; i--) {
    elems[i].className = 'blue';
  }
  
  // 해결 2. while 반복문 사용
  const elems = document.getElementsByClassName('red');
  
  // elems에 요소가 남아 있지 않을 때까지 무한반복하기 위해 index를 0으로 고정
  let i = 0;
  while (elems.length > i) {
    elems[i].className = 'blue';
  }
  
  // 해결 3. HTMLCollection을 배열로 변경 (권장)
  const elems = document.getElementsByClassName('red');
  // 배열로 변환된 HTMLCollection은 더이상 live하지 않음
  console.log([...elems]);
  
  [...elems].forEach(elem => elem.className = 'blue');
  
  // 해결 4. querySelectorAll 메소드를 사용해 NodeList(non-live)를 반환
  const elems = document.querySelectorAll('.red');
  
  [...elems].forEach(elem => elem.className = 'blue');
  ```



document.getElementsByTagName(tagName)

- 태그명으로 요소 노드를 모두 선택

- HTMLColletion을 반환 (live)

  ```javascript
  const elems = document.getElementsByTagName('li');
  
  [...elems].forEach(elem => elem.className = 'blue');
  ```



document.querySelectorAll(selector)

- 지정된 CSS 선택자를 사용하여 요소 노드를 모두 선택

- NodeList를 반환 (non-live)

- IE8 이상

  ```javascript
  const elems = document.querySelectorAll('li.red');
  
  [...elems].forEach(elem => elem.className = 'blue');
  ```



### DOM Traversing (탐색)

parentNode

- 부모 노드를 탐색

- HTMLElement를 상속받은 객체를 반환

  ```javascript
  const elem = document.querySelector('#two');
  
  elem.parentNode.className = 'blue';
  ```



firstChilde, lastChild

- 자식 노드를 탐색

- HTMLElement를 상속받은 객체를 반환

- IE9 이상

  - IE를 제외한 대부분의 브라우저들은 요소 사이의 공백 또는 줄바꿈 문자를 텍스트 노드로 취급

  ```javascript
  // 예상대로 동작하지 않음
  const elem = document.querySelector('ul');
  
  elem.firstChild.className = 'blue';
  elem.lastChild.className = 'blue';
  ```

  ```html
  <!-- 해결 1. HTML의 공백을 제거 -->
  <ul><li
    id='one' class='red'>Seoul</li><li
    id='two' class='red'>London</li><li
    id='three' class='red'>Newyork</li><li
    id='four'>Tokyo</li></ul>
  ```

  ```javascript
  // 해결 2. fistElementChild, lastElementChild 프로퍼티 사용
  const elem = document.querySelector('ul');
  
  elem.firstElementChild.className = 'blue';
  elem.lastElementChild.className = 'blue';
  ```



hasChildNodes()

- 자식 노드가 있는지 확인
- Boolean 값을 반환

childeNodes

- 자식 노드의 컬렉션을 반환 (텍스트 요소를 포함한 모든 자식 요소를 반환)
- NodeList를 반환 (non-live)

children

- 자식 노드의 컬렉션을 반환 (자식 요소 중 Element type 요소만 반환)
- HTMLCollection을 반환 (live)
- IE9 이상

```javascript
const elem = document.querySelector('ul');

if (elem.hasChildNodes()) {
  console.log(elem.childNodes);
  console.log(elem.children);
  [...elem.children].forEach(el => console.log(el.nodeType));
}
```



previousSibling, nextSibling

- 형제 노드를 탐색 (text node를 포함한 모든 형제 노드를 탐색)
- HTMLElement를 상속받은 객체를 반환

previousElementSibling, nextElementSibling

- 형제 노드를 탐색 (형제 노드 중 Element type 요소만 반환)
- HTMLElement를 상속받은 객체를 반환
- IE9 이상

```javascript
const elem = document.querySelector('ul');

elem.firstElementChild.nextElementSibling.className = 'blue';
elem.firstElementChild.nextElementSibling.previousElementSibling.className = 'blue';
```



## DOM Manipulation (조작)

### 텍스트 노드에의 접근/수정

요소의 텍스트는 텍스트 노드에 저장되어 있음



텍스트 노드에 접근

1. 해당 텍스트 노드의 부모 노드를 선택 (텍스트 노드는 요소 노드의 자식)
2. firstChild 프로퍼티를 사용하여 텍스트 노드를 탐색
3. 텍스트 노드의 유일한 프로퍼티 nodeValue를 이용하여 텍스트를 취득
4. nodeValue를 이용하여 텍스트를 수정



nodeValue

- 노드의 값을 반환

- 텍스트 노드의 경우 문자열, 요소 노드의 경우 null을 반환

- IE6 이상

  ```javascript
  const one = document.getElementById('one');
  console.dir(one);
  
  // nodeName, nodeType을 통해 노드의 정보를 취득
  console.log(one.nodeName);
  console.log(one.nodeType);
  
  // firstChild 프로퍼티를 사용하여 텍스트 노드를 탐색
  const textNode = one.firstChild;
  
  console.log(textNode.nodeName);
  console.log(textNode.nodeType);
  
  // 노드의 값을 취득
  console.log(textNode.nodeValue);
  // 텍스트를 수정
  textNode.nodeValue = 'Pusan';
  ```

  

### 어트리뷰트 노드에의 접근/수정

어트리뷰트 노드를 조작할 때 사용 가능한 프로퍼티 또는 메소드

className

- class 어트리뷰트의 값을 취득 또는 변경
- 값을 할당하는 경우, class 어트리뷰트가 존재하지 않으면 class 어트리뷰트를 생성하고 지정된 값을 설정
- 값이 여러 개일 경우, 공백으로 구분된 문자열이 반환됨
  - String 메소드 split(' ')를 사용하여 배열로 변경하여 사용

classList

- add, remove, item, toggle, contains, replace 메소드를 제공
- IE10 이상

```javascript
const elems = document.querySelectorAll('li');

[...elems].forEach(elem => {
  if (elem.className === 'red') {
    elem.className = 'blue';
  }
});

[...elems].forEach(elem => {
  if (elem.classList.contains('blue')) {
    elem.classList.replace('blue', 'red');
  }
});
```



id

- id 어트리뷰트의 값을 취득 또는 변경

- 값을 할당하는 경우, id 어트리뷰트가 존재하지 않으면 id 어트리뷰트를 생성하고 지정된 값을 설정

  ```javascript
  const heading = document.querySelector('h1');
  
  console.dir(heading);
  console.log(heading.firstChild.nodeValue);
  
  heading.id = 'heading';
  console.log(heading.id);
  ```



hasAttribute(attribute)

- 지정한 어트리뷰트를 갖고 있는지 검사
- Boolean을 반환
- IE8 이상

getAttribute(attribute)

- 어트리뷰트의 값을 취득
- 문자열을 반환

setAttribute(attribute, value)

- 어트리뷰트와 어트리뷰트 값을 설정
- undefined를 반환

removeAttribute(attribute)

- 지정한 어트리뷰트를 제거
- undefined를 반환

```html
<!DOCTYPE html>
<html>
  <body>
  <input type="text">
  <script>
  const input = document.querySelector('input[type=text]');
  console.log(input);

  if (!input.hasAttribute('value')) {
    input.setAttribute('value', 'hello!');
  }

  console.log(input.getAttribute('value'));

  input.removeAttribute('value');

  console.log(input.hasAttribute('value'));
  </script>
  </body>
</html>
```

```html
<!DOCTYPE html>
<html>
<body>
  <input class="password" type="password" value="123">
  <button class="show">show</button>
  <script>
    const $password = document.querySelector('.password');
    const $show = document.querySelector('.show');

    function makeClickHandler() {
      let isShow = false;
      return function () {
        $password.setAttribute('type', isShow ? 'password' : 'text');
        isShow = !isShow;
        $show.innerHTML = isShow ? 'hide' : 'show';
      };
    }

    $show.onclick = makeClickHandler();
  </script>
</body>
</html>
```



### HTML 콘텐츠 조작(Manipulation)

HTML 콘텐츠를 조작할 때 사용 가능한 프로퍼티 또는 메소드



>  마크업이 포함된 콘텐츠를 추가하는 행위는 [크로스 스크립팅 공격(XSS: Cross-Site Scripting Attacks)](https://namu.wiki/w/XSS)에 취약하므로 주의가 필요하다. 



textContent

- 요소의 텍스트 콘텐츠를 취득 또는 변경 (마크업은 무시됨)

- 요소에 새로운 텍스트를 할당하면 텍스트를 변경할 수 있음

- 순수한 텍스트만 지정해야 함 (마크업을 포함시키면 문자열로 인식돼 그대로 출력)

- IE9 이상

  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <style>
        .red  { color: #ff0000; }
        .blue { color: #0000ff; }
      </style>
    </head>
    <body>
      <div>
        <h1>Cities</h1>
        <ul>
          <li id="one" class="red">Seoul</li>
          <li id="two" class="red">London</li>
          <li id="three" class="red">Newyork</li>
          <li id="four">Tokyo</li>
        </ul>
      </div>
      <script>
      const ul = document.querySelector('ul');
      console.log(ul.textContent);
          
      const one = document.getElementById('one');
      console.log(one.textContent);
          
      one.textContent += ', Korea';
      console.log(one.textContent);
          
      one.textContent = '<h1>Heading</h1>';
      console.log(one.textContent);
      </script>
    </body>
  </html>
  ```



innerText

- 요소의 텍스트 콘텐츠에만 접근 가능
- 사용하지 않는 것이 좋음
  - 비표준
  - CSS에 순종적 (visibility: hidden; 으로 지정돼 있다면 텍스트 반환되지 않음)
  - textContent 프로퍼티보다 느림 (CSS를 고려해야 하기 때문)

innerHTML

- 해당 요소의 모든 자식 요소를 포함하는 모든 콘텐츠를 하나의 문자열로 취득할 수 있음 (마크업을 포함함)

  ```javascript
  const ul = document.querySelector('ul');
  console.log(ul.innerHTML);
  ```

- 마크업이 포함된 새로운 콘텐츠를 지정하면 새로운 요소를 DOM에 추가할 수 있음

  - 크로스 스크립팅 공격(XSS: Cross-Site Scripting Attacks)에 취약

  ```javascript
  const one = document.getElementById('one');
  console.log(one.innerHTML);
  
  one.innerHTML += '<em class="blue">, Korea</em>';
  console.log(one.innerHTML);
  ```

  

### DOM 조작 방식

DOM을 직접 조작

- innerHTML 프로퍼티를 사용하지 않고 새로운 콘텐츠를 추가할 수 있는 방법
- 한 개의 요소를 추가하는 경우 사용

1. 요소 노드 생성 createElement() 메소드를 사용해 새로운 요소 노드를 생성
   - 인자로 태그 이름 전달
2. 텍스트 노드 생성 createTextNode() 메소드를 사용해 새로운 텍스트 노드를 생성
   - 경우에 따라 생략 가능 (생략하는 경우, 콘텐츠가 비어 있는 요소가 됨)
3. 생성된 요소를 DOM에 추가
   - appendChild() 메소드를 사용해 생성된 노드를 DOM tree에 추가
   - removeChild() 메소드를 사용에 노드를 DOM tree에서 삭제



creatElement(tagName)

- 태그 이름을 인자로 전달해 요소를 생성
- HTMLElement를 상속받은 객체를 반환

createTextNode(text)

- 텍스트를 인자로 전달해 텍스트 노드를 생성
- Text 객체를 반환

appendChild(Node)

- 인자로 전달한 노드를 마지막 자식 요소로 DOM 트리에 추가
- 추가한 노드를 반환

removeChild(Node)

- 인자로 전달한 노드를 DOM 트리에 제거
- 추가한 노드를 반환

```javascript
const newElem = document.createElement('li');
const newText = document.createTextNode('Beijing');

newElem.appendChild(newText);

const container = document.querySelector('ul');

container.appendChild(newElem);

const removeElem = document.getElementById('one');

container.removeChild(removeElem);
```



### insertAdjacentHTML()

insertAdjacentHTML(position, string)

- 인자로 전달한 텍스트를 HTML로 파싱하고, 그 결과로 생성된 노드를 DOM 트리의 지정된 위치에 삽입

- 첫번째 인자: 삽입 위치

  - 올 수 있는 값: 'beforebegin', 'afterbegin', 'beforeend', 'afterend'

    ```javascript
    const one = document.getElementById('one');
    
    one.insertAdjacentHTML('beforeend', '<em class="blue">, Korea</em>');
    ```

- 두번째 인자: 삽일할 요소를 표현한 문자열



### innerHTML vs. DOM 조작 방식 vs. insertAdjacentHTML()

innerHTML

- 장점
  - DOM 조작 방식에 비해 빠르고 간편
  - 간편하게 문자열로 정의한 여러 요소를 DOM에 추가할 수 있음
  - 콘텐츠를 취득할 수 있음
- 단점
  - XSS 공격에 취약
    - 사용자로부터 입력 받은 콘텐츠(untrusted data: 댓글, 사용자 이름, 등)를 추가할 때 주의
  - 해당 요소의 내용을 덮어쓰기 때문에 비효율적 (HTML을 다시 파싱)

DOM 조작 방식

- 장점
  - 특정 노드 한 개(노드, 텍스트, 데이터, 등)를 DOM에 추가할 때 적합
- 단점
  - innerHTML보다 느리고 더 많은 코드가 필요



insertAdjacentHTML()

- 장점
  - 간편하게 문자열로 정의된 여러 요소를 DOM에 추가할 수 있음
  - 삽입되는 위치를 선정할 수 있음
- 단점
  - XSS 공격에 취약



textContent

- 텍스트를 추가 또는 변경할 때 사용

DOM 조작 방식

- 새로운 요소를 추가 또는 삭제할 때 사용



## style

