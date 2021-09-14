날짜를 저장할 수 있고, 날짜와 관련된 메서드도 제공해주는 내장 객체 [Date](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date)에 대해 알아봅시다(이 글에선 일시(날짜/시간)를 날짜와 혼용해서 사용하겠습니다 – 옮긴이).

Date 객체를 활용하면 생성 및 수정 시간을 저장하거나 시간을 측정할 수 있고, 현재 날짜를 출력하는 용도 등으로도 활용할 수 있습니다.

> **객체 생성하기**

`new Date()`를 호출하면 새로운 `Date` 객체가 만들어지는데, `new Date()`는 다음과 같은 형태로 호출할 수 있습니다.

**`new Date()`**인수 없이 호출하면 현재 날짜와 시간이 저장된 `Date` 객체가 반환됩니다.

```jsx
let now = new Date();
alert( now ); // 현재 날짜 및 시간이 출력됨
```

**`new Date(milliseconds)`**

UTC 기준(UTC+0) 1970년 1월 1일 0시 0분 0초에서 `milliseconds` 밀리초(1/1000 초) 후의 시점이 저장된 `Date` 객체가 반환됩니다.

```jsx
// 1970년 1월 1일 0시 0분 0초(UTC+0)를 나타내는 객체
let Jan01_1970 = new Date(0);
alert( Jan01_1970 );

// 1970년 1월 1일의 24시간 후는 1970년 1월 2일(UTC+0)임
let Jan02_1970 = new Date(24 * 3600 * 1000);
alert( Jan02_1970 );
```

1970년의 첫날을 기준으로 흘러간 밀리초를 나타내는 정수는 *타임스탬프(timestamp)* 라고 부릅니다.

타임스탬프를 사용하면 날짜를 숫자 형태로 간편하게 나타낼 수 있습니다. `new Date(timestamp)`를 사용해 타임스탬프를 사용해 특정 날짜가 저장된 `Date` 객체를 손쉽게 만들 수 있고 `date.getTime()` 메서드를 사용해 `Date` 객체에서 타임스탬프를 추출하는 것도 가능합니다(자세한 사항은 아래에서 다루도록 하겠습니다).

1970년 1월 1일 이전 날짜에 해당하는 타임스탬프 값은 음수입니다. 예시를 살펴봅시다.

```jsx
// 31 Dec 1969
let Dec31_1969 = new Date(-24 * 3600 * 1000);
alert( Dec31_1969 );
```

**`new Date(datestring)`**

인수가 하나인데, 문자열이라면 해당 문자열은 자동으로 구문 분석(parsed)됩니다. 구문 분석에 적용되는 알고리즘은 `Date.parse`에서 사용하는 알고리즘과 동일한데, 자세한 내용은 아래에서 다루도록 하겠습니다.

```jsx
let date = new Date("2017-01-26");
alert(date);
// 인수로 시간은 지정하지 않았기 때문에 GMT 자정이라고 가정하고
// 코드가 실행되는 시간대(timezone)에 따라 출력 문자열이 바뀝니다.
// 따라서 얼럿 창엔
// Thu Jan 26 2017 11:00:00 GMT+1100 (Australian Eastern Daylight Time)
// 혹은
// Wed Jan 25 2017 16:00:00 GMT-0800 (Pacific Standard Time)등이 출력됩니다.
```

**`new Date(year, month, date, hours, minutes, seconds, ms)`**

주어진 인수를 조합해 만들 수 있는 날짜가 저장된 객체가 반환됩니다(지역 시간대 기준). 첫 번째와 두 번째 인수만 필수값입니다.
• `year`는 반드시 네 자리 숫자여야 합니다. `2013`은 괜찮고 `98`은 괜찮지 않습니다.
• `month`는 `0`(1월)부터 `11`(12월) 사이의 숫자여야 합니다.
• `date`는 일을 나타내는데, 값이 없는 경우엔 1일로 처리됩니다.
• `hours/minutes/seconds/ms`에 값이 없는 경우엔 `0`으로 처리됩니다.
예시:

```jsx
new Date(2011, 0, 1, 0, 0, 0, 0); // 2011년 1월 1일, 00시 00분 00초
new Date(2011, 0, 1); // hours를 비롯한 인수는 기본값이 0이므로 위와 동일
```

최소 정밀도는 1밀리초(1/1000초)입니다.

```jsx
let date = new Date(2011, 0, 1, 2, 3, 4, 567);
alert( date ); // 2011년 1월 1일, 02시 03분 04.567초
```

> **날짜 구성요소 얻기**

`Date` 객체의 메서드를 사용하면 연, 월, 일 등의 값을 얻을 수 있습니다.

- **[getFullYear()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getFullYear)**

    연도(네 자릿수)를 반환합니다.

- **[getMonth()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMonth)**

    월을 반환합니다(**0 이상 11 이하**).

- **[getDate()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate)**

    일을 반환합니다(1 이상 31 이하). 어! 그런데 메서드 이름이 뭔가 이상하네요.

- **[getHours()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getHours), [getMinutes()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMinutes), [getSeconds()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getSeconds), [getMilliseconds()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getMilliseconds)**

    시, 분, 초, 밀리초를 반환합니다.

⚠️  **`getYear()`말고 `getFullYear()`를 사용하세요.**

여러 자바스크립트 엔진이 더는 사용되지 않는(deprecated) 비표준 메서드 `getYear()`을 구현하고 있습니다. 이 메서드는 두 자릿수 연도를 반환하는 경우가 있기 때문에 절대 사용해선 안 됩니다. 연도 정보를 얻고 싶다면 `getFullYear()`를 사용하세요.

이 외에도 요일을 반환해주는 메서드도 있습니다.

- **[getDay()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay)**

    일요일을 나타내는 `0`부터 토요일을 나타내는 `6`까지의 숫자 중 하나를 반환합니다. 몇몇 나라에서 요일의 첫날이 일요일이 아니긴 하지만, getDay에선 항상 `0`이 일요일을 나타냅니다. 이를 변경할 방법은 없습니다.

**위에서 소개해드린 메서드 모두는 현지 시간 기준 날짜 구성요소를 반환합니다.**

위 메서드 이름에 있는 ‘get’ 다음에 'UTC’를 붙여주면 표준시(UTC+0) 기준의 날짜 구성 요소를 반환해주는 메서드 [getUTCFullYear()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCFullYear), [getUTCMonth()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCMonth), [getUTCDay()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getUTCDay)를 만들 수 있습니다.

현지 시간대가 UTC 시간대와 다르다면 아래 예시를 실행했을 때 얼럿창엔 다른 값이 출력됩니다.

```jsx
// 현재 일시
let date = new Date();

// 현지 시간 기준 시
alert( date.getHours() );

// 표준시간대(UTC+0, 일광 절약 시간제를 적용하지 않은 런던 시간) 기준 시
alert( date.getUTCHours() );
```

아래 두 메서드는 위에서 소개한 메서드와 달리 표준시(UTC+0) 기준의 날짜 구성 요소를 반환해주는 메서드가 없습니다.

- **[getTime()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)**

    주어진 일시와 1970년 1월 1일 00시 00분 00초 사이의 간격(밀리초 단위)인 타임스탬프를 반환합니다.

- **[getTimezoneOffset()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)**

    현지 시간과 표준 시간의 차이(분)를 반환합니다.

```jsx
// UTC-1 시간대에서 이 예시를 실행하면 60이 출력됩니다.
// UTC+3 시간대에서 이 예시를 실행하면 -180이 출력됩니다.
alert( new Date().getTimezoneOffset() );
```