복잡한 객체를 다루고 있다고 가정해 봅시다. 네트워크를 통해 객체를 어딘가에 보내거나 로깅 목적으로 객체를 출력해야 한다면 객체를 문자열로 전환해야 할겁니다.

이때 전환된 문자열엔 원하는 정보가 있는 객체 프로퍼티 모두가 포함되어야만 합니다.

아래와 같은 메서드를 구현해 객체를 문자열로 전환해봅시다.

```jsx
let user = {
  name: "John",
  age: 30,

  toString() {
    return `{name: "${this.name}", age: ${this.age}}`;
  }
};

alert(user); // {name: "John", age: 30}
```

그런데 개발 과정에서 프로퍼티가 추가되거나 삭제, 수정될 수 있습니다. 이렇게 되면 위에서 구현한 `toString`을 매번 수정해야 하는데 이는 아주 고통스러운 작업이 될 겁니다. 프로퍼티에 반복문을 돌리는 방법을 대안으로 사용할 수 있는데, 중첩 객체 등으로 인해 객체가 복잡한 경우 이를 문자열로 변경하는 건 까다로운 작업이라 이마저도 쉽지 않을 겁니다.

다행히 자바스크립트엔 이런 문제를 해결해주는 방법이 있습니다. 관련 기능이 이미 구현되어 있어서 우리가 직접 코드를 짤 필요가 없습니다.

> JSON.stringify

[JSON](http://en.wikipedia.org/wiki/JSON) (JavaScript Object Notation)은 값이나 객체를 나타내주는 범용 포맷으로, [RFC 4627](http://tools.ietf.org/html/rfc4627) 표준에 정의되어 있습니다. JSON은 본래 자바스크립트에서 사용할 목적으로 만들어진 포맷입니다. 그런데 라이브러리를 사용하면 자바스크립트가 아닌 언어에서도 JSON을 충분히 다룰 수 있어서, JSON을 데이터 교환 목적으로 사용하는 경우가 많습니다. 특히 클라이언트 측 언어가 자바스크립트일 때 말이죠. 서버 측 언어는 무엇이든 상관없습니다.

자바스크립트가 제공하는 JSON 관련 메서드는 아래와 같습니다.

- `JSON.stringify` – 객체를 JSON으로 바꿔줍니다.
- `JSON.parse` – JSON을 객체로 바꿔줍니다.

객체 `student`에 `JSON.stringify`를 적용해봅시다.

```jsx
let student = {
  name: 'John',
  age: 30,
  isAdmin: false,
  courses: ['html', 'css', 'js'],
  wife: null
};

let json = JSON.stringify(student);

alert(typeof json); // 문자열이네요!

alert(json);
/* JSON으로 인코딩된 객체:
{
  "name": "John",
  "age": 30,
  "isAdmin": false,
  "courses": ["html", "css", "js"],
  "wife": null
}
*/
```

`JSON.stringify(student)`를 호출하자 `student`가 문자열로 바뀌었습니다.

이렇게 변경된 문자열은 *JSON으로 인코딩된(JSON-encoded)*, *직렬화 처리된(serialized)*, *문자열로 변환된(stringified)*, *결집된(marshalled)* 객체라고 부릅니다. 객체는 이렇게 문자열로 변환된 후에야 비로소 네트워크를 통해 전송하거나 저장소에 저장할 수 있습니다.

JSON으로 인코딩된 객체는 일반 객체와 다른 특징을 보입니다.

- 문자열은 큰따옴표로 감싸야 합니다. JSON에선 작은따옴표나 백틱을 사용할 수 없습니다(`'John'`이 `"John"`으로 변경된 것을 통해 이를 확인할 수 있습니다).
- 객체 프로퍼티 이름은 큰따옴표로 감싸야 합니다(`age:30`이 `"age":30`으로 변한 것을 통해 이를 확인할 수 있습니다).

`JSON.stringify`는 객체뿐만 아니라 원시값에도 적용할 수 있습니다.

적용할 수 있는 자료형은 아래와 같습니다.

- 객체 `{ ... }`
- 배열 `[ ... ]`
- 원시형:
    - 문자형
    - 숫자형
    - 불린형 값 `true`와 `false`
    - `null`

예시:

```jsx
// 숫자를 JSON으로 인코딩하면 숫자입니다.
alert( JSON.stringify(1) ) // 1

// 문자열을 JSON으로 인코딩하면 문자열입니다(다만, 큰따옴표가 추가됩니다).
alert( JSON.stringify('test') ) // "test"

alert( JSON.stringify(true) ); // true

alert( JSON.stringify([1, 2, 3]) ); // [1,2,3]
```

JSON은 데이터 교환을 목적으로 만들어진 언어에 종속되지 않는 포맷입니다. 따라서 자바스크립트 특유의 객체 프로퍼티는 `JSON.stringify`가 처리할 수 없습니다.

`JSON.stringify` 호출 시 무시되는 프로퍼티는 아래와 같습니다.

- 함수 프로퍼티 (메서드)
- 심볼형 프로퍼티 (키가 심볼인 프로퍼티)
- 값이 `undefined`인 프로퍼티

```jsx
let user = {
  sayHi() { // 무시
    alert("Hello");
  },
  [Symbol("id")]: 123, // 무시
  something: undefined // 무시
};

alert( JSON.stringify(user) ); // {} (빈 객체가 출력됨)
```

대개 이 프로퍼티들은 무시 되어도 괜찮습니다. 그런데 이들도 문자열에 포함시켜야 하는 경우가 생기곤 하는데 이에 대해선 아래에서 다루도록 하겠습니다.

`JSON.stringify`의 장점 중 하나는 중첩 객체도 알아서 문자열로 바꿔준다는 점입니다.

예시:

```jsx
let meetup = {
  title: "Conference",
  room: {
    number: 23,
    participants: ["john", "ann"]
  }
};

alert( JSON.stringify(meetup) );
/* 객체 전체가 문자열로 변환되었습니다.
{
  "title":"Conference",
  "room":{"number":23,"participants":["john","ann"]},
}
*/
```

`JSON.stringify`를 사용할 때 주의하셔야 할 점이 하나 있습니다. 순환 참조가 있으면 원하는 대로 객체를 문자열로 바꾸는 게 불가능합니다.

예시:

```jsx
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: ["john", "ann"]
};

meetup.place = room;       // meetup은 room을 참조합니다.
room.occupiedBy = meetup; // room은 meetup을 참조합니다.

JSON.stringify(meetup); // Error: Converting circular structure to JSON
```

`room.occupiedBy`는 `meetup`을, `meetup.place`는 `room`을 참조하기 때문에 JSON으로의 변환이 실패했습니다.

> **replacer로 원하는 프로퍼티만 직렬화하기**

`JSON.stringify`의 전체 문법은 아래와 같습니다.

```jsx
let json = JSON.stringify(value[, replacer, space])
```

- **value**

    인코딩 하려는 값

- **replacer**

    JSON으로 인코딩 하길 원하는 프로퍼티가 담긴 배열. 또는 매핑 함수 `function(key, value)`

- **space**

    서식 변경 목적으로 사용할 공백 문자 수

대다수의 경우 `JSON.stringify`엔 인수를 하나만 넘겨서 사용합니다. 그런데 순환 참조를 다뤄야 하는 경우같이 전환 프로세스를 정교하게 조정하려면 두 번째 인수를 사용해야 합니다.

JSON으로 변환하길 원하는 프로퍼티가 담긴 배열을 두 번째 인수로 넘겨주면 이 프로퍼티들만 인코딩할 수 있습니다.

예시:

```jsx
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup은 room을 참조합니다.
};

room.occupiedBy = meetup; // room references meetup

alert( JSON.stringify(meetup, ['title', 'participants']) );
// {"title":"Conference","participants":[{},{}]}
```

배열에 넣어준 프로퍼티가 잘 출력된 것을 확인할 수 있습니다. 그런데 배열에 `name`을 넣지 않아서 출력된 문자열의 `participants`가 텅 비어버렸네요. 규칙이 너무 까다로워서 발생한 문제입니다.

순환 참조를 발생시키는 프로퍼티 `room.occupiedBy`만 제외하고 모든 프로퍼티를 배열에 넣어봅시다.

```jsx
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup references room
};

room.occupiedBy = meetup; // room references meetup

alert( JSON.stringify(meetup, ['title', 'participants', 'place', 'name', 'number']) );
/*
{
  "title":"Conference",
  "participants":[{"name":"John"},{"name":"Alice"}],
  "place":{"number":23}
}
*/
```

`occupiedBy`를 제외한 모든 프로퍼티가 직렬화되었습니다. 그런데 배열이 좀 길다는 느낌이 듭니다.

`replacer` 자리에 배열 `대신` 함수를 전달해 이 문제를 해결해 봅시다(매개변수 replacer는 '대신하다’라는 뜻을 가진 영단어 replace에서 그 이름이 왔습니다 – 옮긴이).

`replacer`에 전달되는 함수(`replacer` 함수)는 프로퍼티 `(키, 값)` 쌍 전체를 대상으로 호출되는데, 반드시 기존 프로퍼티 값을 대신하여 사용할 값을 반환해야 합니다. 특정 프로퍼티를 직렬화에서 누락시키려면 반환 값을 `undefined`로 만들면 됩니다.

아래 예시는 `occupiedBy`를 제외한 모든 프로퍼티의 값을 변경 없이 “그대로” 직렬화하고 있습니다. `occupiedBy`는 `undefined`를 반환하게 해 직렬화에 포함하지 않은 것도 확인해 보시길 바랍니다.

```jsx
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup은 room을 참조합니다
};

room.occupiedBy = meetup; // room은 meetup을 참조합니다

alert( JSON.stringify(meetup, function replacer(key, value) {
  alert(`${key}: ${value}`);
  return (key == 'occupiedBy') ? undefined : value;
}));

/* replacer 함수에서 처리하는 키:값 쌍 목록
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
*/
```

`replacer` 함수가 중첩 객체와 배열의 요소까지 포함한 모든 키-값 쌍을 처리하고 있다는 점에 주목해주시기 바랍니다. `replacer` 함수는 재귀적으로 키-값 쌍을 처리하는데, 함수 내에서 `this`는 현재 처리하고 있는 프로퍼티가 위치한 객체를 가리킵니다.

첫 얼럿창에 예상치 못한 문자열(`":[object Object]"`)이 뜨는걸 볼 수 있는데, 이는 함수가 최초로 호출될 때 `{"": meetup}` 형태의 "래퍼 객체"가 만들어지기 때문입니다. `replacer`함수가 가장 처음으로 처리해야하는 `(key, value)` 쌍에서 키는 빈 문자열, 값은 변환하고자 하는 객체(meetup) 전체가 되는 것이죠.

이렇게 `replacer` 함수를 사용하면 중첩 객체 등을 포함한 객체 전체에서 원하는 프로퍼티만 선택해 직렬화 할 수 있습니다.