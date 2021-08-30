- 객체는 사용자(user), 주문(order) 등과 같이 실제 존재하는 개체(entity)를 표현하고자 할 때 생성됩니다.

```jsx
let user = {
  name: "John",
  age: 30
};
```

- 사용자는 현실에서 장바구니에서 물건 선택하기, 로그인하기, 로그아웃하기 등의 행동을 합니다. 이와 마찬가지로 사용자를 나타내는 객체 user도 특정한 행동을 할 수 있습니다.
- 자바스크립트에선 객체의 프로퍼티에 함수를 할당해 객체에게 행동할 수 있는 능력을 부여해줍니다.

> **메서드 만들기**

- 객체 user에게 인사할 수 있는 능력을 부여해 줍시다.

```jsx
let user = {
  name: "John",
  age: 30
};

user.sayHi = function() {
  alert("안녕하세요!");
};

user.sayHi(); // 안녕하세요!
```

- 함수 표현식으로 함수를 만들고, 객체 프로퍼티 `user.sayHi`에 함수를 할당해 주었습니다.
- 이제 객체에 할당된 함수를 호출하면 user가 인사를 해줍니다.
- 이렇게 객체 프로퍼티에 할당된 함수를 *메서드(method)* 라고 부릅니다.

- 메서드는 아래와 같이 이미 정의된 함수를 이용해서 만들 수도 있습니다.

```jsx
let user = {
  // ...
};

// 함수 선언
function sayHi() {
  alert("안녕하세요!");
};

// 선언된 함수를 메서드로 등록
user.sayHi = sayHi;

user.sayHi(); // 안녕하세요!
```

> **메서드 단축 구문**

```jsx
// 아래 두 객체는 동일하게 동작합니다.

user = {
  sayHi: function() {
    alert("Hello");
  }
};

// 단축 구문을 사용하니 더 깔끔해 보이네요.
user = {
  sayHi() { // "sayHi: function()"과 동일합니다.
    alert("Hello");
  }
};
```

> **메서드와 this**

- 메서드는 객체에 저장된 정보에 접근할 수 있어야 제 역할을 할 수 있습니다. 모든 메서드가 그런 건 아니지만, 대부분의 메서드가 객체 프로퍼티의 값을 활용합니다.
- user.sayHi()의 내부 코드에서 객체 user에 저장된 이름(name)을 이용해 인사말을 만드는 경우가 이런 경우에 속합니다.
- 메서드 내부에서 this 키워드를 사용하면 객체에 접근할 수 있습니다.
- 이때 '점 앞’의 this는 객체를 나타냅니다. 정확히는 메서드를 호출할 때 사용된 객체를 나타내죠.

```jsx
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // 'this'는 '현재 객체'를 나타냅니다.
    alert(this.name);
  }

};

user.sayHi(); // John
```

- this를 사용하지 않고 외부 변수를 참조해 객체에 접근하는 것도 가능합니다.
- 그런데 외부 변수를 사용해 객체를 참조하면 예상치 못한 에러가 발생할 수 있습니다. user를 복사해 다른 변수에 할당(admin = user)하고, user는 전혀 다른 값으로 덮어썼다고 가정해 봅시다. sayHi()는 원치 않는 값(null)을 참조할 겁니다.

```jsx
let user = {
  name: "John",
  age: 30,

  sayHi() {
    alert( user.name ); // Error: Cannot read property 'name' of null
  }

};

let admin = user;
user = null; // user를 null로 덮어씁니다.

admin.sayHi(); // sayHi()가 엉뚱한 객체를 참고하면서 에러가 발생했습니다.
```

- alert 함수가 [user.name](http://user.name/) 대신 this.name을 인수로 받았다면 에러가 발생하지 않았을 겁니다.

> **자유로운 this**

- 자바스크립트의 this는 다른 프로그래밍 언어의 this와 동작 방식이 다릅니다. 자바스크립트에선 모든 함수에 this를 사용할 수 있습니다.
- 아래와 같이 코드를 작성해도 문법 에러가 발생하지 않습니다.

```jsx
function sayHi() {
  alert( this.name );
}
```

- this 값은 런타임에 결정됩니다. 컨텍스트에 따라 달라지죠.
- 동일한 함수라도 다른 객체에서 호출했다면 'this’가 참조하는 값이 달라집니다.

```jsx
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
  alert( this.name );
}

// 별개의 객체에서 동일한 함수를 사용함
user.f = sayHi;
admin.f = sayHi;

// 'this'는 '점(.) 앞의' 객체를 참조하기 때문에
// this 값이 달라짐
user.f(); // John  (this == user)
admin.f(); // Admin  (this == admin)

admin['f'](); // Admin (점과 대괄호는 동일하게 동작함)
```

> **this가 없는 화살표 함수**

- 화살표 함수는 일반 함수와는 달리 ‘고유한’ this를 가지지 않습니다. 화살표 함수에서 this를 참조하면, 화살표 함수가 아닌 ‘평범한’ 외부 함수에서 this 값을 가져옵니다.
- 아래 예시에서 함수 arrow()의 this는 외부 함수 user.sayHi()의 this가 됩니다.

```jsx
let user = {
  firstName: "보라",
  sayHi() {
    let arrow = () => alert(this.firstName);
    arrow();
  }
};

user.sayHi(); // 보라
```

- 별개의 this가 만들어지는 건 원하지 않고, 외부 컨텍스트에 있는 this를 이용하고 싶은 경우 화살표 함수가 유용합니다.