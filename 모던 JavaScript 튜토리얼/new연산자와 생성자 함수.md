- 객체 리터럴 {...} 을 사용하면 객체를 쉽게 만들 수 있습니다. 그런데 개발을 하다 보면 유사한 객체를 여러 개 만들어야 할 때가 생기곤 합니다. 복수의 사용자, 메뉴 내 다양한 아이템을 객체로 표현하려고 하는 경우가 그렇죠.
- `'new'`연산자와 생성자 함수를 사용하면 유사한 객체 여러 개를 쉽게 만들 수 있습니다.

> **생성자 함수**

- 생성자 함수(constructor function)와 일반 함수에 기술적인 차이는 없습니다. 다만 생성자 함수는 아래 두 관례를 따릅니다.
    1. 함수 이름의 첫 글자는 대문자로 시작합니다.
    2. 반드시 `'new'` 연산자를 붙여 실행합니다.

    예시 :

    ```jsx
    function User(name) {
      this.name = name;
      this.isAdmin = false;
    }

    let user = new User("보라");

    alert(user.name); // 보라
    alert(user.isAdmin); // false
    ```

    - `new User(...)`를 써서 함수를 실행하면 아래와 같은 알고리즘이 동작합니다.
    1. 빈 객체를 만들어 `this`에 할당합니다.
    2. 함수 본문을 실행합니다. `this`에 새로운 프로퍼티를 추가해 `this`를 수정합니다.
    3. `this`를 반환합니다.

    - 예시를 이용해 `new User(...)`가 실행되면 무슨 일이 일어나는지 살펴 보도록 하겠습니다.

    ```jsx
    function User(name) {
      // this = {};  (빈 객체가 암시적으로 만들어짐)

      // 새로운 프로퍼티를 this에 추가함
      this.name = name;
      this.isAdmin = false;

      // return this;  (this가 암시적으로 반환됨)
    }
    ```

    - 이제 `let user = new User("보라")`는 아래 코드를 입력한 것과 동일하게 동작합니다.

    ```jsx
    let user = {
      name: "보라",
      isAdmin: false
    };
    ```

    - `new User("보라")`이외에도 `new User("호진")`, `new User("지민")` 등을 이용하면 손쉽게 사용자 객체를 만들 수 있습니다. 객체 리터럴 문법으로 일일이 객체를 만드는 방법보다 훨씬 간단하고 읽기 쉽게 객체를 만들 수 있게 되었죠.
    - 생성자의 의의는 바로 여기에 있습니다. 재사용할 수 있는 객체 생성 코드를 구현하는 것이죠.

> **생성자와 return문**

- 생성자 함수엔 보통 `return`문이 없습니다. 반환해야 할 것들은 모두 this에 저장되고, `this`는 자동으로 반환되기 때문에 반환문을 명시적으로 써 줄 필요가 없습니다.
- 그런데 만약 return 문이 있다면 어떤 일이 벌어질까요? 아래와 같은 간단한 규칙이 적용됩니다.
    - 객체를 `return` 한다면 `this` 대신 객체가 반환됩니다.
    - 원시형을 `return` 한다면 `return`문이 무시됩니다.
- `return` 뒤에 객체가 오면 생성자 함수는 해당 객체를 반환해주고, 이 외의 경우는 `this`가 반환되죠.
- 아래 예시에선 첫 번째 규칙이 적용돼 `return`은 `this`를 무시하고 객체를 반환합니다.

```jsx
function BigUser() {

  this.name = "원숭이";

  return { name: "고릴라" };  // <-- this가 아닌 새로운 객체를 반환함
}

alert( new BigUser().name );  // 고릴라
```

- 아무것도 return하지 않는 예시를 살펴봅시다. 원시형을 반환하는 경우와 마찬가지로 두 번째 규칙이 적용됩니다.

```jsx
function SmallUser() {

  this.name = "원숭이";

  return; // <-- this를 반환함
}

alert( new SmallUser().name );  // 원숭이
```

- return문이 있는 생성자 함수는 거의 없습니다.

> **생성자 내 메서드**

- 생성자 함수를 사용하면 매개변수를 이용해 객체 내부를 자유롭게 구성할 수 있습니다. 엄청난 유연성이 확보되죠.
- 지금까진 `this`에 프로퍼티를 더해주는 예시만 살펴봤는데, 메서드를 더해주는 것도 가능합니다.
- 아래 예시에서 `new User(name)`는 프로퍼티 `name`과 메서드 `sayHi`를 가진 객체를 만들어줍니다.

```jsx
function User(name) {
  this.name = name;

  this.sayHi = function() {
    alert( "제 이름은 " + this.name + "입니다." );
  };
}

let bora = new User("이보라");

bora.sayHi(); // 내 이름은 이보라입니다.

/*
bora = {
   name: "이보라",
   sayHi: function() { ... }
}
*/
```

> **요약**

- 생성자 함수(짧게 줄여서 생성자)는 일반 함수입니다. 다만, 일반 함수와 구분하기 위해 함수 이름 첫 글자를 대문자로 씁니다.
- 생성자 함수는 반드시 `new` 연산자와 함께 호출해야 합니다. `new`와 함께 호출하면 내부에서 `this`가 암시적으로 만들어지고, 마지막엔 `this`가 반환됩니다.
- 생성자 함수는 유사한 객체를 여러 개 만들 때 유용합니다.