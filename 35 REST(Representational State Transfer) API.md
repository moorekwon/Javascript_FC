# REST(Representational State Transfer) API

"RESTful": REST의 기본 원칙을 성실히 지킨 서비스 디자인



## REST API 중심 규칙

REST에서 가장 중요한 기본적인 규칙

1. URI는 정보의 자원을 표현해야 함
   - 리소스명은 명사를 사용
   - get 같은 행위에 대한 표현이 들어가서는 안됨 (예: GET /getTodos/1)
   - 좋은 예: GET /todos/1
2. 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE, 등)로 표현함
   - 좋은 예: DELETE /todos/1



## HTTP Method

5개의 Method를 사용하여 CRUD를 구현

- GET: index/retrieve (모든/특정 리소스를 조회)
- POST: create (리소스를 생성)
- PUT: update all (리소스의 전체를 갱신)
- PATCH: update (리소스의 일부를 갱신)
- DELETE: delete (리소스를 삭제)



## REST API의 구성

REST는 자체 표현 구조로 구성되어 REST API만으로 요청을 이해할 수 있음

3가지 요소로 구성

- Resource(자원): HTTP URI로 표현
- Verb(행위): HTTP Method로 표현
- Representations(표현): HTTP Message Pay Load로 표현



## REST API의 Example

### json-server

```bash
$ mkdir rest-api-exam && cd rest-api-exam
$ npm init -y
$ npm install json-server
```

```json
// db.json 파일을 생성
{
  "todos": [
    { "id": 1, "content": "HTML", "completed": false },
    { "id": 2, "content": "CSS", "completed": true },
    { "id": 3, "content": "Javascript", "completed": false }
  ]
}
```

npm script를 사용해 json-server를 실행

```json
// package.json을 수정
{
  "name": "rest-api-exam",
  "version": "1.0.0",
  "description": "",
  "scripts": {
    "start": "json-server --watch db.json --port 5000"
  },
  "dependencies": {
    "json-server": "^0.15.0"
  }
}
```

json-server를 실행 (포트 5000 사용)

```bash
$ npm start
```



### GET

todos 리소스에서 모든 todo를 조회(index)

```bash
$ curl -X GET http://localhost:5000/todos
[
  {
    "id": 1,
    "content": "HTML",
    "completed": false
  },
  {
    "id": 2,
    "content": "CSS",
    "completed": true
  },
  {
    "id": 3,
    "content": "Javascript",
    "completed": false
  }
]
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', 'http://localhost:5000/todos');
xhr.send();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```

todos 리소스에서 id를 사용해 특정 todo를 조회(retrieve)

```bash
$ curl -X GET http://localhost:5000/todos/1
{
  "id": 1,
  "content": "HTML",
  "completed": false
}
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', 'http://localhost:5000/todos/1');
xhr.send();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```



### POST

todos 리소스에 새로운 todo를 생성

```bash
$ curl -X POST http://localhost:5000/todos -H "Content-Type: application/json" -d '{"id": 4, "content": "Angular", "completed": true}'
{
  "id": 4,
  "content": "Angular",
  "completed": true
}
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('POST', 'http://localhost:5000/todos');
xhr.setRequestHeader('Content-type', 'application/json');
xhr.send(JSON.stringify({ id: 4, content: 'Angular', completed: true }));

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 201) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```



### PUT

특정 리소스의 전체를 갱신

todos 리소스에서 id를 사용해 todo를 특정해 id를 제외한 리소스 전체를 갱신

```bash
$ curl -X PUT http://localhost:5000/todos/4 -H "Content-Type: application/json" -d '{"id": 4, "content": "React", "completed": false}'
{
  "content": "React",
  "completed": false,
  "id": 4
}
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('PUT', 'http://localhost:5000/todos/4');
xhr.setRequestHeader('Content-type', 'application/json');
xhr.send(JSON.stringify({ id: 4, content: 'React', completed: false }));

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```



### PATCH

특정 리소스의 일부를 갱신

todos 리소스의 id를 사용해 todo를 특정해 completed만 true로 갱신

```bash
$ curl -X PATCH http://localhost:5000/todos/4 -H "Content-Type: application/json" -d '{"completed": true}'
{
  "id": 4,
  "content": "React",
  "completed": true
}
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('PATCH', 'http://localhost:5000/todos/4');
xhr.setRequestHeader('Content-type', 'application/json');
xhr.send(JSON.stringify({ completed: true }));

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```



### DELETE

todos 리소스에서 id를 사용해 todo를 특정하고 삭제

```bash
$ curl -X DELETE http://localhost:5000/todos/4
{}
```

```javascript
const xhr = new XMLHttpRequest();
xhr.open('DELETE', 'http://localhost:5000/todos/4');
xhr.send();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if(xhr.status === 200) {
    console.log(xhr.responseText);
  } else {
    console.log("Error!");
  }
};
```

