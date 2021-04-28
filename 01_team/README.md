### 2.6 연산 (Operations)

Sass는 기본적인 연산 기능을 제공하는데, 해당 연산을 사용하여 레이아웃 작업 시 상황에 맞게 크기를 계산하거나 정해진 값을 나눠서 작성할 수 있어 유용합니다. Sass에서 사용할 수 있는 연산자의 종류로는 산술, 비교, 논리 연산자가 존재합니다.

| 종류 | 연산자 |       설명       | 주의사항                            |
| :--: | :----: | :--------------: | :---------------------------------- |
| 산술 |  `+`   |      더하기      |
| 산술 |  `-`   |       빼기       |
| 산술 |  `*`   |      곱하기      | 피연산자 중 하나의 값이 반드시 숫자 |
| 산술 |  `/`   |      나누기      | 나누는 값이 반드시 숫자             |
| 산술 |  `%`   |      나머지      |
| 비교 |  `==`  |       동등       |
| 비교 |  `!=`  |       부등       |
| 비교 |  `<`   |    보다 작은     |
| 비교 |  `>`   |     보다 큰      |
| 비교 |  `<=`  | 보다 작거나 같은 |
| 비교 |  `>=`  | 보다 크거나 같은 |
| 논리 | `and`  |      그리고      |
| 논리 |  `or`  |       또는       |
| 논리 | `not`  |       부정       |

#### 2.6.1 숫자 (Numbers)

##### 2.6.1.1 상대적 단위 연산

- 절댓값을 나타내는 `px` 단위를 사용하여 일반적으로는 연산을 합니다.
  > 명확하게는 `px`는 해상도에 따른 '상대적 단위' 입니다.
- Sass에서의 연산은 대상을 변환하여 연산할 수 없는 경우에 에러를 출력합니다.
  - 상대적 단위인 `%`,`em`,`vw` 등을 Sass는 알지 못합니다.
    - 상대적 값의 결과값은 브라우저만이 알 수 있습니다.
    - 상대적 단위를 사용하는 연산은 동일한 단위를 갖는 값과의 연산만 유효합니다.
- CSS3의 calc 함수를 사용하면 위의 문제를 해결할 수 있습니다.
  ```scss
  /* 단위가 다르기 때문에 단위 모순 에러(Incompatible units error)가 발생한다. */
  width: 50% - 20px;
  /* 순수한 CSS 함수로 단위가 달라도 연산이 가능하다. */
  width: calc(50% - 20px);
  /* 상잭적 단위 연산 */
  width: 5% + 10%;
  ```

##### 2.6.1.2 나누기 연산의 주의사항

- `/` 연산자의 경우 CSS의 `font` 속성의 값처럼 숫자를 분리하는 방법으로도 사용하기 때문에 나누기 연산으로 사용되지 않을 수도 있습니다.
  ```scss
  p {
    font: italic bold 12px/30px Georgia, serif;
    border-radius: 10px 20px/20px;
  }
  ```
- `/` 연산자를 나누기 연산자로 사용하기 위해서는 다음과 같은 조건을 충족해야 합니다.

  1. 값 또는 그 일부가 변수에 저장되었거나 함수에 의해 반환되는 경우
  2. 괄호 내에서 사용되는 경우
  3. 다른 산술 표현식의 일부로 사용되는 경우

  ```scss
  $width: 300px;

  width: $width / 2; // 1번의 경우 -> 150px
  height: (300px / 2); // 2번의 경우 -> 150px
  margin-right: 10px + 20px / 2px; // 3번의 경우 -> 20px
  ```

#### 2.6.2 색상 (Colors)

- 모든 산술 연산자를 사용하여 색상값도 연산할 수 있습니다.
  ```scss
  p {
    color: #010203 + #040506; // #050709
    color: #010203 * 2; // #020406
    color: rbga(255, 0, 0, 0.75);
  }
  ```
- rgba 연산은 alpha 값이 동일해야 가능합니다.
  - 다만, alpha 값은 연산되지 않습니다.
  - alpha 값 연산을 위해서는 `opacify()`, `transparentize()` 함수를 사용해야 합니다.
  ```scss
  color: rgba(255, 0, 0, 0.3) + rgba(0, 0, 255, 0.3); // rgba(255, 0, 255, 0.3)
  ```

#### 2.6.3 문자 (Strings)

- 문자 연산에는 `+` 연산자가 사용되는데 자바스크립트와 같이 문자열을 연결할 수 있습니다.
- 문자 연산의 결과는 왼쪽 피연산자를 기준으로 합니다.
  ```scss
  div::after {
    /* 왼쪽 피연산자가 따옴표로 묶여있을시 연산 결과도 따옴표로 묶여있습니다. */
    content: 'Hello ' + World; // "Hello World"
    /* 왼쪽 피연산자가 따옴표로 묶여있지 않을시 연산 결과도 묶여있지 않습니다.*/
    flex-flow: row + '-reverse' + ' ' + wrap; // row-reverse wrap
  }
  ```

#### 2.6.4 논리 (Boolean)

- 논리 연산자는 자바스크립트의 `&&`, `||`, `!`와 같은 기능을 하는 연산자이며, `Sass`의 조건문인 `@if`와 `built-in if()` 함수에서 사용됩니다.
- 논리 연산자의 종류로는 `and(그리고)`, `or(또는)`, `not(부정)`이 있습니다.

  ```scss
  $width: 90px;

  div {
    @if not($width > 100px) {
      // True
      height: 300px;
    }

    @if ($width > 40px and $width < 90px) {
      // False
      height: 500px;
    }

    @if ($width > 40px or $width < 90px) {
      // True
      height: 100px;
    }
  }
  ```

### 2.7 재활용 (Mixins)

중복되는 코드를 방지하기 위해 스타일 시트 전체에서 **재사용 할 CSS 선언 그룹**을 정의하는 Sass의 매우 유용한 기능입니다. Mixin을 사용하면 다양한 스타일을 만들어 사용할 수 있습니다. 사용하는 방법은 매우 간단합니다. `@mixin`으로 선언하고 `@include`로 불러옵니다!

#### 2.7.1 @mixin

- Mixin의 선언 방법은 다음과 같습니다.

  - scss와 sass의 선언 방법에 약간의 차이점이 존재합니다.

    ```scss
    /* Scss */
    @mixin 믹스인이름 {
      스타일;
    }

    /* Sass */
    /* Sass의 경우 들여쓰기를 주의해야 합니다! */
    =믹스인이름
      스타일
    ```

  - 함수와 같이 매개 변수를 사용할 수도 있습니다.

    ```scss
    @mixin size($w, $h) {
      width: $w;
      height: $h;
    }

    .box {
      @include size(100px, 200px);
    }
    ```

  - 선택자를 포함 할 수 있으며, 상위(부모) 요소 참조(`&`)도 가능합니다.

    ```scss
    @mixin deco-text {
      &::after {
        content: '!!';
      }

      span.icon {
        background-image: url('/image/smail-icon.png');
      }
    }
    ```

#### 2.7.2 @include

- `@include`는 선언된 Maxin을 불러오기 위해 사용하며, 사용 방법은 다음과 같습니다.

  ```scss
  /* Scss */
  @include 믹스인이름;
  .box {
    /* mixin에 전달할 인수가 없을 경우 뒤의 괄호는 생략할 수 있습니다. */
    @include deco-text;
  }

  /* Sass */
  +믹스인이름
  .box
    +deco-text
  ```

#### 2.7.3 인수 (Arguments)

- Mixin은 마치 함수처럼 인수를 가질 수 있습니다. 이러한 특성 덕분에 하나의 Mixin을 이용하여 다양한 결과를 만들어 낼 수 있습니다.
- 인수를 가질 수 있는 Mixin의 선언 방법 및 사용 방법은 다음과 같습니다.

  ```scss
  /* Scss */
  @mixin 믹스인이름($매개변수) {
    스타일;
  }
  @include 믹스인이름(인수)

  /* Sass */
  =믹스인이름($매개변수)
    스타일

  +믹스인이름(인수)
  ```

  > 매개변수(Parameters)는 제공되는 여러 데이터를 가리키기 위해 사용되는 변수를 말합니다.
  > 인수(Arguments)는 `Mixin`에 제공되는 여러 데이터들을 말합니다.

- 사용 예시는 다음과 같습니다.

  ```scss
  @mixin solid-line($width, $color) {
    border: $width solid $color;
  }

  .box1 {
    @include solid-line(1px, red);
  }
  .box2 {
    @include solid-line(4px, blue);
  }
  ```

##### 2.7.3.1 인수의 기본값 설정

- 인수가 전달되지 않았을 때 사용할 수 있는 매개 변수의 초기값을 설정할 수 있습니다.
  ```scss
  @mixin 믹스인이름($매개변수: 기본값) {
    스타일;
  }
  ```
- 사용 예시는 다음과 같습니다.

  ```scss
  @mixin size($w: 100px, $h: 100px) {
    width: $w;
    height: $h;
  }

  /* mixin에 전달할 인수가 없을 경우 뒤의 괄호는 생략할 수 있습니다. */
  .box1 {
    @include size;
  }
  .box2 {
    @include size(50px, 60px);
  }
  ```

##### 2.7.3.2 키워드 인수

- 위에서 만든 `@mixin size`을 사용할 때 `$w`의 값은 기본값을 사용하고 싶고, `$h`만 지정하고 싶을 때처럼 인수 입력 순서에 상관없이 값을 넘겨주고 싶을때 사용할 수 있습니다.
  - 단, 작성하지 않은 인수가 적용 가능하도록 기본값을 설정해 주어야 합니다.
- 사용 방법 및 예시는 다음과 같습니다.

  ```scss
  @mixin 믹스인이름($매개변수A: 기본값, $매개변수B: 기본값) {
    스타일;
  }
  @include 믹스인이름($매개변수B: 인수);

  @mixin size($w: 100px, $h: 100px) {
    width: $w;
    height: $h;
  }
  .box {
    @include size($h: 50px);
  }
  ```

##### 2.7.3.3 가변 인자

- 입력할 인수의 개수가 불확실한 경우에 가변 인수를 사용할 수 있습니다.
  - 가변 인수 사용 방법은 매개변수 뒤에 `...`을 붙여주면 됩니다.
- 사용 방법 및 예시는 다음과 같습니다.

  ```scss
  @mixin 믹스인이름($매개변수...) {
    스타일;
  }
  @include 믹스인이름(인수A, 인수B, 인수C);

  @mixin bg($w, $h, $bg-values...) {
    width: $w;
    height: $h;
    background: $bg-values;
  }
  .div {
    @include bg(100px, 200px, url("images/x.png") no-repeat 10px 20px,
                url("images/y.png") no-repeat
                );
  }
  ```

##### 2.7.3.4 @content

- `@mixin`에 `@content`가 포함되어 있다면 해당 부분에 원하는 스타일 블록을 전달할 수 있습니다. 해당 방식을 사용하게 되면 기존 `@mixin`이 가지고 있는 기능에 추가로 선택자나 속성 등을 작성할 수 있습니다.
- 사용 방법 및 예시

  ```scss
  @mixin 믹스인이름() {
    스타일;
    @content;
  }
  @include 믹스인이름() {
    /* 스타일 블록 */
    스타일;
  }

  @mixin icon($url) {
    $::after {
      content: $url;
      @content;
    }
  }
  .icon1 {
    @include icon("/images/icon.png") {
      /* 위에 @content 부분에 해당 스타일 블록이 들어갑니다.*/
      position: absolute;
    };
  }
  ```

### 2.8 확장 (Extend)

- 특정 선택자가 기존에 있는 스타일을 상속받고자 하는 경우에 사용할 수 있습니다.
- `@extend`를 사용하기 전에 다음과 같은 문제를 고려해야 합니다.
  - 상속을 받으려는 현재 선택자가 어디에 첨부될 것인가?
  - 상속을 사용함으로써 원치 않는 부작용이 생길 수 있는가?
  - 이 한 번의 확장으로 얼마나 큰 CSS가 생성될 수 있는가?
    > 상속은 위에서 본 것처럼 고려할 부분이 많습니다.
    > 그러므로 `@extend`의 사용은 가급적으로 자제하고 `@mixin`을 사용하는 것을 추천합니다.
- 사용 방법 및 예시는 다음과 같습니다.

  ```scss
  @extend 선택자;

  .error {
    border: 1px #f00;
    background-color: blue;
  }
  .seriousError {
    @extend .error;
    border-width: 3px;
  }

  /* 컴파일 결과는 아래와 같습니다 */
  /* @extend 사용시 ','로 구분하는 다중 선택자가 생성됩니다. */
  .error,
  .seriousError {
    border: 1px #f00;
    background-color: blue;
  }
  .seriousError {
    border-width: 3px;
  }
  ```

### 2.9. 함수 (Function)

`@mixin`과 비슷하지만 다른 부분은 mixin은 정의된 스타일을 인자를 받아 반환하지만 함수는 `@retrun`을 통해 값을 반환합니다 호출 할 때도 `@include`와 다르게 `함수이름(인수)`와 같은 방법으로 바로 호출할 수 있습니다.

다음과 같이 사용할 수 있습니다.

```scss
@function 함수이름($매개변수) {
  @return 값;
}
```

예를 들어 다음과 같이 분할된 컬럼에서 차지할 비율을 정하는 함수를 생성할 수 있습니다.

```scss
@function columns($max-width, $number: 1, $columns: 12) {
  @return $max-width * ($number / $columns);
}
```

가변인자를 리스트로 받아 순회하여 사용할 수도 있습니다.

```scss
@function test($args...) {
  $new: ();
  @each $arg in $args {
    $new: append($new, $arg + 1);
  }
  @return $new;
}

div {
  test: test(1, 2, 3, 4, 5, 6);
}
```

```scss
div {
  test: 2 3 4 5 6 7;
}
```

매개 변수에 초기값을 지정 해 줄수도 있으며 필요시에는 인자를 받아 매개변수에 할당해서 호출 할 수 있습니다.

```scss
.box_group {
  $width: 980px;

  .box1 {
    width: columns($width); // 1, 12 => 1/12 = 81.667
  }
  .box2 {
    width: columns($width, 8); // 8, 12 => 8/12 = 653.33
  }
  .box3 {
    width: columns($width, 3); // 3, 12 => 3/12 =245
  }
}
```

이렇게 호출해서 사용할 수 있습니다. 물론 CSS, SCSS내의 내장함수와 함수 이름이 충돌하지 않도록 주의해야 합니다. 만약 함수이름을 rgb로 지정한다면 css에서 색상을 지정하는 `rgb()` 함수와 이름이 중복될 수 있습니다.

### 2.10. 조건과 반복 (Flow Control)

#### 2.10.1 조건문 if로 할당하기 (함수 사용)

if함수를 사용해서 다음과 같은 방법으로 조건문을 통해 값을 바로 할당할 수 있습니다.

```scss
$is: true;

div {
  width: if($is, 100px, auto);
  // if(조건, 참일경우, 거짓일경우)
}
```

일반적인 프로그래밍언어의 삼항 조건문과 비슷하게 사용됩니다.

#### 2.10.2 조건문 if로 분기 처리하기

`@if` 지시어를 통해 일반적인 분기 처리가 가능합니다. `@else` `@else if` 모두 사용 가능합니다. 조건을 넣을 때 괄호`()`는 선택사항 입니다.

```scss
$color: orange;
div {
  @if $color == strawberry {
    color: #fe2e2e;
  } @else if $color == orange {
    color: #fe9a2e;
  } @else if $color == banana {
    color: #ffff00;
  } @else {
    color: #2a1b0a;
  }
}
```

조건문에 논리 연산자 `and`, `or`, `not`또한 이용 가능합니다.

#### 2.10.3 증가하는 정수를 활용한 반복문 사용하기

반복문은 특정 횟수를 반복하면서 스타일을 생성합니다. 정수를 1씩 증가시키면서 변수에 값을 할당하여 사용 할 수 있습니다.

`@for` 지시어를 통해 사용할 수 있으며 두가지 방법이 있습니다.

```scss
@for $i from 1 through 3 {
  // for(i ; i <= 3 ; i++) 와 같습니다.
  //반복내용
}
@for $i from 1 to 3 {
  // for(i ; i < 3 ; i++) 와 같습니다.
  //반복내용
}
```

다음과 같이 `$i`에 할당된 값을 활용할 수 있습니다.

```scss
// 1부터 3번 반복
@for $i from 1 through 3 {
  .through:nth-child(#{$i}) {
    width: 20px * $i;
  }
}

// 1부터 3 직전까지만 반복(2번 반복)
@for $i from 1 to 3 {
  .to:nth-child(#{$i}) {
    width: 20px * $i;
  }
}
```

컴파일 한다면 다음과 같이 `$i`에 할당된 변수에 맞는 스타일을 생성합니다.

```scss
.through:nth-child(1) {
  width: 20px;
}
.through:nth-child(2) {
  width: 40px;
}
.through:nth-child(3) {
  width: 60px;
}

.to:nth-child(1) {
  width: 20px;
}
.to:nth-child(2) {
  width: 40px;
}
```

#### 2.10.4 List, Map의 요소를 활용한 반복문 사용하기

SCSS의 데이터 타입 중 List와 Map의 요소를 순회하며 반복문을 사용할 수 있습니다. 마치 프로그래밍 언어의 for in과 비슷합니다.

List와 함께 사용할 경우 다음과 같이 요소를 변수에 할당하여 사용할 수 있습니다.

```scss
$fruits: (apple, orange, banana, mango); // List 할당

.fruits {
  @each $fruit in $fruits {
    //fruits List안에 있는 요소들을 fruit변수에 할당하여 순회
    $i: index($fruits, $fruit); //index 함수를 사용해서 fruits안의 $fruit요소의 인덱스를 할당
    li:nth-child(#{$i}) {
      left: 50px * $i;
    }
    li:nth-child(#{$i})::after {
      content: quote($fruit);
    }
  }
}
```

위와 같은 방식으로 인덱스를 활용할 수도 있습니다. index함수를 사용할 때 인덱스는 1부터 시작합니다. 참고로 결과는 다음과 같습니다

```scss
.fruits li:nth-child(1) {
  left: 50px;
}
.fruits li:nth-child(1)::after {
  content: 'fruit';
}
.fruits li:nth-child(2) {
  left: 100px;
}
.fruits li:nth-child(2)::after {
  content: 'fruit';
}

//... 생략 ...
```

Map의 요소를 순회할 수도 있습니다. Map은 Key와 Value가 있기 때문에 각 각 할당하여 순회합니다.

```scss
$fruits-data: (
  apple: korea,
  orange: china,
  banana: japan,
);

@each $fruit, $country in $fruits-data {
  // fruits-data Map의 Key를 $fruit에 Value를 $country에 할당하여 순회
  .box-#{$fruit} {
    background: url('/images/#{$country}.png');
  }
}
```

#### 2.10.5 조건문을 통한 반복 사용하기

`@while` 지시어를 통해 조건이 `false`가 될 때까지 계속 반복할 수 있습니다. 이 또한 프로그래밍 언어의 while과 비슷합니다.

```scss
$i: 6;

@while $i > 0 {
  // $i가 0보다 작아지면 종료합니다.
  .item-#{$i} {
    width: 2px * $i;
  }
  $i: $i - 2; // 반복 할 때마다 $i를 2씩 감소시킵니다.
}
```

```scss
.item-6 {
  width: 12px;
}
.item-4 {
  width: 8px;
}
.item-2 {
  width: 4px;
}
```

### 2.11. SASS의 내장함수 (Built-In Modules)

sass에는 다양한 내장함수를 제공하여 스타일규칙을 만드는데 도움을 주고 있습니다. 모든 내장함수는 [Sass Built-in Modules](https://sass-lang.com/documentation/modules)에서 확인 할 수 있으며, 유용하게 사용되는 내장 함수들을 소개합니다.

> SASS에서 인덱스는 1부터 시작합니다.

#### 2.11.1 색상 관련 내장함수

- `mix(\$color1, \$color2)` : 두 개의 색을 섞습니다.

  ```scss
  $new-color: mix(rgb(245, 0, 0), rgb(0, 0, 245));
  //빨강과 파랑을 인수로 지정

  div {
    background: $new-color;
  }
  // 결과

  div {
    background: #7b007b; // 보라색
  }
  ```

- `lighten(\$color, \$amount)` : 백분률을 지정하여 밝게 합니다.
- `darken(\$color, \$amount)` : 백분률을 지정하여 어둡게 합니다.
- `saturate(\$color, \$amount)` : 백분률을 지정하여 채도를 높입니다.
- `desaturate(\$color, \$amount)` : 백분률을 지정하여 채도를 낮춥니다.
- `grayscale(\$color)` : 색의 채도를 제거하여 회색조로 만듭니다.
- `invert(\$color)` : 반전된 색을 반환합니다.
- `opacify(\$color, \$amount)` / `fade-in(\$color, \$amount)` : 색상의 불투명도를 추가합니다.
- `transparentize(\$color, \$amount)` / `fade-out(\$color, \$amount)` : 색상의 투명도를 추가합니다.

#### 2.11.2 문자 관련 내장함수

- `unquote(\$string)` : 문자열에서 따옴표를 제거하여 반환합니다.
- `quote(\$string)` : 문자열에 따옴표를 추가하여 반환합니다.
- `str-insert(\$string, \$insert, \$index)` : 문자열 사이에 인덱스를 통해 다른 문자를 추가하여 반환합니다.
- `str-index(\$string, \$substring)` : 문자열에서 특정 문자의 인덱스를 반환합니다.
- `str-length(\$string)` : 문자열의 길이를 반환합니다.
- `str-slice(\$string, \$start-at, [\$end-at])` : 문자열을 인덱스를 이용해 슬라이싱하여 반환합니다.
- `to-upper-case(\$string)` : 문자열을 대문자로 변환하여 반환합니다.
- `to-lower-case(\$string)` : 문자열을 대문자로 변환하여 반환합니다.

#### 2.11.3 숫자 관련 내장함수

- `percentage(\$number)` : 숫자를 백분률로 변환해 반환합니다. 0은 0% 1은 100% 입니다.
- `round(\$number)` : 정수로 반올림하여 반환합니다.
- `ceil(\$number)` : 정수로 올림하여 반환합니다.
- `floor(\$number)` : 정수로 내림(버림)하여 반환합니다.
- `abs(\$number)` : 숫자의 절대 값을 반환합니다.
- `min(\$numbers…)` : 숫자 중 최소 값을 찾아 반환합니다.
- `max(\$numbers…)` : 숫자 중 최대 값을 찾아 반환합니다.
- `random()` : 0 부터 1 사이의 난수를 반환합니다.

#### 2.11.4 List 관련 내장함수

> List 관련 내장함수는 모두 Map에서도 (1: 2, 3: 4) ghrdms (1 2, 3 4)의 꼴로 사용 가능합니다.

- `length(\$list)` : List의 요소의 개수를 반환합니다.
- `nth(\$list, \$index)` : List에서 인덱스에 해당하는 값을 반환합니다.
- `set-nth(\$list, \$index, \$value)` : List에서 n번째 값을 다른 값으로 변경하여 반환합니다.
- `join(\$list1, \$list2, [\$separator])` : 두개의 리스트를 하나로 결합하여 반환합니다.
- `append(\$list, \$val, [\$separator])` : 리스트의 뒤쪽에 요소를 추가하여 반환합니다.
- `zip(\$lists…)` : 여러 List들을 하나의 다차원 List로 결합해 반환합니다.
- `index(\$list, \$value)` : List에서 요소의 index를 반환합니다.

#### 2.11.5 Map 관련 내장함수

- `map-get(\$map, \$key)` : Map에서 특정 key의 value를 반환합니다.
- `map-merge(\$map1, \$map2)` : 두 개의 Map을 병합하여 새로운 Map를 만들어 반환합니다.
- `map-keys(\$map)` : Map에서 모든 key만 List로 반환합니다.
- `map-values(\$map)` : Map에서 모든 value만 List로 반환합니다.

#### 2.11.6 환경 관련 내장함수

- `variable-exists(\$name)` : 변수의 이름을 인자로 받아 해당 변수가 현재 스코프에 존재 하는지 확인하여 불리언 값을 반환합니다.
- `global-variable-exists(\$name, \$module: null)` : 변수의 이름을 인자로 받아 해당 변수가 글로벌 변수로 존재 하는지 확인하여 불리언 값을 반환합니다. 필요에 따라 모듈의 이름을 넣어 네임스페이스를 지정 해 줄 수 있습니다.
- `mixin-exists(\$name, \$module: null)` : 믹스인의 이름을 인자로 받아 해당 믹스인이 현재 존재하는지 확인하여 불리언 값을 반환합니다. 필요에 따라 모듈의 이름을 넣어 네임스페이스를 지정 해 줄 수 있습니다.
- `function-exists(\$name)` : 함수의 이름을 인자로 받아 해당 함수가 존재하는지 확인하여 불리언 값을 반환합니다.
- `type-of(\$value)` : 인수로 받은 데이터의 타입을 반환합니다. 아래의 데이터 타입중 하나를 출력합니다.
  - number
  - string
  - color
  - list
  - map
  - bool
  - null
  - function
  - arglist
- `keywords(\$args)` : 믹스인에서 다수의 인자를 받은 경우 Map 형식으로 변환하여 반환합니다.
- `get-function(\$name, \$css: false, \$module: null)` : 함수를 변수에 할당하여 반환합니다. 변수에 할당된 함수는 인수로 전달하거나 List안에 포함시킬 수 있습니다. 변수로 지정된 함수는 call 내장함수를 통해 사용해야 합니다. 만약 get-function 함수의 인자 중 $css를 true로 호출한다면 일반 css함수를 바로 반환하기 때문에 일회성으로 사용할 수 있습니다. 필요에 따라 모듈의 이름을 넣어 네임스페이스를 지정 해 줄 수 있습니다.
- `call($function, $args...)` : `get-function` 함수를 통해 변수에 할당한 함수를 호출시킬 수 있습니다. 첫번째 인자에 함수를 할당한 변수명을 넣고, 인수로 전달할 값을 이어서 넣어 값을 반환받습니다.

## 참고 자료

- [Sass guidelines](https://sass-guidelin.es/ko/)
- [Sass documntation](https://sass-lang.com/documentation)
- [Heropy](https://heropy.blog/2018/01/31/sass/)
- [Poiemaweb](https://poiemaweb.com/sass-css-extention)
