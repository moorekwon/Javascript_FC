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





### 이터레이션 프로토콜의 필요성



## for...of 문

## 커스텀 이터러블



