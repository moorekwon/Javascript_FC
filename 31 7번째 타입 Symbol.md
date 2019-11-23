# 7번째 타입 Symbol

## Symbol이란?

ES6에서 새롭게 추가된 7번째 타입

변경 불가능한 원시 타입의 값

주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용



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





## Symbol과 프로퍼티 키

## Symbol과 프로퍼티 은닉

## Symbol과 표준 빌트인 객체 확장

## Well known Symbol

