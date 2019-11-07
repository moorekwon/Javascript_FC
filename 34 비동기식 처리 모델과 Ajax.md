# 비동기식 처리 모델과 Ajax

## Ajax (Asynchronous JavaScript and XML)

브라우저에서 웹페이지를 요청하거나 링크를 클릭하면 브라우저와 서버 간 통신에 의해 화면 갱신이 발생



서버

- 요청받은 페이지(HTML)를 반환 (HTML에서 로드하는 CSS, JavaScript 파일들도 같이)
- 클라이언트의 요청에 따라 정적인 파일을 반환하거나 서버 사이드 프로그램이 만들어낸 파일/데이터를 반환할 수 있음
- 웹페이지 반환

클라이언트(브라우저)

- 서버로부터 반환된 웹페이지를 렌더링해 화면에 표시

Ajax

- 자바스크립트를 이용해 비동기적으로 서버와 브라우저가 데이터를 교환할 수 있는 통신 방식
- 갱신이 필요한 페이지 일부만 로드하여 동일한 효과를 봄
- 빠른 퍼포먼스와 부드러운 화면 표시 효과



## JSON (JavaScript Object Notation)

클라이언트와 서버 간 데이터 교환을 위한 규칙, 즉 데이터 포맷

일반 텍스트 포맷보다 효과적인 데이터 구조화가 가능

XML 포맷보다 가볍고 사용하기 간편하며 가독성이 좋음

자바스크립트의 객체 리터럴과 매우 흡사하나, 순수한 텍스트로 구성된 규칙이 있는 데이터 구조

```javascript
// 키는 반드시 큰따옴표로 둘러쌈
{
  "name": "Lee",
  "gender": "male",
  "age": 20,
  "alive": true
}
```



### JSON.stringify

객체를 JSON 형식의 문자열로 변환

```javascript
const o = { name: 'Lee', gender: 'male', age: 20 };

const strObject = JSON.stringify(o);
console.log(typeof strObject, strObject);

const strPrettyObject = JSON.stringify(o, null, 2);
console.log(typeof strPrettyObject, strPrettyObject);

function filter(key, value) {
  return typeof value === 'number' ? undefined : value;
}

const strFilteredObject = JSON.stringify(o, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);

const arr = [1, 5, 'false'];

const strArray = JSON.stringify(arr);
console.log(typeof strArray, strArray);

function replaceToUpper(key, value) {
  return value.toString().toUpperCase();
}

const strFilteredArray = JSON.stringify(arr, replaceToUpper);
console.log(typeof strFilteredArray, strFilteredArray);
```



### JSON.parse

JSON 데이터를 가진 문자열을 (배열) 객체로 변환

내장 객체 JSON의 static 메소드



>  배열의 요소가 객체인 경우 배열의 요소까지 객체로 변환한다. 



역직렬화(Deserializing)

- 서버로부터 브라우저로 전송된 문자열의 JSON 데이터를 객체로서 사용하기 위해 객체화

```javascript
const o = { name: 'Lee', gender: 'male', age: 20 };

const strObject = JSON.stringify(o);
console.log(typeof strObject, strObject);

const arr = [1, 5, 'false'];

const strArray = JSON.stringify(arr);
console.log(typeof strArray, strArray);

const obj = JSON.parse(strObject);
console.log(typeof obj, obj);

const objArray = JSON.parse(strArray);
console.log(typeof objArray, objArray); 
```



## XMLHttpRequest

브라우저는 XMLHttpRequest 객체를 이용해 Ajax 요청을 생성하고 전송

서버가 브라우저의 요청에 대해 응답을 반환하면 같은 XMLHttpRequest 객체가 그 결과를 처리

response가 클라이언트에 도달했는지 추적할 수 있는 프로퍼티(XMLHttpRequest.readyState)를 제공



### Ajax request

```javascript
// Ajax 요청 처리
const xhr = new XMLHttpRequest();

// 비동기 방식으로 Request 오픈
xhr.open('GET', '/users');
// Request 전송
xhr.send();
```



#### XMLHttpRequest.open

XMLHttpRequest 객체의 인스턴스를 생성

서버로의 요청을 준비



XMLHttpRequest.open(method, url[, async])

- method: HTTP method("GET", "POST", "PUT", "DELETE", 등)
- url: 요청 보낼 url
- async: 비동기 조작 여부. default는 true, 비동기 방식으로 동작



#### XMLHttpRequest.send

준비된 요청을 서버에 전달



기본적으로 서버로 전송하는 데이터

- GET, POST 메소드에 따라 전송 방식이 다름
  - GET 메소드: URL의 일부분인 쿼리문자열(query string)로 데이터를 서버로 전송
  - POST 메소드: 데이터(페이로드)를 Request Body에 담아 전송



request body에 담아 전송할 인수를 전달할 수 있음

요청 메소드가 GET인 경우, send 메소드의 인수는 무시되고 request body는 null로 설정



#### XMLHttpRequest.setRequestHeader

HTTP Request Header의 값을 설정

반드시 XMLHttpRequest.open 메소드 호출 이후에 호출



자주 사용하는 Request Header

- Content-type

  - request body에 담아 전송할 데이터의 MIME-type의 정보를 표현

  - 자주 사용되는 MIME-type

    - text 타입: text/plain, text/html, text/css, text/javascript
    - Application 타입: application/json, application/x-www-form-urlencode
    - File을 업로드하기 위한 타입: multipart/formed-data

  - request body에 담아 서버로 전송할 데이터의 MIME-type을 지정

    ```javascript
    // json으로 전송
    xhr.open('POST', '/users');
    xhr.setRequestHeader('Content-type', 'application/json');
    
    const data = { id: 3, title: 'JavaScript', author: 'Park', price: 5000};
    
    xhr.send(JSON.stringify(data));
    
    
    // x-www-form-urlencoded으로 전송하는 경우
    xhr.open('POST', '/users');xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    
    const data = { title: 'JavaScript', author: 'Park', price: 5000};
    
    xhr.send(Object.keys(data).map(key => `${key}=${data[key]}`).join('&'));
    ```

- Accept

  - HTTP 클라이언트가 서버에 요청할 때 서버가 센드백할 데이터의 MIME-type을 Accept로 지정

  - 서버가 센드백할 데이터의 MIME-type을 지정

    ```javascript
    xhr.setRequestHeader('Accept', 'application/json');
    ```

  - Accept 헤더를 설정하지 않으면, send 메소드가 호출될 때 Accept 헤더가 ```*/*```으로 전송



### Ajax response

```javascript
// Ajax 응답 처리
const xhr = new XMLHttpRequest();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;
    
  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log('Error!');
  }
};
```

XMLHttpRequest.send 메소드를 통해 서버에 Request를 전송

서버는 Response를 반환하지만, 언제 클라이언트에 도달하 지는 알 수 없음



XMLHttpRequest.onreadystatechange

- Response가 클라이언트에 도달해 발생된 이벤트를 감지하고 콜백 함수를 실행
- 이벤트는 Request에 어떤 변화가 발생(XMLHttpRequest.readyState가 변경)한 경우 발생
- XMLHttpRequest.readyState 프로퍼티가 변경될 때마다 콜백 함수(이벤트 핸들러)를 호출

XMLHttpRequest.readyState

- response가 클라이언트에 도달했는지 추적할 수 있는 프로퍼티
- 값
  - 0: UNSENT, XMLHttpRequest.open() 메소드 호출 이전
  - 1: OPENED, XMLHttpRequest.open() 메소드 호출 완료
  - 2: HEADERS_RECEIVED, XMLHttpRequest.send() 메소드 호출 완료
  - 3: LOADING, 서버 응답 중(XMLHttpRequest.responseText 미완성 상태)
  - 4: DONE, 서버 응답 완료 (이후 XMLHttpRequest.status가 200(정상 응답)임을 확인하고, (정상인 경우) XMLHttpRequest.responseText를 취득해 서버가 전송한 데이터를 담음)

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'data/test.json');
xhr.send();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log('Error!');
  }
};
```



## Web Server

클라이언트(브라우저)로부터 HTTP 요청을 받아들이고 웹 페이지(HTML)를 반환하는 컴퓨터 프로그램

Ajax는 웹서버와의 통신이 필요



## Ajax 예제

### Load HTML

HTML은 Ajax를 이용해 웹페이지에 추가하지 가장 손쉬운 데이터 형식 (별도 작업없이 전송받은 데이터를 DOM에 추가하면 됨)

```html
<!-- 루트 폴더(webserver-express/public)/loadhtml.html -->
<body>
  <div id="content"></div>

  <script>
    const xhr = new XMLHttpRequest();
    xhr.open('GET', 'data/data.html');
    xhr.send();

    xhr.onreadystatechange = function () {
      if (xhr.readyState !== XMLHttpRequest.DONE) return;

      if (xhr.status === 200) {
        console.log(xhr.responseText);

        document.getElementById('content').innerHTML = xhr.responseText;
      } else {
        console.log(`[${xhr.status}] : ${xhr.statusText}`);
      }
    };
  </script>
</body>
```

```html
<!-- 루트 폴더(webserver-express/public)/data/data.html -->
<div id="tours">
  <h1>Guided Tours</h1>
  <ul>
    <li class="usa tour">
      <h2>New York, USA</h2>
      <span class="details">$1,899 for 7 nights</span>
      <button class="book">Book Now</button>
    </li>
    <li class="europe tour">
      <h2>Paris, France</h2>
      <span class="details">$2,299 for 7 nights</span>
      <button class="book">Book Now</button>
    </li>
    <li class="asia tour">
      <h2>Tokyo, Japan</h2>
      <span class="details">$3,799 for 7 nights</span>
      <button class="book">Book Now</button>
    </li>
  </ul>
</div>
```



### Load JSON

서버로부터 브라우저로 전송된 JSON 데이터는 문자열

역직렬화를 통해 문자열을 객체화

역직렬화를 위해 내장 객체 JSON의 static 메소드인 JSON.parse()를 사용

```html
<!-- 루트 폴더(webserver-express/public)/loadjson.html -->
<body>
  <div id="content"></div>

  <script>
    var xhr = new XMLHttpRequest();

    xhr.open('GET', 'data/data.json');
    xhr.send();

    xhr.onreadystatechange = function () {
      if (xhr.readyState !== XMLHttpRequest.DONE) return;

      if (xhr.status === 200) {
        console.log(xhr.responseText);

        responseObject = JSON.parse(xhr.responseText);

        let newContent = '<div id="tours"><h1>Guided Tours</h1><ul>';

        responseObject.tours.forEach(tour => {
          newContent += `<li class="${tour.region} tour">
              <h2>${tour.location}</h2>
              <span class="details">${tour.details}</span>
              <button class="book">Book Now</button>
            </li>`;
        });

        newContent += '</ul></div>';

        document.getElementById('content').innerHTML = newContent;
      } else {
        console.log(`[${xhr.status}] : ${xhr.statusText}`);
      }
    };
  </script>
</body>
```

```json
// 루트 폴더(webserver-express/public)/data/data.json
{
  "tours": [
    {
      "region": "usa",
      "location": "New York, USA",
      "details": "$1,899 for 7 nights"
    },
    {
      "region": "europe",
      "location": "Paris, France",
      "details": "$2,299 for 7 nights"
    },
    {
      "region": "asia",
      "location": "Tokyo, Japan",
      "details": "$3,799 for 7 nights"
    }
  ]
}
```



### Load JSONP

동일출처원칙(Same-origin policy)

- 요청에 의해 웹페이지가 전달된 서버와 동일한 도메인의 서버로부터 전달된 데이터는 문제없이 처리 가능하지만, 보안상의 이유로 (포트가) 다른 도메인으로의 요청(크로스 도메인 요청)은 제한



동일출처원칙을 우회하는 방법

1. 웹서버의 프록시 파일

   - 프록시(Proxy): 서버에 원격 서버로부터 데이터를 수집하는 별도의 기능을 추가

2. JSONP

   - script 태그의 원본 주소에 대한 제약은 존재하지 않음

   - 이것을 이용해 다른 도메인의 서버에서 데이터를 수집

   - 자신의 서버에 함수를 정의하고 다른 도메인의 서버에 얻고자 하는 데이터를 인수로 하는 함수 호출문을 로드

     ```html
     <!-- 루트 폴더(webserver-express/public)/loadjsonp.html -->
     <body>
       <div id="content"></div>
     <script>
       function showTours(data) {
         console.log(data);
             
         let newContent = `<div id="tours">
             <h1>Guided Tours</h1>
           <ul>`;
     
         data.tours.forEach(tour => {
           newContent += `<li class="${tour.region} tour">
               <h2>${tour.location}</h2>
               <span class="details">${tour.details}</span>
               <button class="book">Book Now</button>
             </li>`;
         });
     
         newContent += '</ul></div>';
     
         document.getElementById('content').innerHTML = newContent;
       }
       </script>
       <script src='https://poiemaweb.com/assets/data/data-jsonp.js'></script>
     </body>
     ```

     ```javascript
     // https://poiemaweb.com/assets/data/data-jsonp.js
     showTours({
       "tours": [
         {
           "region": "usa",
           "location": "New York, USA",
           "details": "$1,899 for 7 nights"
         },
         {
           "region": "europe",
           "location": "Paris, France",
           "details": "$2,299 for 7 nights"
         },
         {
           "region": "asia",
           "location": "Tokyo, Japan",
           "details": "$3,799 for 7 nights"
         }
       ]
     });
     ```

3. Cross-Origin Resource Sharing

   - HTTP 헤더에 추가적으로 정보를 추가해 브라우저와 서버가 서로 통신해야 한다는 사실을 알게함

   - 최신 브라우저에서만 동작, 서버에 HTTP 헤더를 설정해 줘야 함

   - Node.js로 구현한 서버의 경우, CORS package를 사용하여 간단하게 구현 가능

     ```javascript
     const express = require('express')
     const cors = require('cors')
     const app = express()
     
     app.use(cors())
     
     app.get('/products/:id', function (req, res, next) {
       res.json({msg: 'This is CORS-enabled for all origins!'})
     })
     
     app.listen(80, function () {
       console.log('CORS-enabled web server listening on port 80')
     })
     ```

