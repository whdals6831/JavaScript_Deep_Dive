*반복 가능한(iterable, 이터러블)* 객체는 배열을 일반화한 객체입니다. 이터러블 이라는 개념을 사용하면 어떤 객체에든 `for..of` 반복문을 적용할 수 있습니다.

배열은 대표적인 이터러블입니다. 배열 외에도 다수의 내장 객체가 반복 가능합니다. 문자열 역시 이터러블의 예입니다.

배열이 아닌 객체가 있는데, 이 객체가 어떤 것들의 컬렉션(목록, 집합 등)을 나타내고 있는 경우, `for..of` 문법을 적용할 수만 있다면 컬렉션을 순회하는데 유용할 겁니다. 이게 가능하도록 해봅시다.

> **Symbol.iterator**

직접 이터러블 객체를 만들어 이터러블이라는 개념을 이해해 보도록 합시다.

`for..of`를 적용하기에 적합해 보이는 배열이 아닌 객체를 만들겠습니다.

예시의 객체 `range`는 숫자 간격을 나타내고 있습니다.

```jsx
let range = {
  from: 1,
  to: 5
};

// 아래와 같이 for..of가 동작할 수 있도록 하는 게 목표입니다.
// for(let num of range) ... num=1,2,3,4,5
```

`range`를 이터러블로 만들려면(`for..of`가 동작하도록 하려면) 객체에 `Symbol.iterator`(특수 내장 심볼)라는 메서드를 추가해 아래와 같은 일이 벌어지도록 해야 합니다.

1. `for..of`가 시작되자마자 `for..of`는 `Symbol.iterator`를 호출합니다(`Symbol.iterator`가 없으면 에러가 발생합니다). `Symbol.iterator`는 반드시 *이터레이터(iterator, 메서드 `next`가 있는 객체)* 를 반환해야 합니다.
2. 이후 `for..of`는 *반환된 객체(이터레이터)만*을 대상으로 동작합니다.
3. `for..of`에 다음 값이 필요하면, `for..of`는 이터레이터의 `next()`메서드를 호출합니다.
4. `next()`의 반환 값은 `{done: Boolean, value: any}`와 같은 형태이어야 합니다. `done=true`는 반복이 종료되었음을 의미합니다. `done=false`일땐 `value`에 다음 값이 저장됩니다.

`range`를 반복 가능한 객체로 만들어주는 코드는 다음과 같습니다.

```jsx
let range = {
  from: 1,
  to: 5
};

// 1. for..of 최초 호출 시, Symbol.iterator가 호출됩니다.
range[Symbol.iterator] = function() {

  // Symbol.iterator는 이터레이터 객체를 반환합니다.
  // 2. 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작하는데, 이때 다음 값도 정해집니다.
  return {
    current: this.from,
    last: this.to,

    // 3. for..of 반복문에 의해 반복마다 next()가 호출됩니다.
    next() {
      // 4. next()는 값을 객체 {done:.., value :...}형태로 반환해야 합니다.
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// 이제 의도한 대로 동작합니다!
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
```

이터러블 객체의 핵심은 '관심사의 분리(Separation of concern, SoC)'에 있습니다.

- `range`엔 메서드 `next()`가 없습니다.
- 대신 `range[Symbol.iterator]()`를 호출해서 만든 ‘이터레이터’ 객체와 이 객체의 메서드 `next()`에서 반복에 사용될 값을 만들어냅니다.

이렇게 하면 이터레이터 객체와 반복 대상인 객체를 분리할 수 있습니다.

이터레이터 객체와 반복 대상 객체를 합쳐서 `range` 자체를 이터레이터로 만들면 코드가 더 간단해집니다.

다음처럼 말이죠.

```jsx
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    this.current = this.from;
    return this;
  },

  next() {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  }
};

for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```

이제 `range[Symbol.iterator]()`가 객체 `range` 자체를 반환합니다. 반환된 객체엔 필수 메서드인 `next()`가 있고 `this.current`에 반복이 얼마나 진행되었는지를 나타내는 값도 저장되어 있습니다. 코드는 더 짧아졌고요. 이렇게 작성하는 게 좋을 때가 종종 있습니다.

단점은 두 개의 `for..of` 반복문을 하나의 객체에 동시에 사용할 수 없다는 점입니다. 이터레이터(객체 자신)가 하나뿐이어서 두 반복문이 반복 상태를 공유하기 때문이죠. 그런데 동시에 두 개의 `for..of`를 사용하는 것은 비동기 처리에서도 흔한 케이스는 아닙니다.

> **문자열은 이터러블입니다**

배열과 문자열은 가장 광범위하게 쓰이는 내장 이터러블입니다.

`for..of`는 문자열의 각 글자를 순회합니다.

```jsx
for (let char of "test") {
  // 글자 하나당 한 번 실행됩니다(4회 호출).
  alert( char ); // t, e, s, t가 차례대로 출력됨
}
```

서로게이트 쌍(surrogate pair)에도 잘 동작합니다.

```jsx
let str = '𝒳😂';
for (let char of str) {
    alert( char ); // 𝒳와 😂가 차례대로 출력됨
}
```

> **이터레이터를 명시적으로 호출하기**

이터레이터를 어떻게 명시적으로 사용할 수 있는지 살펴보면서 좀 더 깊게 이해해봅시다.

`for..of`를 사용했을 때와 동일한 방법으로 문자열을 순회할 건데, 이번엔 직접 호출을 통해서 순회해보겠습니다. 다음 코드는 문자열 이터레이터를 만들고, 여기서 값을 ‘수동으로’ 가져옵니다.

```jsx
let str = "Hello";

// for..of를 사용한 것과 동일한 작업을 합니다.
// for (let char of str) alert(char);

let iterator = str[Symbol.iterator]();

while (true) {
  let result = iterator.next();
  if (result.done) break;
  alert(result.value); // 글자가 하나씩 출력됩니다.
}
```

이터레이터를 명시적으로 호출하는 경우는 거의 없는데, 이 방법을 사용하면`for..of`를 사용하는 것보다 반복 과정을 더 잘 통제할 수 있다는 장점이 있습니다. 반복을 시작했다가 잠시 멈춰 다른 작업을 하다가 다시 반복을 시작하는 것과 같이 반복 과정을 여러 개로 쪼개는 것이 가능합니다.