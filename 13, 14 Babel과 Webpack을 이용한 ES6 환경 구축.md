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

의존 관계에 있는 모듈들을 하나의 자바스크립트 파일로 번들링하는 모듈 번들러

모듈 로더가 필요없음

html 파일에서 script 태그로 다수의 자바스크립트 파일을 로드할 필요없음



Webpack과 Babel을 이용하여 ES6+ 개발 환경 구축

- Webpack이 자바스크립트 파일을 번들링하기 전에 Babel을 로드하여 ES6+ 코드를 ES5 코드로 트랜스파일링하도록 설정
- Sass를 사용하는 경우, Sass 트랜스파일링도 Webpack에서 관리하도록 설정



### Webpack 설치

```bash
# Webpack V4는 webpack-cli를 요구
$ npm install --save-dev webpack webpack-cli
```



설치가 완료된 이후 package.json

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
    "@babel/preset-env": "^7.7.1",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  }
}
```



### babel-loader

Webpack이 모듈을 번들링할 때 Babel을 사용하여 트랜스파일링하도록 설치

```bash
$ npm install --save-dev babel-loader
```



npm script를 변경하여 Babel 대신 Webpack을 실행하도록 수정

scripts 변경된 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    "build": "webpack -w"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/plugin-proposal-class-properties": "^7.7.0",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  }
}
```



### webpack.config.js

Webpack이 실행될 때 참조하는 설정 파일

프로젝트 루트에 생성

```javascript
// 작성
const path = require('path');

module.exports = {
  // enntry file
  entry: './src/js/main.js',
  // 컴파일 + 번들링된 js 파일이 저장될 경로와 이름 지정
  output: {
    path: path.resolve(__dirname, 'dist/js'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [
          path.resolve(__dirname, 'src/js')
        ],
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties']
          }
        }
      }
    ]
  },
  devtool: 'source-map',
  // https://webpack.js.org/concepts/mode/#mode-development
  mode: 'development'
};
```



Webpack을 실행하여 트랜스파일링 및 번들링을 실행

- 트랜스파일링은 Babel이 실행
- 번들링은 Webpack이 실행

```bash
$ npm run build
```



bundle.js

- (실행 결과) dist/js 폴더에 생성

- main.js, lib.js 모듈이 하나로 번들링된 결과물



index.html을 수정하고 브라우저에서 실행

```html
<!DOCTYPE html>
<html>
<body>
  <script src="./dist/js/bundle.js"></script>
</body>
</html>
```



### babel-polyfill

Babel을 사용해 트랜스파일링해도 브라우저가 지원하지 않는 코드가 남아 있을 수 있음

오래된 브라우저에도 ES6+에서 새롭게 추가된 객체나 메소드를 사용하기 위해 설치

개발 환경에서만 사용하는 것이 아니라 실제 환경에서도 사용해야 하므로 --save-dev 옵션으로 개발 설치를 하지 않도록 함



src/js/main.js를 수정

```javascript
import { pi, power, Foo } from './lib';

console.log(pi);
console.log(power(pi, pi));

const f = new Foo();
console.log(f.foo());
console.log(f.bar());

// polyfill이 필요한 코드
console.log(new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 100);
}));

// polyfill이 필요한 코드
console.log(Object.assign({}, { x: 1 }, { y: 2 }));

// polyfill이 필요한 코드
console.log(Array.from([1, 2, 3], v => v + v));
```



다시 트랜스파일링과 번들링을 실행한 후, dist/js/bundle.js 확인

```javascript
...
// 190 line
console.log(new Promise(function (resolve, reject) {
  setTimeout(function () {
    return resolve(1);
  }, 100);
})); // polyfill이 필요한 코드

console.log(Object.assign({}, {
  x: 1
}, {
  y: 2
})); // polyfill이 필요한 코드

console.log(Array.from([1, 2, 3], function (v) {
  return v + v;
}));
...
```

- Promise, Object.assign, Array.from 등 ES5 이하로 대체할 수 없는 기능은 트랜스파일링이 되지 않음
- @babel/polyfill 설치하여 해결



@babel/polyfill 설치

```bash
$ npm install @babel/polyfill
```



설치가 완료된 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    "build": "webpack -w"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/plugin-proposal-class-properties": "^7.7.0",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  },
  "dependencies": {
    "@babel/polyfill": "^7.7.0"
  }
}
```



ES6의 import를 사용하는 경우, 진입점의 선두에서 먼저 폴리필을 로드

```javascript
// src/js/main.js
import "@babel/polyfill";
...
```



webpack을 사용하는 경우, 폴리필을 webpack.config.js 파일의 entry 배열에 추가

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  // entry files
  entry: ['@babel/polyfill', './src/js/main.js'],
  ...
```



webpack.config.js 파일 수정 후 폴리필 반영

```bash
$ npm run build
```



### Sass 컴파일

Webpack을 통해 Sass를 컴파일



#### 컴파일된 CSS를 bundle.js 파일에 포함시키는 방법

필요한 패키지 설치

- node-sass
  - node.js 환경에서 사용할 수 있는 Sass 라이브러리
  - 실제로 Sass를 css로 컴파일
- style-loader, css-loader, sass-loader
  - Webpack 플러그인

```bash
$ npm install node-sass style-loader css-loader sass-loader --save-dev
```



설치가 완료된 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    "build": "webpack -w"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/plugin-proposal-class-properties": "^7.7.0",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "css-loader": "^3.2.0",
    "node-sass": "^4.13.0",
    "sass-loader": "^8.0.0",
    "style-loader": "^1.0.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  },
  "dependencies": {
    "@babel/polyfill": "^7.7.0"
  }
}
```



webpack.config.js 파일을 수정

```javascript
const path = require('path');

module.exports = {
  // entry files
  entry: ['@babel/polyfill', './src/js/main.js', './src/sass/main.scss'],
  // 컴파일 + 번들링된 js 파일이 저장될 경로와 이름 지정
  output: {
    path: path.resolve(__dirname, 'dist/js'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [
          path.resolve(__dirname, 'src/js')
        ],
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties']
          }
        }
      },
      {
        test: /\.scss$/,
        use: [
          "style-loader", // creates style nodes from JS strings
          "css-loader",   // translates CSS into CommonJS
          "sass-loader"   // compiles Sass to CSS, using Node Sass by default
        ],
        exclude: /node_modules/
      }
    ]
  },
  devtool: 'source-map',
  // https://webpack.js.org/concepts/mode/#mode-development
  mode: 'development'
};
```



테스트를 위해 3개의 Sass 파일을 src/sass 폴더와 src/sass/partials 폴더에 추가

```scss
// src/sass/main.scss
@import "partials/vars";
@import "partials/body";
```

```scss
// src/sass/partials/_vars.scss
$font_color: #333;
$font_family: Arial, sans-serif;
$font_size: 16px;
$line_height: percentage(20px / $font_size);
```

```scss
// src/sass/partials/_body.scss
body {
  color: $font_color;

  // Property Nesting
  font: {
    size: $font_size;
    family: $font_family;
  }

  line-height: $line_height;
}
```



다시 명령 실행

```bash
$ npm run build
```



CSS가 적용되는 것을 확인하기 위해 index.html 수정

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="./dist/js/bundle.js"></script>
  </head>
<body>
  Hello world!
</body>
</html>
```

- 컴파일된 CSS는 bundle.js에 포함되어 있음



#### 컴파일된 CSS를 별도의 CSS 파일로 분리하는 방법

Sass 파일이 방대해지면 자바스크립트 파일에서 분리하는 것이 효율적일 수 있음

bundle.js 파일에 컴파일된 css를 포함시키지 말고 별도의 css 파일로 분리해서 하나의 파일로 번들링

사용하는 플러그인은 mini-css-extract-plugin



mini-css-extract-plugin 설치

```bash
$ npm install --save-dev mini-css-extract-plugin
```



설치가 완료된 이후 package.json

```json
{
  "name": "es6-project",
  "version": "1.0.0",
  "scripts": {
    "build": "webpack -w"
  },
  "devDependencies": {
    "@babel/cli": "^7.7.0",
    "@babel/core": "^7.7.2",
    "@babel/plugin-proposal-class-properties": "^7.7.0",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "css-loader": "^3.2.0",
    "mini-css-extract-plugin": "^0.8.0",
    "node-sass": "^4.13.0",
    "sass-loader": "^8.0.0",
    "style-loader": "^1.0.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10"
  },
  "dependencies": {
    "@babel/polyfill": "^7.7.0"
  }
}
```



webpack.config.js 파일 수정

```javascript
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  // entry files
  entry: ['@babel/polyfill', './src/js/main.js', './src/sass/main.scss'],
  // 컴파일 + 번들링된 js 파일이 저장될 경로와 이름 지정
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'js/bundle.js'
  },
  plugins: [
    // 컴파일 + 번들링 CSS 파일이 저장될 경로와 이름 지정
    new MiniCssExtractPlugin({ filename: 'css/style.css' })
  ],
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [
          path.resolve(__dirname, 'src/js')
        ],
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins: ['@babel/plugin-proposal-class-properties']
          }
        },
        exclude: /node_modules/
      },
      {
        test: /\.scss$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'sass-loader'
        ],
        exclude: /node_modules/
      }
    ]
  },
  devtool: 'source-map',
  // https://webpack.js.org/concepts/mode/#mode-development
  mode: 'development'
};
```



다시 명령 실행

```bash
$ npm run build
```



css 폴더가 생성되고 style.css 파일이 저장됐음

```css
body {
  color: #333;
  font-size: 16px;
  font-family: Arial, sans-serif;
  line-height: 125%;
}
```

- 컴파일되고 하나의 파일로 번들링된 css가 bundle.js 파일에 포함되지 않고 별도 파일로 분리



index.html에서 style.css 파일을 로드

```html
<!DOCTYPE html>
<html>
  <head>
    <link href="./dist/css/style.css" rel="stylesheet"></link>
    <script src="./dist/js/bundle.js"></script>
  </head>
<body>
  Hello world!
</body>
</html>
```