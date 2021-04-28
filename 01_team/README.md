### 2.6 연산 (Operations)
Sass는 기본적인 연산 기능을 제공하는데, 해당 연산을 사용하여 레이아웃 작업 시 상황에 맞게 크기를 계산하거나 정해진 값을 나눠서 작성할 수 있어 유용합니다. Sass에서 사용할 수 있는 연산자의 종류로는 산술, 비교, 논리 연산자가 존재합니다.

종류 | 연산자 | 설명 | 주의사항
:---:|:---:|:---:|:---|
산술| `+` | 더하기 |
산술| `-` | 빼기   |
산술| `*` | 곱하기 | 피연산자 중 하나의 값이 반드시 숫자
산술| `/` | 나누기 | 나누는 값이 반드시 숫자
산술| `%` | 나머지 |
비교| `==` | 동등
비교| `!=` | 부등
비교| `<` | 보다 작은
비교| `>` | 보다 큰
비교| `<=` | 보다 작거나 같은
비교| `>=` | 보다 크거나 같은
논리| `and` | 그리고
논리| `or` | 또는
논리| `not` | 부정


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

  width: $width / 2;                  // 1번의 경우 -> 150px
  height: (300px / 2);                // 2번의 경우 -> 150px
  margin-right: 10px + 20px / 2px;    // 3번의 경우 -> 20px
  ```

#### 2.6.2 색상 (Colors)
- 모든 산술 연산자를 사용하여 색상값도 연산할 수 있습니다.
  ```scss
  p {
    color: #010203 + #040506; // #050709
    color: #010203 * 2 ;      // #020406
    color: rbga(255, 0, 0, 0.75)
  }
  ```
- rgba 연산은 alpha 값이 동일해야 가능합니다.
  - 다만, alpha 값은 연산되지 않습니다.
  - alpha 값 연산을 위해서는 `opacify()`, `transparentize()` 함수를 사용해야 합니다.
  ```scss
  color: rgba(255, 0, 0, 0.3) + rgba(0, 0, 255, 0.3);  // rgba(255, 0, 255, 0.3)
  ```

#### 2.6.3 문자 (Strings)
- 문자 연산에는 `+` 연산자가 사용되는데 자바스크립트와 같이 문자열을 연결할 수 있습니다.
- 문자 연산의 결과는 왼쪽 피연산자를 기준으로 합니다.
  ```scss
    div::after {
      /* 왼쪽 피연산자가 따옴표로 묶여있을시 연산 결과도 따옴표로 묶여있습니다. */
      content: "Hello " + World; // "Hello World"
      /* 왼쪽 피연산자가 따옴표로 묶여있지 않을시 연산 결과도 묶여있지 않습니다.*/
      flex-flow: row + "-reverse" + " " + wrap; // row-reverse wrap
    }
  ```

#### 2.6.4 논리 (Boolean)
- 논리 연산자는 자바스크립트의 `&&`, `||`, `!`와 같은 기능을 하는 연산자이며, `Sass`의 조건문인 `@if`와 `built-in if()` 함수에서 사용됩니다.
- 논리 연산자의 종류로는 `and(그리고)`, `or(또는)`, `not(부정)`이 있습니다.
  ```scss
  $width: 90px;

  div {
    @if not ($width > 100px) {                // True
      height: 300px;
    }

    @if ($width > 40px and $width < 90px) {   // False
      height: 500px;
    }

    @if ($width > 40px or $width < 90px) {    // True
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
          content: "!!";
        }

        span.icon {
          background-image: url("/image/smail-icon.png");
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

  .box1 { @include solid-line(1px, red); }
  .box2 { @include solid-line(4px, blue); }
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
  .box1 { @include size; }
  .box2 { @include size(50px, 60px); }
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
  .error, .seriousError {
    border: 1px #f00;
    background-color: blue;
  }
  .seriousError {
    border-width: 3px;
  }
  ```

## 참고 자료
[sass guidelines](https://sass-guidelin.es/ko/)
[heropy](https://heropy.blog/2018/01/31/sass/)
[poiemaweb](https://poiemaweb.com/sass-css-extention)