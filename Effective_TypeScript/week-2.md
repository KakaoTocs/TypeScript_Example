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