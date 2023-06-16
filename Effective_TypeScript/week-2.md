## 아이템 10: 객체 래퍼 타입 피하기

### JS 기본형 값

기본형은 불변(immutable)이고, 메서드를 가지지 않는 점이 객체와 구분됨

* string: 래퍼 객체(String)
* number: 래퍼 객체(Number)
* boolean: 래퍼 객체(Boolean)
* null: 래퍼 객체(없음)
* undefined: 래퍼 객체(없음)
* symbol: 래퍼 객체(Symbol), ES2015에서 추가l
* bigint: 래퍼 객체(BigInt), 추가 확정 단계

자바스크립트는 기본형과 객체 타입을 자유롭게 변환함 (= 몽키-패치(monkey-patch))
(ex: string 에는 `charAt`메서드가 없지만, 사용시 String객체로 변환함)
몽키-패치는 런타임에 프로그램의 어떤 기능을 수정해서 사용하는 기법을 의미함. JS에서는 주로 프로토타입을 변경하는 것이 대표적

기본형과 래퍼 객체를 잘못 선언하면 동작 하는 것처럼 보일수 도 있고, string에 String 전달시 문제 발생
=> 객체 래퍼에 기본형 타입 할당 가능, 기본형 타입에 객체 래퍼 할당 불가능

객체 래퍼 타입 사용은 지양...

## 아이템 11: 잉여 속성 체크의 한계 인지하기

TS는 타입이 명시된 변수에 객체 리터럴을 할당할때 해당 타입의 속성이 있는지 & 그 외의 속성이 없는지 확인함

구조적 타이핑과 상충하는 것처럼 보이지만 타입을 명시하는냐 여부에따라 나눠지는듯..?

객체 리터럴일때만 잉여 속성 체크가 적용됨

약한 타입에도 값의 타입과 선언 타입에 공통된 속성이 있는지 체크함 (임시 변수 사용시 잉여 속성 체크를 건너띔)
`interface LineChartOptions {`

`logscale?: boolean;`

`const opts = { logScale: true };`

`const o: LineChartOptions = opts;`

`}`

할당 가능성과 잉여 속성 체크는 다름

## 아이템 12: 함수 표현식에 타입 적용하기

JS & TS는 함수 문장(statement)과 함수 표현식(expression)을 다르게 인식함 (TS는 표현식을 사용하는 것이 좋음)

* 문장: 일반적인 함수 구현 방식 `function rollDice1(sides: number): number { /*...*/ }`
* 표현식: (클로저?) `const rollDice2 = function(sides: number): number { /*...*/ };` = 입/출력 타입을 정의해서 재사용 가능

## 아이템 13: 타입과 인터페이스의 차이점 알기

TS에서 명명된 타입(named type)을 정의하는 방법 2가지

타입 사용 (= 타입 별칭)
type TState = {
	name: string;
	capital: string;
}

인터페이스 사용
interface IState {
	name: string;
	capital: string;
}

공통점

* type, interface 모두 제네릭 사용 가능
* type은 interface를 interface는 type을 확장할 수 있음
* class 구현시 type, interface 둘다 사용 가능

차이점

* interface는 type은 확장 가능하지만 유니온은 확장 불가능
* interface에는 보강(augment)이 가능함

### 선언 병합(declaration merging)
interface IState {
	name: string;
}

interface IState {
	population: number;
}

Const wyoming: State = {
	name: string;
	population: number;
}

## 아이템 14: 타입 연산과 제네릭 사용으로 반복 줄이기

타입의 이름을 지정해서 재사용

TopNavState를 확장해서 State를 구성하기보단, State의 부분 집합으로 정의 => 하나의 인터페이스로 유지
State를 인덱싱하여 속성의 타입에서 중복 제거 

interface State {
	userId: string;
	pageTitle: string;
	recentFiles: string[];
	pageContents: string;
}

interface TopNavState {
	userId: string;
	pageTitle: string;
	recentFiles: string[];
}

Type TopNavState = {
	userId: State['userId'];
	pageTitle: State['pageTitle'];
	recentFiles: State['recentFiles'];
};

매핑된 타입: 타입 정의를 루프 돌아 정의
type Pick<T, K> = { [k in K]: T[k] };

태그 중복제거
Interface SaveAction {
	type: 'save';
	// ...
}

interface LoadAction {
	type: 'load';
	// ...
} 

type Action = SaveAction | LoadAction
type ActionType = 'save' | 'load';
type ActionType = Action['type'];
type ActionRec = Pick<Action, 'type'>; // {type: "save" | "load"}

매핑된 타입과 keyof 조합해서 사용

interface Options {
	width?: number;
	height?: number;
	color?: string;
	label?: string;
}

interface OptionsUpdate {
	width?: number;
	height?: number;
	color?: string;
	label?: string;
}

class UIWidget {
	constructor(init: Options) { /* ... */ }
	update(options: OptionsUpdate) { /* ... */ }
}

=> (JS의 런타임 연산자가 아닌, 타입스크립트의 typeof를 사용함
type OptionsUpdate = { [k in keyof Options] ?: Options[k]};

요약

* 타입에 이름 붙이고, extends를 사용해서 인터페이스 필드 반복 줄이기
* 타입들 간의 매핑 - keyof, typeof, 인덱싱, 매핑된 타입
* 표준 라이브러리에 있는 제네릭 예시 Pick, Partial, ReturnType

**<다시 보기>**

## 아이템 15: 동적 데이터에 인덱스 시그니처 사용하기

"자바스크립트의 장점 중 하나는 바로 객체를 생성하는 문법이 간단하다는 것 입니다" (과연... 장점인가..?)

type Rocket = {[property: string]: string};

interface Rocket {
	name: string;
	variant: string;
	thrust_kN: number;
}

런타임에 실제 데이터 타입이 다를 수 있다면, undefined 추가 => 항상 undefined 여부 체크 필요

필드가 여러개 있고, 열마다 불특정 필드가 undefined라면, 모든 케이스를 타입으로 정의하기 보단, undefined 타입 1개로 퉁 치는게 최선...

데이터의 타입에 불확실성 있는경우 인덱스 시그니처를 사용하고, 타입이 정확하거나 보다 안전한 방법으로는 인터페이스 ,Record,매핑된 타입을 사용

## 아이템 16: number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

\> "0" == 0
true
(세상에나..., === or !== 사용해서 해결)

자바스크립트에서 객체는 키/값 쌍의 모음 

* 키는 보통 문자열이고, 심볼도 가능
* 값은 뭐든 가능

배열에 숫자 인덱스로 접근하면 런타임에 문자열도 변환되어 사용됨 == 문자열 키로 접근가능
그래서 타입스크립트는 숫자 키를 허용하고, 문자열 키와 다른 것으로 인식

for of..
for (const x of XS) {
	x;
}

요약

* 인덱스 시그니처에 number를 사용하는것 보단 Array, 튜플, ArrayLike를 사용하는게 좋음

## 아이템 17: 변경된 오류 방지를 위해 readonly 사용하기

readonly 접근제어자를 사용해 함수 내에서 변경을 막음

readonly 함수를 호출하는 함수도 readonly여야함
외부 라이브러리 함수를 호출하는 경우 타입 단언문을 사용해야함

(배열의 .length에 0을 할다아면 배열이 비워짐)

readonly, let(상수), 원본은 두고 값을 복사해서 수정 => Immutable

깊은 readonly 타입은 제네릭으로 만들어야함.. (라이브러리 ts-essentials -> DeepReadonly)

인덱스 시그니처에도 readonly를 쓸 수 있음
let obj: { readonly [k: string]: number } = {};

요약

* readonly를 사용해서 인터페이스(매개변수가 수정 가능한지)를 명확하게 함

## 아이템 18: 매핑된 타입을 사용하여 값을 동기화하기

보수적(conservative) 접근법 or 실패에 닫힌(fail close) 접근법

매핑된 타입은 한 객체가 다른 객체와 정확히 같은 속성을 가지게 할때 이상적임 => 값과 타입 동기화 == 새로운 타입이 추가되면 다른쪽도 추가 되어야함

타입의 구성과 값을 const로 선언해 정확히 일치한지 체크함
