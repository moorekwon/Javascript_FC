# 7번째 타입 Symbol

## Symbol이란?

ES6에서 새롭게 추가된 7번째 타입

변경 불가능한 원시 타입의 값

주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용

기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가(하위 호환성을 보장)하기 위해 도입



객체의 프로퍼티 키로 사용할 수 있는 값

- 빈 문자열 포함 모든 문자열
- 심볼



## Symbol의 생성

Symbol 함수를 호출해 생성

노출되지 않고 다른 값과 절대 중복되지 않는 유일무이한 값

new 연산자를 사용하지 않음

```javascript
const mySymbol = Symbol();

console.log(mySymbol);
console.log(typeof mySymbol); 


new Symbol(); // TypeError: Symbol is not a constructor
```



Symbol 함수

- 문자열을 인수로 전달할 경우, 문자열은 생성된 심볼 값에 대한 설명으로 디버깅 용도로만 사용 (심볼 값 생성에 어떠한 영향도 안 줌)

  ```javascript
  const mySymbol1 = Symbol('mySymbol');
  const mySymbol2 = Symbol('mySymbol');
  
  console.log(mySymbol1 === mySymbol2); // false
  ```

- 심볼 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않음

  ```javascript
  const mySymbol = Symbol();
  
  console.log(mySymbol + ''); // TypeError: Cannot convert a Symbol value to a string
  console.log(+mySymbol);     // TypeError: Cannot convert a Symbol value to a string
  ```

- 불리언 타입으로는 암묵적으로 타입 변환

  ```javascript
  const mySymbol = Symbol();
  
  console.log(!!mySymbol); // true
  
  // if 문 등에서 존재 확인 가능
  if (mySymbol) console.log('mySymbol is not empty.');
  ```



## Symbol.for 메소드

Symbol 함수

- 호출될 때마다 유일무이한 심볼 값을 생성
- 전역 심볼 레지스트리에서 심볼 값을 검색할 수 있는 키를 지정할 수 없으므로, 전역 심볼 레지스트리에 등록되어 관리되지 않음



Symbol.for 메소드

- (애플리케이션 전역에서 중복되지 않는) 심볼 값을 단 하나만 생성하여 전역 심볼 레지스트리를 통해 공유할 수 있음

- 인수로 전달받은 문자열을 키로 사용해 키와 심볼 값의 쌍들이 저장되어 있는 전역 심볼 레지스트리에서 해당 키와 일치하는 심볼 값을 검색

  - 전역 심볼 레지스트리(global symbo registry): 자바스크립트 엔진이 관리하는 심볼 값 저장소
  - 검색에 성공
    - 새로운 심볼 값을 생성하지 않고 검색된 심볼 값을 반환

  - 검색에 실패
    - 새로운 심볼 값을 생성

- 전역 심볼 레지스트리에 저장된 심볼 값의 키를 추출할 수 있음

- Symbol.for 메소드의 인수로 전달된 키로 전역 Symbol 레지스트리에 저장한 후, 생성된 심볼 값을 반환

  ```javascript
  // 새로운 심볼 값을 생성
  const s1 = Symbol.for('mySymbol');
  // 전역 심볼 레지스트리에 저장된 심볼 값의 키를 추출
  console.log(Symbol.keyFor(s1)); 
  
  // 해당 심볼 값을 반환
  const s2 = Symbol.for('mySymbol');
  console.log(s1 === s2); // true
  ```



## Symbol과 상수

중복될 가능성이 없는 유일무이한 심볼 값으로 상수값 생성

```javascript
const UP = Symbol('up');
const DOWN = Symbol('down');
const LEFT = Symbol('left');
const RIGHT = Symbol('right');

const myDirection = UP;

if (myDirection === UP) {
  console.log('You are going UP.');
}
```



## Symbol과 프로퍼티 키

객체의 프로퍼티 키

- 빈 문자열을 포함하는 모든 문자열 또는 심볼 값
- 동적으로 생성(Computed property name)

심볼 값으로 프로퍼티 키를 동적 생성

- 프로퍼티 키로 사용할 심볼 값에 대괄호 사용

- 다른 프로퍼티 키와 (미래에 추가될 어떤 프로퍼티 키와도) 절대 충돌하지 않음

  ```javascript
  const obj = {
    [Symbol.for('mySymbol')]: 1
  };
  
  console.log(obj[Symbol.for('mySymbol')]); 
  ```



## Symbol과 프로퍼티 은닉

심볼 값으로 동적 생성한 프로퍼티 키로 만든 프로퍼티

- for...in 문이나 Object.keys, Object.getOwnPropertyNames 메소드로 찾을 수 없음

  ```javascript
  const obj = {
    [Symbol('mySymbol')]: 1
  };
  
  for (const key in obj) {
    console.log(key);
  }
  console.log(Object.keys(obj));
  console.log(Object.getOwnPropertyNames(obj));
  ```

- 프로퍼티를 숨길 수 있음

  - 완전하게 숨길 수 있는 것은 아님

  - Object.getOwnPropertySymbols 메소드(ES6)를 사용해 프로퍼티를 찾음

    ```javascript
    console.log(Object.getOwnPropertySymbols(obj));
    
    const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
    console.log(obj[symbolKey1]);
    ```



## Symbol과 표준 빌트인 객체 확장

표준 빌트인 객체에 사용자 정의 메소드를 직접 추가

```javascript
Array.prototype.sum = function () {
  return this.reduce((p, c) => p + c, 0);
};

console.log([1, 2].sum());
```

- 일반적으로 권장하지 않음
  - 개발자가 직접 추가한 메소드와 미래에 표준 사양으로 추가될 메소드가 이름이 중복될 수 있음
  - 표준 빌트인 메소드를 사용자 정의 메소드가 덮어쓸 경우 문제
  - Array.prototype은 읽기 전용으로 사용하는 것이 좋음

심볼 값으로 프로퍼티 키 생성

```javascript
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((p, c) => p + c, 0);
};

console.log([1, 2][Symbol.for('sum')]()); 
```

- 안전하게 표준 빌트인 객체를 확장
  - 표준 빌트인 객체의 기존 프로퍼티 키와 충돌하지 않음
  - 버전이 올라감에 따라 추가될지 모르는 어떤 프로퍼티 키와도 충돌할 위험 없음



## Well known Symbol

자바스크립트가 기본 제공하는 빌트인 심볼 값

Symbol 함수의 프로퍼티에 할당되어 있음

자바스크립트 엔진의 내부 알고리즘에 사용됨



빌트인 이터러블(iterable)

- 배열, String 객체, arguments 객체와 같이 for...of 문으로 순회 가능

- 문법의 피연산자가 될 수 있는 객체

- Well-known Symbol인 Symbol.iterator를 키로 갖는 메소드를 가짐

- 자바스크립트가 기본 제공하는 빌트인 이터러블 -> 프로퍼티 키가 Symbol.iterator인 메소드

  - Array -> Array.prototype[Symbol.iterator]
  - String -> String.prototype[Symbol.iterator]
  - Map -> Map.prototype[Symbol.iterator]
  - Set -> Set.prototpye[Symbol.iterator]
  - TypeArray -> TypedArray.prototype[Symbol.iterator]
  - arguments -> arguments[Symbol.iterator]
  - DOM 컬렉션 -> NodeList.prototype[Symbol.iterator], HTMLCollection.prototype[Symbol.iterator]

- 이터레이션 프로토콜을 준수

  - Symbol.iterator 메소드를 호출하면 이터레이터를 반환하도록 규정(ECMAScript)
  - (빌트인 이터러블이 아닌) 일반 객체를 이터러블처럼 동작하도록 구현하고 싶으면 이 규정을 따르면 됨
  - Symbol.iterator를 키로 갖는 메소드를 객체에 추가하고 이터레이터를 반환하도록 구현하면 그 객체는 이터러블이 됨

  ```javascript
  const iterable = {
    // 이터러블 프로토콜을 준수
    [Symbol.iterator]() {
      let cur = 1;
      const max = 5;
      // next 메소드를 소유한 이터레이터를 반환
      return {
        next() {
          return {
            value: cur++,
            done: cur > max + 1
          };
        }
      };
    }
  };
  
  for (const num of iterable) {
    console.log(num);
  }
  ```