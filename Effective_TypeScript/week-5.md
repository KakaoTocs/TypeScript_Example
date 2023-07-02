선택적: Optional
점진적: Gradual

## 아이템 38: any 타입은 가능한 한 좁은 범위에서만 사용하기

any타입으로 변환은 필요한 문맥(라인)에서만 사용하기 -> 모든 상황에 any타입으로 사용하게 되면 전체에 영향을 주게됨
let x: any = expressionReturningFoo(); 
processBar(x as any);

@ts-ignore: 다음 줄의 오류가 무시됨 => 강제로 타입 오류를 제거하는 목적

객체에서도 부분적으로 필요한 곳에만 any사용

const config: Config = {
	a: 1,
	b: 2,
	c: {
		key: value as any
	}
};

요약

- 함수의 반환 타입이 any인 경우 타입 안정성이 나빠진다.

## 아이템 39: any를 구체적으로 변형해서 사용하기

막연한 any 사용보단 구체적인 any타입이 좋음

function getLengthBad(array: any)
function getLength(array: any[])

any[ ] [ ], {[key: string]: any} 

비기본형(non-primitive) 타입을 포함하는 object 타입: 객체의 키를 열거할 수 있지만 속성에 접근 할 수 없음

## 아이템 40: 함수 안으로 타입 단언문 감추기

요약

* 타입 선언문은 일반적으로 타입을 위험하게 만듬
* 상황에 따라 필요 하기도 하고 현실적인 해결책이 되기도 함
* 불가피하게 사용해야하면, 정확한 정의를 가지는 함수 안으로 숨기기

<다시 읽기>

## 아이템 41: any의 진화를 이해하기

타입 좁히기와 타입의 진화는 다름

any 타입의 진화는 noImplicitAny가 설정된 상태에서 변수의 타입이 암시적 any인 경우에만 일어남

타입의 진화는 값을 활당하거나 배열에 요소를 넣은 후에만 일어남, 편집기에서는 반영 안됨

타입 진화는 잘못 진화 할수 도 있음 => 명시적 타입 구문을 사용하는 것이 더 좋은 설계다.

## 아이템 42: 모르는 타입의 값에는 any 대신 unknown을 사용하기

unknown은

1. 함수의 반환값과 관련된 형태
2. 변수 선언과 관련된 형태
3. 단언문과 관련된 형태

가 있음

any는

* 어떠한 타입이든 any 타입에 할당 가능하다.
* any 타입은 어떠한 타입으로도 할당 가능하다.

unknwon은

* 어떠한 타입이든 unknown에 할당 가능하다.
* unknown은 오직 unknown과 any에만 할당 가능하다.

never은

* 어떤 타입도 never에 할당할 수 없다.
* never은 어떤 타입으로도 할당 가능하다.

unknown 상태로 사용하면 오류가 발생해 적절한 타입으로 변환하도록 강제 할 수 있다.

instanceof를 체크한 후 원하는 타입으로 변환 가능
if (val instanceof Date) {
	val
}

사용자 정의 타입 가드도 원하는 타입으로 변환 가능
return (
	typeof(val) === 'object' && val !== null && 'name' in val && 'author' in val
);

제네릭 보다는 unknown을 반환하고 사용자가 직접 단언문을 사용하거나 원하는 대로 타입을 좁히도록 강제하는 것이 좋음

* { } 타입은 null과 undefined를 제외한 모든 값을 포함한다.
* object 타입은 모든 비기본형(non - primitive) 타입으로 이루어진다.
* => null과 undefined가 불가능한 경우에만 unknown대신 { } 사용

## 아이템 43 몽키 패치보다는 안전한 타입을 사용하기

전역 변수는 멀리 떨어진 부분들 간에 의존성을 만듬 => 사이드 이펙트를 고려해야함

해력법

1. interface의 특수 기능 중 하나인 보강(augmentation)을 사용
2. 더 구체적인 타입 단언문을 사용

## 아이템 44: 타입 커버리지를 추적하여 타입 안전성 유지하기

npm의 type-cover-age 패키지로 any를 추적할 수 있음

* npx type-coverage
* npx type-coverage --detail

<다시 읽기>