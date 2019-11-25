# 이터레이션과 for...of 문

## 이터레이션 프로토콜

ES6에서 도입

데이터 컬렉션을 순회하기 위한 프로토콜(미리 약속된 규칙)

1. 이터러블 프로토콜(iterable protocol)
   - 순회 가능한 자료 구조
2. 이터레이터 프로토콜(iterator protocol)
   - 이터러블의 요소를 탐색하기 위한 포인터

이터레이션 프로토콜을 준수한 객체

- for...of 문으로 순회할 수 있음
- Spread 문법의 피연산자가 될 수 있음



### 이터러블

이터러블 프로토콜을 준수한 객체

Symbol.iterator 메소드를 구현하거나 프로토타입 체인에 의해 상속한 객체

- Symbol.iterator 메소드는 이터레이터를 반환

for...of 문에서 순회 및 Spread 문법의 대상으로 사용 가능



배열

- Symbol.iterator 메소드를 소유

- 이터러블 프로토콜을 준수한 이터러블

  ```javascript
  const array = [1, 2, 3];
  console.log(Symbol.iterator in array); // true
  
  for (const item of array) {
    console.log(item);
  }
  ```

일반 객체

- Symbol.iterator 메소드를 소유하지 않음

- 이터러블 프로토콜을 준수한 이터러블이 아님

  ```javascript
  const obj = { a: 1, b: 2 };
  console.log(Symbol.iterator in obj); // false
  
  // TypeError: obj is not iterable
  for (const p of obj) {
    console.log(p);
  }
  ```

- for...of 문에서 순회 및 Spread 문법의 대상으로 사용 불가능

- 이터러블 프로토콜을 준수하도록 구현하면 이터러블이 됨 (커스텀 이터러블)



### 이터레이터

이터레이터 프로토콜을 준수한 객체



이터러블 프로토콜을 준수한 이터러블

```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블
const array = [1, 2, 3];
```

- Symbol.iterator 메소드를 소유

- Symbol.iterator 메소드를 호출하면 이터레이터를 반환

  ```javascript
  const iterator = array[Symbol.iterator]();
  ```

- 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 가짐

  ```javascript
  console.log('next' in iterator);
  ```



이터레이터 프로토콜

- next 메소드를 소유

  - 이터러블의 각 요소를 순회하기 위한 포인터 역할

- next 메소드를 호출하면 이터러블을 순회하며 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환

  - value 프로퍼티는 현재 순회 중인 이터러블의 값을 반환
  - done 프로퍼티는 이터러블의 순회 완료 여부를 반환

  ```javascript
  console.log(iterator.next());
  console.log(iterator.next());
  console.log(iterator.next());
  console.log(iterator.next()); 
  ```



### 빌트인 이터러블

ES6에서 제공하는 빌트인 이터러블

- Array

  ```javascript
  const array = [1, 2, 3];
  let iter = array[Symbol.iterator]();
  
  console.log(iter.next());
  console.log(iter.next());
  console.log(iter.next());
  console.log(iter.next());
  
  for (const item of array) {
    console.log(item);
  }
  ```

- String

  ```javascript
  const string = 'hi';
  iter = string[Symbol.iterator]();
  
  console.log(iter.next());
  console.log(iter.next());
  console.log(iter.next());
  
  for (const letter of string) {
    console.log(letter);
  }
  ```

- Map

- Set

- TypedArray(Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array, Int32Array, Uint32Array, Float32Array, Float64Array)

- DOM data structure(NodeList, HTMLCollection)

- Arguments 

  ```javascript
  (function () {
    iter = arguments[Symbol.iterator]();
  
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
  
    for (const arg of arguments) {
      console.log(arg);
    }
  }(1, 2));
  ```



### 이터레이션 프로토콜의 필요성

데이터 소비자(Data consumer)

- for...of 문, spread 연산자, 디스트럭처링 할당, Map/Set 생성자, 등

- 이터러블을 지원
- 내부에서 Symbol.iterator 메소드를 호출해 이터레이터를 생성
- 이터레이터의 next 메소드를 호출해 이터러블을 순회
- next 메소드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값을 취득



데이터 공급자(Data provider)

- (이터레이션 프로토콜을 준수하는) 이터러블

- 데이터 소스
- 데이터 소비자가 사용하는 다양한 데이터 소스
- 데이터 소비자는 이터레이션 프로토콜만 지원하도록 구현하면 됨



이터레이션 프로토콜

- 다양한 데이터 소스가 하나의 순회 방식을 갖도록 규정
- 데이터 소비자가 효율적으로 다양한 데이터 소스를 사용할 수 있도록 함
- 데이터 소비자와 데이터 소스를 연결하는 인터페이스



## for...of 문

내부적으로 이터레이터의 next 메소드를 호출하여 이터러블을 순회

next 메소드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값을 for...of 문의 변수에 할당

이터레이터 리절트 객체의 done 프로퍼티 값

- false이면 이터러블의 순회를 계속함
- true이면 이터러블의 순회를 중단

```javascript
// 배열
for (const item of ['a', 'b', 'c']) {
  console.log(item);
}

// 문자열
for (const letter of 'abc') {
  console.log(letter);
}

// Map
for (const [key, value] of new Map([['a', '1'], ['b', '2'], ['c', '3']])) {
  console.log(`key : ${key} value : ${value}`);
}

// Set
for (const val of new Set([1, 2, 3])) {
  console.log(val);
}
```



for...of 문이 내부적으로 동작하는 것을 for 문으로 표현

```javascript
// 이터러블
const iterable = [1, 2, 3];

// 이터레이터
const iterator = iterable[Symbol.iterator]();

for (;;) {
  // next 메소드를 호출하여 이터러블을 순회
  const res = iterator.next();

  // 이터레이터 리절트 객체의 done 프로퍼티가 true가 될 때까지 반복
  if (res.done) break;

  console.log(res);
}
```



## 커스텀 이터러블

### 커스텀 이터러블 구현

일반 객체

- 이터러블이 아님
- Symbol.iterator 메소드를 소유하지 않음
- 이터러블 프로토콜을 준수하지 않음
- for...of 문으로 순회할 수 없음

```javascript
const obj = { a: 1, b: 2 };

console.log(Symbol.iterator in obj); // false

// TypeError: obj is not iterable
for (const p of obj) {
  console.log(p);
}
```



일반 객체가 이터레이션 프로토콜을 준수하도록 구현하면 이터러블이 됨

```javascript
const fibonacci = {
  // Symbol.iterator 메소드를 구현
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1];
    const max = 10;

    return {
      // fibonacci 객체를 순회할 때마다 next 메소드가 호출
      next() {
        [pre, cur] = [cur, pre + cur];
        return {
          value: cur,
          done: cur >= max
        };
      }
    };
  }
};

// 외부에서 값을 전달할 방법이 없음
for (const num of fibonacci) {
  console.log(num);
}
```



Symbol.iterator 메소드

- next 메소드를 갖는 이터레이터를 반환

next 메소드

- done과 value 프로퍼티를 갖는 이터레이터 리절트 객체를 반환

for...of 문

- done 프로퍼티가 true가 될 때까지 반복
- done 프로퍼티가 true가 되면 반복 중지



이터러블

- for...of 문, spread 문법, 디스트럭처링 할당, Map과 Set의 생성자에도 사용됨

  ```javascript
  // spread 문법
  const arr = [...fibonacci];
  console.log(arr);
  
  // 디스트럭처링
  const [first, second, ...rest] = fibonacci;
  console.log(first, second, rest);
  ```

  

### 이터러블을 생성하는 함수

```javascript
// 이터러블의 최대 순회수를 전달받아 이터러블을 반환하는 함수
const fibonacciFunc = function (max) {
  let [pre, cur] = [0, 1];

  return {
    // Symbol.iterator 메소드를 구현
    [Symbol.iterator]() {
      // next 메소드를 소유한 이터레이터를 반환해야 함
      // next 메소드는 이터레이터 리절트 객체를 반환
      return {
        // fibonacci 객체를 순회할 때마다 next 메소드가 호출
        next() {
          [pre, cur] = [cur, pre + cur];
          return {
            value: cur,
            done: cur >= max
          };
        }
      };
    }
  };
};

// 이터러블을 반환하는 함수에 이터러블의 최대값을 외부에서 전달
for (const num of fibonacciFunc(10)) {
  console.log(num);
}
```



### 이터러블이면서 이터레이터인 객체를 생성하는 함수

이터레이터를 생성하려면, 이터러블의 Symbol.iterator 메소드를 호출해야 함

이터러블이면서 이터레이터인 객체를 생성하려면, Symbol.iterator 메소드를 호출하지 않아도 됨

```javascript
// 이터러블이면서 이터레이터인 객체를 반환하는 함수
const fibonacciFunc = function (max) {
  let [pre, cur] = [0, 1];

  // Symbol.iterator 메소드와 next 메소드를 소유한 이터러블이면서 이터레이터인 객체를 반환
  return {
    // Symbol.iterator 메소드는 this를 반환하므로 next 메소드를 갖는 이터레이터를 반환
    [Symbol.iterator]() {
      return this;
    },
    // next 메소드는 이터레이터 리절트 객체를 반환
    next() {
      [pre, cur] = [cur, pre + cur];
      return {
        value: cur,
        done: cur >= max
      };
    }
  };
};

// iter는 이터러블이면서 이터레이터
let iter = fibonacciFunc(10);

// iter는 이터레이터
console.log(iter.next());
console.log(iter.next());
console.log(iter.next());

iter = fibonacciFunc(10);

// iter는 이터러블
for (const num of iter) {
  console.log(num); // 1 2 3 5 8
}
```



### 무한 이터러블과 Lazy evaluation(지연 평가)

무한 이터러블을 생성하는 함수를 통해 무한 수열을 표현

```javascript
// 무한 이터러블을 생성하는 함수
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      return { value: cur };
    }
  };
};

// fibonacciFunc 함수는 무한 이터러블을 생성
// Lazy evaluation
// for...of 문(데이터 소비자)이 실행되기 전까지 데이터를 생성하지 않음
// 이터러블을 순회할 때 내부에서 이터레이터의 next 메소드를 호출하고, 이때 데이터가 생성됨
// 데이터가 필요할 때까지 데이터의 생성을 지연하다가 데이터가 필요한 순간 데이터를 생성
for (const num of fibonacciFunc()) {
  if (num > 10000) break;
  console.log(num);
}

// 무한 이터러블에서 3개만을 취득
const [f1, f2, f3] = fibonacciFunc();
console.log(f1, f2, f3);
```



빌트인 이터러블

- 배열, 문자열, Map, Set 등
- 데이터를 모두 메모리에 확보한 다음 동작

이터러블

- 데이터 공급자 역할
- Lazy evaluation(지연 평가)을 통해 값을 생성
  - 평가 결과가 필요할 때까지 평가를 늦추는 기법