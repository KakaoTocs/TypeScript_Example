"정적 타입"과 "명시적 타입"은 다르다.

## 아이템 19: 추론 가능한 타입을 사용해 장황한 코드 방지하기

타입을 명시하기 위해 타입 구문을 넣는건 맞지만, 모든 곳에 타입 구문을 넣을 필요 없음
타입스크립트가 타입 추론을 잘 해주기 떄문

타입스크립트는 타입 제약이 강하기에 오히려 타입을 명시한게 나중에 수정해야할 대상이 될 수도 있음
=> 그래서 타입 추론이 힘든 상황에 명시하는게 좋음 (ex: 직점만든 interface, type 등)
함수/메서드, 시그니처에 타입 구문을 포함하지만, 함수 내에 생성된 지역 변수에 는 타입 구문을 안넣음

기본값이 있는경우 타입 추론이 잘됨

객체 리터럴, 함수의 반환 타입에 타입 명시 => 사용자 코드 위치에 오류가 나타나는 것을 방지

## 아이템 20: 다른 타입에는 다른 변수 사용하기

상황에 따라 타입이 바뀌는 변수는 피해야함

## 아이템 21: 타입 넓히기

타입스크립트에서 타입은 '가능한' 값들의 집합이다.

타입 체커는 지정된 단일 값을 가지고, 할당 가능한 값들의 딥합을 유추함, 이런 과정을 넓히기(widening)라고 함

const 사용해 넓히기 제한

타입 추론 강도 제어

1. 명시적 타입 구문 추가
2. 타입 체커에 추가적인 문맥을 제공
3. const 단언문 사용 (ex: const v3 = { x: 1, y: 2 } as const;, const와 전혀 다름

## 아이템 22: 타입 좁히기

타입 좁히기: 타입스크립트가 넓은 타입으로부터 좁은 타입으로 진행하는 과정

* 조건문을 통한 타입 좁히기가 대표적임, 이외에도 instanceof, 속성체크로 타입을 좁 힐 수 있음
* 태그된/구별된 유니온과 사용자 정의 타입 가드로 타입 좁히기 과정을 원할하게 가능

## 아이템 23: 한꺼번에 객체 생성하기

속성을 제각각 추가하지 말고 한꺼번에 객체로 만들어야함.

## 아이템 24: 일관성 있는 별칭 사용하기

요약

* 별칭은 타입스크립트가 타입을 좁히는 것을 방해함.
* 비구조화 문법을 사용해 일관된 이르을 사용하는 것이 좋음
* 함수 호출이 객체 속성의 타입 정제를 무효화 할 수 있음

## 아이템 25: 비동기 코드에는 콜백 대신 async 함수 사용하기

콜백 -> Promise -> async/await

Promise 장점

* 콜백보다는 프로미스 코드를 작성하기 쉽다.
* 콜백보다는 프로미스가 타입을 추론하기 쉽다.

Async/await 장점

* 일반적으로 더 간결하고 직관적인 코드가 된다.
* Async 함수는 항상 프로미스를 반환하도록 강제된다.
* 반동기  코드를 작성하는 것을 막을 수 있다.
* 프로미스 중첩 래핑이 안됨

## 아이템 26: 타입 추론에 문맥이 어떻게 사용되는지 이해하기

타입 추론에 문맥 제공

* 타입 선언하기
* const 사용해 가능한 값에 제한

const / as const

* const는 단지 값이 가리키는 참조가 변하지 않는 얕은(shallow) 상수
* as const는 그 값이 내부까지(deeply) 상수
* 단점: const 정의한 곳에 문제가 있을시 상요한 곳에 오류가 뜸

## 아이템 27: 함수형 기법과 라이브러리로 타입 흐름 유지하기

함수형 프로그래밍 예시... (체이닝)