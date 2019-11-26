# Babel과 Webpack을 이용한 ES6 환경 구축

에버그린 브라우저(Evergreen browser)

- 사용자의 업데이트 없이도 최신 버전으로 자동 업데이트를 수행하는 모던 브라우저
- 크롬, 사파리, 파이어폭스 등
- ES6 지원 비율 약 98%

인터넷 익스플로러(IE)

- ES6 지원 비율 약 11%



최신 사양으로 작성된 코드를 구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축해야 함

아직까지는 ES6 모듈 기능보다는 Webpack 등 모듈 번들러를 사용하는 것이 일반적

- 구형 브라우저는 ES6 모듈을 지원하지 않음
- ES6 모듈 기능을 사용하더라도 트랜스파일링이나 번들링이 필요
  - 트랜스파일러 Babel과 모듈 번들러 Webpack을 이용
- 아직 지원하지 않는 기능이 있음



## Babel

### Babel이란?

최신 사양의 자바스크립트 코드를 IE나 구형 브라우저에서도 동작하는 ES5 이하의 코드로 변환할 수 있음

```javascript
// ES6 화살표 함수와 ES7 지수 연산자
// IE와 다른 구형 브라우저에서는 지원하지 않을 수 있음
[1, 2, 3].map(n => n ** n);

// ES5
// Babel을 사용하여 ES5 이하의 버전으로 변환 가능
"use strict";

[1, 2, 3].map(function (n) {
  return Math.pow(n, n);
});
```



### Babel CLI 설치

npm을 사용하여 Babel CLI (로컬로) 설치

```bash
# 프로젝트 폴더 생성
$ mkdir es6-project && cd es6-project

# package.json 생성
$ npm init -y

# babel-core, babel-cli 설치
$ npm install --save-dev @babel/core @babel/cli
```



설치가 완료된 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2"
  }
}
```



### babelrc 설정 파일 작성

@babel/peset-env

- Babel 프리셋

- Babel을 사용하기 위해 설치 필요
- 함께 사용되어야 하는 Babel 플러그인을 모아둔 것
- 필요한 플러그인들을 프로젝트 지원 환경에 맞춰서 동적으로 결정해 줌
  - 프로젝트 지원 환경은 Browserslist 형식
  - .browserslistrc 파일에 상세히 설정할 수 있음
  - 프로젝트 지원 환경 설정 작업을 생략하면 기본값으로 설정



Babel이 제공하는 공식 Babel 프리셋

- @babel/preset-env
- @babel/preset-flow
- @babel/preset-react
- @babel/preset-typescript



기본 설정 (모든 ES6+ 코드를 변환)

```bash
# env preset 설치
$ npm install --save-dev @babel/preset-env
```



설치가 완료된 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/preset-env": "^7.7.1"
  }
}
```



(설치가 완료되었으면) 프로젝트 루트에 .babelrc 파일을 생성

```json
// 작성
// 지금 설치한 @babel/preset-env를 사용하겠다는 의미
{
  "presets": ["@babel/preset-env"]
}
```



### 트랜스파일링

npm script를 사용하여 트랜스파일링



package.json 파일에 scripts 추가

```json
// src/js 폴더(타깃 폴더)에 있는 모든 ES6+ 파일들을 트랜스파일링
// 결과물을 dist/js 폴더에 저장
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    // -w: 타깃 폴더에 있는 모든 파일들의 변경을 감지해 자동으로 트랜스파일
    // -d: 트랜스파일링된 결과물이 저장될 폴더를 지정
    "build": "babel src/js -w -d dist/js"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/preset-env": "^7.7.1"
  }
}
```



트랜스파일링을 테스트하기 위해 ES6+ 파일 작성

src/js 폴더를 생성한 후 lib.js와 main.js 추가

```javascript
// src/js/lib.js
// ES6 모듈
export const pi = Math.PI;

export function power(x, y) {
  // ES7: 지수 연산자
  return x ** y;
}

// ES6 클래스
export class Foo {
  // 클래스 필드 정의 제안
  #private = 10;

  foo() {
    const { a, b, ...x } = { ...{ a: 1, b: 2 }, c: 3, d: 4 };
    return { a, b, x };
  }

  bar() {
    return this.#private;
  }
}
```

```javascript
// src/js/main.js
// ES6 모듈
import { pi, power, Foo } from './lib';

console.log(pi);
console.log(power(pi, pi));

const f = new Foo();
console.log(f.foo());
console.log(f.bar());
```



터미널에서 트랜스파일링 실행

```bash
$ npm run build
```

- 제안 단계에 있는 사양에 대한 플러그인을 지원하지 않기 때문에 에러 발생
- 지원하려면 별도의 플러그인을 설치해야 함



### Babel 플러그인

@babel/plugin-proposal-class-properties 설치

```bash
$ npm install --save-dev @babel/plugin-proposal-class-properties
```



설치 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    "build": "babel src/js -w -d dist/js"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/plugin-proposal-class-properties": "^7.7.0",
    "@babel/preset-env": "^7.7.1"
  }
}
```



설치한 플러그인을 .babelrc 파일에 추가

```javascript
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```



다시 터미널에서 트랜스파일링 실행

```bash
npm run build
```

- (성공하면) 프로젝트 루트에 dist/js 폴더가 자동 생성
- 트랜스파일링된 main.js와 lib.js가 저장됨



트랜스파일링된  main.js 실행

```bash
$ node dist/js/main
```



### 브라우저에서 모듈 로딩 테스트

ES5로 트랜스파일링됨

- ES6+에서 새롭게 추가된 기능

- 현재 제안 상태에 있는 "클래스 필드 정의 제안"
- ES6 모듈의 import와 export 키워드



src/js/main.js가 Babel에 의해 트랜스파일링된 결과

```javascript
// dist/js/main.js
"use strict";

var _lib = require("./lib");

console.log(_lib.pi);
console.log((0, _lib.power)(_lib.pi, _lib.pi));
var f = new _lib.Foo();
console.log(f.foo());
console.log(f.bar());
```

- 모듈 기능은 node.js 환경에서 동작
- node.js가 기본 지원하는 CommonJS 방식의 module loading system에 따라 Babel이 모듈을 트랜스파일링
- 브라우저는 CommonJS 방식의 module loading system(require 함수)을 지원하지 않음
  - 위 결과를 그대로 브라우저에서 실행하면 에러 발생



프로젝트 루트 폴더에 index.html을 작성하여 트랜스파일링된 자바스크립트 파일을 브라우저에서 실행

```html
<!DOCTYPE html>
<html>
<body>
  <script src="dist/js/lib.js"></script>
  <script src="dist/js/main.js"></script>
</body>
</html>
```

- 브라우저 실행하면 에러 발생 (브라우저의 ES6 모듈 기능을 사용하는 것에 문제)
  - Webpack을 통해 해결



## Webpack

### Webpack이란?





### Webpack 설치

### babel-loader

### webpack.config.js

### babel-polyfill

### Sass 컴파일

