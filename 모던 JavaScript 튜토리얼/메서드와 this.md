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

> **과제**

1. **객체 리터럴에서 'this' 사용하기**
    - 함수 makeUser는 객체를 반환합니다.
    - 이 객체의 ref에 접근하면 어떤 결과가 발생하고, 그 이유는 뭘까요?

    ```jsx
    function makeUser() {
      return {
        name: "John",
        ref: this
      };
    };

    let user = makeUser();

    alert( user.ref.name ); // 결과가 어떻게 될까요?
    ```

    : 해답

    - 에러가 발생합니다.

    ```jsx
    function makeUser() {
      return {
        name: "John",
        ref: this
      };
    };

    let user = makeUser();

    alert( user.ref.name ); // Error: Cannot read property 'name' of undefined
    ```

    - 에러가 발생하는 이유는 this 값을 설정할 땐 객체 정의가 사용되지 않기 때문입니다. this 값은 호출 시점에 결정됩니다.
    - 위 코드에서 makeUser() 내 this는 undefined가 됩니다. 메서드로써 호출된 게 아니라 함수로써 호출되었기 때문입니다.
    - this 값은 전체 함수가 됩니다. 코드 블록과 객체 리터럴은 여기에 영향을 주지 않습니다.
    - 따라서 ref: this는 함수의 현재 this 값을 가져옵니다.
    - this의 값이 undefined가 되게 함수를 다시 작성하면 다음과 같습니다.

    ```jsx
    function makeUser(){
      return this; // 이번엔 객체 리터럴을 사용하지 않았습니다.
    }

    alert( makeUser().name ); // Error: Cannot read property 'name' of undefined
    ```

    - 보시다시피 alert( makeUser().name )와 위쪽에서 살펴본 alert( [user.ref.name](http://user.ref.name/) )의 결과가 같은 것을 확인할 수 있습니다.

    - 에러가 발생하지 않게 하려면 코드를 다음과 같이 수정하면 됩니다.

    ```jsx
    function makeUser() {
      return {
        name: "John",
        ref() {
          return this;
        }
      };
    };

    let user = makeUser();

    alert( user.ref().name ); // John
    ```

    - 이렇게 하면 user.ref()가 메서드가 되고 this는 . 앞의 객체가 되기 때문에 에러가 발생하지 않습니다.

1. **계산기 만들기**

    `calculator`라는 객체를 만들고 세 메서드를 구현해 봅시다.

    - `read()`에선 프롬프트 창을 띄우고 더할 값 두 개를 입력받습니다. 입력받은 값은 객체의 프로퍼티에 저장합니다.
    - `sum()`은 저장된 두 값의 합을 반환합니다.
    - `mul()`은 저장된 두 값의 곱을 반환합니다.

    ```jsx
    let calculator = {
      sum() {
        return this.a + this.b;
      },

      mul() {
        return this.a * this.b;
      },

      read() {
        this.a = +prompt('첫 번째 값:', 0);
        this.b = +prompt('두 번째 값:', 0);
      }
    };

    calculator.read();
    alert( calculator.sum() );
    alert( calculator.mul() );
    ```

1. **체이닝**
    - 올라가기(up)와 내려가기(down) 메서드를 제공하는 객체 ladder가 있습니다.

    ```jsx
    let ladder = {
      step: 0,
      up() {
        this.step++;
      },
      down() {
        this.step--;
      },
      showStep: function() { // 사다리에서 몇 번째 단에 올라와 있는지 보여줌
        alert( this.step );
      }
    };
    ```

    - 메서드를 연이어 호출하고자 한다면 아래와 같이 코드를 작성할 수 있습니다.

    ```jsx
    ladder.up();
    ladder.up();
    ladder.down();
    ladder.showStep(); // 1
    ```

    - up, down, showStep을 수정해 아래처럼 메서드 호출 체이닝이 가능하도록 해봅시다.

    ```jsx
    ladder.up().up().down().showStep(); // 1
    ```

    - 참고로 이런 방식은 자바스크립트 라이브러리에서 널리 사용됩니다.

    : 해답

    - 메서드를 호출할 때마다 객체 자신을 반환하게 하면 됩니다.

    ```jsx
    let ladder = {
      step: 0,
      up() {
        this.step++;
        return this;
      },
      down() {
        this.step--;
        return this;
      },
      showStep() {
        alert( this.step );
        return this;
      }
    }

    ladder.up().up().down().up().down().showStep(); // 1
    ```