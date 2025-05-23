# Chapter 06: 예제 애플리케이션

## 선언형(Declarative) 코딩

이제부터는 사고방식을 전환하려고 합니다. 지금까지는 컴퓨터에게 작업을 어떻게 수행해야 하는지를 지시했지만, 이제는 원하는 결과가 무엇인지를 명세하는 방식으로 코드를 작성할 것입니다. 모든 것을 일일이 관리하려고 애쓰느 것보다 훨씬 스트레스를 덜 받게 될겁니다.

선언형은 명령형과 반대되는 개념으로, 단계별 명령을 작성하는 대신 표현식(expression) 을 사용하여 작성하는 방식을 의미합니다.

SQL을 떠올려 보세요. SQL에서는 "먼저 이것을 하고, 다음에 저것을 하라"고 명령하지 않습니다. 대신 하나의 표현식으로 우리가 데이터베이스에서 원하는 것이 무엇인지를 명시합니다. 어떻게 처리할지는 우리가 결정하지 않고, 데이터베이스가 알아서 처리합니다. 데이터베이스가 업그레이드되거나 SQL 엔진이 최적화되어도, 쿼리를 바꿀 필요는 없습니다. 왜냐하면 우리의 명세를 해석하는 방법은 여러 가지가 있으며, 결과는 동일하게 유지되기 때문입니다.

저를 포함해서 어떤 분들에게는 선언형 코딩이라는 개념이 처음엔 다소 이해하기 어려울 수 있습니다. 그래서 몇 가지 예시를 통해 그 감각을 익혀보도록 하겠습니다.

```js
// 명령형(imperative)
const makes = [];
for (let i = 0; i < cars.length; i += 1) {
  makes.push(cars[i].make);
}

// 선언형(declarative)
const makes = cars.map((car) => car.make);
```

명령형 루프는 먼저 배열을 생성해야 합니다. 인터프리터는 이 구문을 평가한 후에야 다음 단계로 넘어갈 수 있습니다. 그 다음엔 자동차 리스트를 직접 반복하면서, 카운터를 수동으로 증가시키고, 반복의 내부 작동 과정을 노골적으로 보여줍니다. 말 그대로 반복 과정을 적나라하게 드러내는 방식입니다.

반면 `map` 버전은 하나의 표현식입니다. 특정한 실행 순서를 강제하지 않으며, map 함수가 어떻게 반복을 수행하고 반환된 배열이 어떻게 구성되는지에 대해서도 다양한 자유도가 존재합니다. 이 코드는 *무엇*을 할 것인지를 명시할 뿐, _어떻게_ 할 것인지는 언급하지 않습니다. 그렇기 때문에 이 코드는 반짝이는 선언형의 휘장을 두르고 있는 것이지요.

map 함수는 더 명확하고 간결할 뿐만 아니라, 내부적으로 얼마든지 최적화될 수 있습니다. 그 과정에서도 우리가 작성한 소중한 애플리케이션 코드는 변경할 필요가 없습니다.

혹시 "그래도 명령형 루프가 훨씬 빠르지 않나요?"라고 생각하시는 분이 계시다면, JIT(Just-In-Time) 컴파일러가 코드 최적화를 어떻게 수행하는지를 한 번 공부해보시길 권해드립니다. 이에 대한 통찰을 줄 수 있는 [훌륭한 영상](https://www.youtube.com/watch?v=g0ek4vV7nEA)도 있습니다.

다른 예시도 살펴봅시다.

```js
// imperative
const authenticate = (form) => {
  const user = toUser(form);
  return logIn(user);
};

// declarative
const authenticate = compose(logIn, toUser);
```

명령형 버전이 반드시 잘못된 것은 아니지만, 여전히 코드 안에는 단계별 평가 순서가 암묵적으로 내포되어 있습니다. 반면 `compose` 표현식은 단순히 하나의 사실을 진술합니다 : 인증이란, `toUser`와 `logIn`의 합성이다. 이러한 방식은 보조 코드의 변경 가능성을 열어두며, 결과적으로 애플리케이션 코드를 고수준의 명세로 만들어 줍니다.

위 예제에서는 평가 순서가 명확히 정해져 있습니다 (`toUser`가 먼저 호출되고, 그 다음에 `logIn`).
하지만 실제로는 평가 순서가 중요하지 않은 경우도 많으며, 이런 경우 선언형 코딩을 통해 쉽게 표현할 수 있습니다 (이 부분은 뒤에서 더 자세히 다룰 예정입니다).

우리는 평가 순서를 코드에 직접 명시하지 않아도 되기 때문에, 선언형 코딩은 병렬 컴퓨팅에 잘 어울립니다.
이 점은 순수 함수(pure function) 와 결합될 때 더욱 강력해지며, 함수형 프로그래밍이 병렬 처리가 중요한 미래 환경에서 적합한 선택인 이유이기도 합니다. 즉, 병렬 혹은 동시성 시스템을 만들기 위해 특별한 작업을 하지 않아도 되는 것입니다.

## 함수형 프로그래밍의 반짝임

이제부터 선언적이고 조합 가능한 방식으로 하나의 예제 애플리케이션을 만들어보겠습니다.
물론 당장은 약간의 반칙으로 사이드 이펙트(부수 효과)를 사용하겠지만, 그 범위를 최소화하고, 순수한 코드와는 분리하여 유지할 것입니다. 우리는 이제 Flickr 이미지를 가져와서 표시하는 브라우저 위젯을 만들 것입니다.

먼저 애플리케이션의 기본 구조부터 잡아보겠습니다. 아래는 해당 HTML입니다:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Flickr App</title>
  </head>
  <body>
    <main id="js-main" class="main"></main>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/require.js/2.2.0/require.min.js"></script>
    <script src="main.js"></script>
  </body>
</html>
```

그리고 아래는 main.js의 뼈대 코드입니다:

```js
const CDN = (s) => `https://cdnjs.cloudflare.com/ajax/libs/${s}`;
const ramda = CDN("ramda/0.21.0/ramda.min");
const jquery = CDN("jquery/3.0.0-rc1/jquery.min");

requirejs.config({ paths: { ramda, jquery } });
requirejs(["jquery", "ramda"], ($, { compose, curry, map, prop }) => {
  // app goes here
});
```

우리는 Lodash나 다른 유틸리티 라이브러리 대신 [Ramda](https://ramdajs.com)를 사용합니다. Ramda에는 `compose`, `curry` 같은 함수들이 포함되어 있습니다. 또한 이 책 전반에서 일관성을 유지하기 위해 requirejs를 사용하고 있는데, 처음에는 조금 과하게 느껴질 수도 있지만, 계속 사용하게 될 것이니 익숙해지시면 됩니다.

이제 준비는 되었으니, 앱의 명세를 살펴보겠습니다. 우리의 애플리케이션은 다음 네 가지 작업을 수행하게 됩니다:

1. 특정 검색어를 기반으로 URL을 생성합니다.
2. Flicker API를 호출합니다.
3. 응답받은 JSON 데이터를 HTML 이미지로 변환합니다.
4. 변환된 이미지를 화면에 표시합니다.

위 작업들 중에 순수하지 않은(impure) 동작이 두 가지 있다는 걸 눈치채셨나요? 바로 Flickr API로부터 데이터를 가져오는 작업과 화면에 이미지를 표시하는 작업입니다. 이 동작들을 격리시키기 위해 먼저 정의해보겠습니다. 또한 디버깅을 좀 더 쉽게 할 수 있도록, 우리가 애용하는 `trace` 함수도 추가하겠습니다.

```js
const Impure = {
  getJSON: curry((callback, url) => $.getJSON(url, callback)),
  setHtml: curry((sel, html) => $(sel).html(html)),
  trace: curry((tag, x) => {
    console.log(tag, x);
    return x;
  }),
};
```

여기서는 jQuery의 메서드들을 단순히 커리 형태로 감싸고, 인자들의 순서를 더 유리한 방식으로 재배치했습니다. 이 함수들이 순수하지 않은 동작임을 명확히 하기 위해 `Impure`라는 네임스페이스로 묶어 두었습니다. 나중에 나올 예제에서는, 이 두 함수도 순수 함수로 전환할 예정입니다.

이제 `Impure.getJSON` 함수에 전달할 URL을 생성해야 합니다.

```js
const host = "api.flickr.com";
const path = "/services/feeds/photos_public.gne";
const query = (t) => `?tags=${t}&format=json&jsoncallback=?`;
const url = (t) => `https://${host}${path}${query(t)}`;
```

`url` 함수를 모노이드나 콤비네이터를 이용해 pointfree 스타일로 작성하는, 화려하지만 지나치게 복잡한 방법들도 있습니다(이에 대해서는 나중에 배우게 됩니다). 하지만 우리는 가독성을 우선으로 두고, 익숙한 pointful 방식으로 문자열을 조립하기로 했습니다.

이제 API를 호출하고, 그 결과를 화면에 표시하는 app 함수를 작성해봅시다.

```js
const app = compose(Impure.getJSON(Impure.trace("response")), url);
app("cats");
```

This calls our `url` function, then passes the string to our `getJSON` function, which has been partially applied with `trace`. Loading the app will show the response from the api call in the console.

이 코드는 먼저 `url` 함수를 호출한 뒤, 생성된 문자열을 `getJSON` 함수에 전달합니다.
이때 getJSON은 `trace`로 부분 적용(partial application) 되어 있어, 디버깅 정보를 출력할 수 있습니다.
앱을 실행하면, API 호출 결과가 콘솔에 표시되는 것을 확인할 수 있습니다.

<img src="images/console_ss.png" alt="console response" />

이제 이 JSON 데이터를 기반으로 이미지를 생성하고자 합니다. 데이터 구조를 보면, 우리가 원하는 `mediaUrls`는 먼저 `items` 배열 안에 들어 있고, 그 안의 각 항목마다 `media`라는 객체가 있으며, 거기에서 다시 `m` 속성을 꺼내야 합니다.

이처럼 중첩된 속성에 접근할 때는 Ramda에서 제공하는 범용 getter 함수인 `prop`을 사용할 수 있습니다.
prop이 실제로 어떻게 동작하는지 이해할 수 있도록, 아래에 간단한 버전으로 직접 구현해 보았습니다:

```js
const prop = curry((property, object) => object[property]);
```

사실 별거 없습니다. 그냥 일반적인 `[]` 문법을 사용해서 객체의 속성에 접근할 뿐이에요. 이제 이걸 활용해서 우리가 원하는 `mediaUrls`를 추출해 봅시다.

```js
const mediaUrl = compose(prop("m"), prop("media"));
const mediaUrls = compose(map(mediaUrl), prop("items"));
```

먼저 `items`를 모은 다음, 그 위에서 `map`을 실행해 각 항목에서 media url을 추출해야 합니다.
그렇게 하면 우리가 원하는 `mediaUrls`의 배열이 만들어집니다. 이제 이 부분을 애플리케이션에 연결하고, 화면에 출력해 봅시다.

```js
const render = compose(Impure.setHtml("#js-main"), mediaUrls);
const app = compose(Impure.getJSON(render), url);
```

우리가 한 일은 단지 새로운 함수 조합(composition)을 만든 것뿐입니다. 이 조합은 `mediaUrls` 함수를 호출하고, 그 결과를 `<main>` 태그 안에 설정해 줍니다. 이제는 더 이상 원시 JSON만 출력하는 것이 아니기 때문에, `trace` 호출을 `render`로 교체했습니다. 이렇게 하면 `mediaUrls`가 화면 본문에 대강 표시되게 됩니다.

마지막 단계는 이 `mediaUrls`를 진짜 `images`들로 변환하는 것입니다.
좀 더 큰 규모의 애플리케이션이라면 Handlebars나 React 같은 템플릿/DOM 라이브러리를 사용하겠지만,
지금 만드는 이 간단한 앱에서는 단순히 img 태그만 필요하므로 jQuery를 계속 사용하겠습니다.

```js
const img = (src) => $("<img />", { src });
```

jQuery의 `html` 메서드는 태그 배열도 받아들일 수 있습니다. 우리는 단지 mediaUrls를 이미지 태그로 변환한 뒤, 그것을 `setHtml` 함수에 전달하기만 하면 됩니다.

```js
const images = compose(map(img), mediaUrls);
const render = compose(Impure.setHtml("#js-main"), images);
const app = compose(Impure.getJSON(render), url);
```

자, 이제 끝났습니다!

<img src="images/cats_ss.png" alt="cats grid" />

완성된 스크립트는 다음과 같습니다.
[include](./exercises/ch06/main.js)

보세요. 이 코드는 무언가가 어떻게 만들어지는지가 아니라, 무엇인지를 설명하는 아름답게 선언적인 명세입니다. 이제 코드의 각 줄을 성질을 가진 하나의 수식처럼 바라볼 수 있습니다. 이러한 성질들을 바탕으로 애플리케이션을 이해하거나 리팩터링하는 데 활용할 수 있습니다.

## A Principled Refactor

현재 코드는 각 item에 대해 한 번 map을 하여 mediaUrl로 변환하고, 다시 그 mediaUrls에 대해 또 한 번 map을 하여 img 태그로 변환합니다. map과 함수 조합(composition)에 관련된 법칙이 존재합니다:

```js
// map's composition law
compose(map(f), map(g)) === map(compose(f, g));
```

이 법칙을 활용하여 코드를 최적화할 수 있습니다. 자, 원칙에 따라 리팩터링을 해봅시다.

```js
// original code
const mediaUrl = compose(prop("m"), prop("media"));
const mediaUrls = compose(map(mediaUrl), prop("items"));
const images = compose(map(img), mediaUrls);
```

우선 map 함수들을 한 줄로 정렬해 봅시다. 등식 추론(equational reasoning)과 순수성 덕분에, `images` 함수 안에 있던 `mediaUrls` 호출을 인라인으로 가져올 수 있습니다.

```js
const mediaUrl = compose(prop("m"), prop("media"));
const images = compose(map(img), map(mediaUrl), prop("items"));
```

이제 `map`들을 정렬했으니, 함수 조합 법칙을 적용할 수 있습니다.

```js
/*
compose(map(f), map(g)) === map(compose(f, g));
compose(map(img), map(mediaUrl)) === map(compose(img, mediaUrl));
*/

const mediaUrl = compose(prop("m"), prop("media"));
const images = compose(map(compose(img, mediaUrl)), prop("items"));
```

이제 이 코드는 각 항목을 img 태그로 변환하면서 한 번만 반복하게 됩니다. 조금 더 읽기 쉽도록, 해당 함수를 분리해 내봅시다.

```js
const mediaUrl = compose(prop("m"), prop("media"));
const mediaToImg = compose(img, mediaUrl);
const images = compose(map(mediaToImg), prop("items"));
```

## 요약

작지만 실제로 동작하는 애플리케이션을 통해, 새로 배운 기술들을 어떻게 활용할 수 있는지 살펴보았습니다. 수학적 프레임워크를 사용해 코드를 이해하고 리팩터링하는 방법도 경험했습니다. 그런데 오류 처리와 코드 분기(조건 처리)는 어떻게 할까요? 파괴적인 함수에 네임스페이스를 붙이는 것에 그치지 않고, 애플리케이션 전체를 어떻게 순수하게 만들 수 있을까요? 어떻게 하면 앱을 더 안전하고 표현력 있게 만들 수 있을까요? 이 질문들이 바로 2부에서 다룰 주제들입니다.

[Chapter 07: 힌들리 밀러(Hindley-Milner)와 나](ch07-kr.md)
