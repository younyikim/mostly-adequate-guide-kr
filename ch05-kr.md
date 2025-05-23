# Chapter 05: 구성(Composing)을 통한 코딩

## 함수형 관리

다음은 `compose` 함수입니다:

```js
const compose =
  (...fns) =>
  (...args) =>
    fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];
```

... 놀라지 마세요! 이것은 _compose_ 함수의 레벨 9000 초사이언 버전입니다. 이해를 돕기 위해, 일단은 이와 같은 가변 인자 구현은 잠시 제쳐두고, 두 개의 함수를 조합하는 더 단순한 형태부터 살펴보겠습니다. 그 원리를 이해하고 나면, 더 많은 수의 함수에도 적용 가능한 방식으로 추상화를 확장할 수 있습니다. (사실 그게 가능하다는 것도 증명할 수 있습니다!)

사랑하는 독자 여러분을 위해, 좀 더 친절한 _compose_ 버전을 준비했습니다:

```js
const compose2 = (f, g) => (x) => f(g(x));
```

`f`와 `g`는 함수이고, `x`는 이 함수들을 "파이프"처럼 통과하는 값입니다.

함수 조합(composition)은 마치 함수들을 교배하는 일처럼 느껴집니다. 여러분은 함수의 사육사처럼, 원하는 특성을 가진 두 함수를 골라 결합하여 전혀 새로운 함수를 만들어냅니다.

사용 방법은 다음과 같습니다 :

```js
const toUpperCase = (x) => x.toUpperCase();
const exclaim = (x) => `${x}!`;
const shout = compose(exclaim, toUpperCase);

shout("send in the clowns"); // "SEND IN THE CLOWNS!"
```

두 함수를 조합하면 새로운 함수가 반환됩니다. 이는 매우 자연스러운 일입니다. 어떤 타입의 두 개의 단위를 조합했을 때, 그 결과도 동일한 타입의 새로운 단위가 되는 것이죠. 레고 두 개를 결합한다고 해서 갑자기 링컨 로그가 되는 건 아니잖아요. 여기에는 이론적인 기반, 즉 우리가 곧 알아가게 될 어떤 근본적인 번칙이 존재합니다.

우리가 정의한 `compose` 함수에서는 `g`가 먼저 실행되고 , 그 결과가 `f`로 전달되어 오른쪽에서 왼쪽으로 데이터가 흐르게 됩니다. 이 방식은 여러 함수를 중첩해서 호출하는 것보다 훨씬 더 가독성이 좋습니다. `compose` 없이 작성하면 다음과 같이 보이게 됩니다:

```js
const shout = (x) => exclaim(toUpperCase(x));
```

안쪽에서 바깥쪽으로 실행하는 대신, 우리는 오른쪽에서 왼쪽으로 실행합니다. 이는 어쩌면 왼쪽으로 가는 한 걸음이라고 볼 수 있겠네요 (부우~ 농담입니다!).
이제 순서가 중요한 예제를 하나 살펴보겠습니다:

```js
const head = (x) => x[0];
const reverse = reduce((acc, x) => [x, ...acc], []);
const last = compose(head, reverse);

last(["jumpkick", "roundhouse", "uppercut"]); // 'uppercut'
```

`reverse` 함수는 리스트의 순서를 뒤집고, `head` 함수는 첫 번째 항목을 가져옵니다. 이 두 함수를 조합하면 결과적으로 동작은 하지만 비효율적인 `last` 함수가 만들어집니다. 이 예제에서는 함수 조합의 실행 순서가 명확하게 드러납니다. 물론 왼쪽에서 오른쪽으로 실행되는 방식으로도 정의할 수 있지만, 현재처럼 작성된 형태가 수학에서의 함수 조합 방식과 훨씬 더 잘 일치합니다. 맞습니다, 함수 조합은 수학 책에서 바로 나온 개념입니다.
사실, 이제는 모든 함수 조합에 적용되는 하나의 성질을 살펴볼 시점이 된 것 같습니다.

```js
// 결합 법칙
compose(f, compose(g, h)) === compose(compose(f, g), h);
```

함수 조합은 결합법칙(associativity)을 따릅니다. 즉, 여러 함수를 조합할 때 괄호로 어떤 두 함수를 먼저 묶느냐는 중요하지 않습니다. 결과는 항상 동일합니다. 예를 들어, 문자열을 대문자로 변환하는 동작을 추가하고 싶다면, 다음과 같이 작성할 수 있습니다:

```js
compose(toUpperCase, compose(head, reverse));
// or
compose(compose(toUpperCase, head), reverse);
```

compose를 사용할 때 어떤 식으로 괄호를 묶든 결과는 동일하기 때문에, 우리는 이를 기반으로 가변 인자(variadic) 버전의 compose를 만들어 사용할 수 있습니다. 즉, 여러 함수를 한 번에 조합하여 다음과 같이 사용할 수 있습니다:

```js
// 이전에는 compose를 두 번 써야 했지만, 조합은 결합법칙을 따르기 때문에
// 이제는 원하는 만큼의 함수를 한 번에 compose에 넘겨주면,
// 내부적으로 어떻게 묶을지는 compose가 알아서 처리해줍니다.
const arg = ["jumpkick", "roundhouse", "uppercut"];
const lastUpper = compose(toUpperCase, head, reverse);
const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

lastUpper(arg); // 'UPPERCUT'
loudLastUpper(arg); // 'UPPERCUT!'
```

결합법칙(associativity)을 적용하면, 이렇게 유연하게 함수를 조합할 수 있고, 어떻게 묶든 결과가 동일하다는 안심감도 함께 얻을 수 있습니다. 조금 더 복잡해 보일 수 있는 가변 인자 버전의 compose 정의는 이 책의 지원 라이브러리에 포함되어 있으며, [lodash][lodash-website], [underscore][underscore-website], and [ramda][ramda-website] 같은 유명한 라이브러리들에서도 일반적으로 동일한 방식으로 구현되어 있습니다.

결합법칙이 주는 또 하나의 유용한 장점은, 함수 묶음 중 일부를 따로 추출해 별도의 조합으로 만들 수 있다는 점입니다. 이제, 앞서 봤던 예제를 리팩터링해보며 그 이점을 살펴보겠습니다:

```js
const loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

// -- or ---------------------------------------------------------------

const last = compose(head, reverse);
const loudLastUpper = compose(exclaim, toUpperCase, last);

// -- or ---------------------------------------------------------------

const last = compose(head, reverse);
const angry = compose(exclaim, toUpperCase);
const loudLastUpper = compose(angry, last);

// more variations...
```

정답이나 오답은 없습니다 — 우리는 그냥 레고 블록을 원하는 방식으로 조립하고 있을 뿐입니다. 보통은 `last`나 `angry`처럼 재사용 가능한 방식으로 그룹화하는 것이 가장 좋습니다. 만약 파울러의 “[리팩토링][refactoring-book]”에 익숙하시다면, 이 과정을 “[함수 추출하기][extract-function-refactor]”와 유사하게 느끼실 수도 있을 겁니다… 다만, 객체 상태에 대한 걱정은 없다는 점이 다르죠.

## 포인트프리(Pointfree)

포인트프리 스타일이란, 데이터를 직접 언급하지 않는 함수 스타일을 말합니다. 농담이지만, “데이터를 말할 필요가 없다는 뜻”이죠. 좀 더 정확히 말하면, 함수가 자신이 다룰 데이터를 명시적으로 언급하지 않는 방식입니다. 일급 함수(first-class functions), 커링(currying), 그리고 함수 조합(composition)은 이 스타일을 만드는 데에 매우 잘 어울리는 조합입니다.

> 힌트: `replace`와 `toLowerCase`의 포인트프리 버전은 [Appendix C -
> Pointfree Utilities](./appendix_c.md)에 정의되어 있습니다. 망설이지 말고 한 번 들춰보세요!

```js
// 데이터인 word를 직접 언급하기 때문에 포인트프리 스타일이 아닙니다.
const snakeCase = (word) => word.toLowerCase().replace(/\s+/gi, "_");

// 포인트프리
const snakeCase = compose(replace(/\s+/gi, "_"), toLowerCase);
```

`replace`를 부분적으로 적용한 것을 보셨나요? 여기서 하는 일은 데이터를 한 인자를 받는 각 함수에 차례차례 통과시키는 것입니다. 커링 덕분에 각 함수는 자신의 데이터만 받아 처리하고, 결과를 다음 함수로 넘기도록 미리 준비할 수 있습니다. 또 한 가지 주목할 점은, 포인트프리 버전에서는 함수를 만들 때 데이터를 직접 사용할 필요가 없지만, 포인트풀한 버전에서는 함수 실행 전에 `word`라는 데이터가 반드시 준비되어 있어야 한다는 점입니다.

이제 다른 예제를 살펴보겠습니다.

```js
// 데이터인 name을 직접 언급하기 때문에 포인트프리 스타일이 아닙니다.
const initials = (name) =>
  name.split(" ").map(compose(toUpperCase, head)).join(". ");

// pointfree
// 참고: 여기서는 9장에서 소개된 join 대신 부록에 있는 intercalate를 사용합니다!
const initials = compose(
  intercalate(". "),
  map(compose(toUpperCase, head)),
  split(" ")
);

initials("hunter stockton thompson"); // 'H. S. T'
```

포인트프리 코드는 불필요한 이름을 제거하고 코드를 간결하고 일반적으로 유지하는 데 도움을 줍니다. 포인트프리는 입력을 받아 출력을 내는 작은 함수들이 잘 만들어졌다는 것을 알려주는, 함수형 코드의 좋은 판단 기준이 되기도 합니다. 예를 들어, while 루프는 함수 조합으로 표현할 수 없습니다. 하지만 주의할 점은, 포인트프리는 양날의 검과 같아서 때로는 의도를 흐릴 수도 있다는 것입니다. 모든 함수형 코드가 포인트프리일 필요는 없으며, 그것도 괜찮습니다. 가능한 곳에서는 포인트프리를 지향하되, 그렇지 않은 경우에는 일반 함수 방식을 유지하는 것이 좋습니다.

## 디버깅

흔히 발생하는 실수 중 하나는, 두 개의 인자를 받는 함수인 `map`을 먼저 부분 적용하지 않고 바로 조합하는 것입니다.

```js
// 잘못된 예 - 이렇게 하면 angry 함수에 배열이 직접 전달되고 map은 어떤 값으로 부분 적용되었는 지 알수 없다.
const latin = compose(map, angry, reverse);

latin(["frog", "eyes"]); // error

// 올바른 예 - 각 함수가 하나의 인자를 기대
const latin = compose(map(angry), reverse);

latin(["frog", "eyes"]); // ['EYES!', 'FROG!'])
```

함수 조합 디버깅에 어려움이 있다면, 다음과 같은 유용하지만 순수하지 않은(trace 함수) 함수를 사용해 내부 동작을 확인할 수 있습니다.

```js
const trace = curry((tag, x) => {
  console.log(tag, x);
  return x;
});

const dasherize = compose(
  intercalate("-"),
  toLower,
  split(" "),
  replace(/\s{2,}/gi, " ")
);

dasherize("The world is a vampire");
// TypeError: Cannot read property 'apply' of undefined
```

여기가 무언가 문제가 있군요. 같이 `trace` 해봅시다.

```js
const dasherize = compose(
  intercalate("-"),
  toLower,
  trace("after split"),
  split(" "),
  replace(/\s{2,}/gi, " ")
);

dasherize("The world is a vampire");
// after split [ 'The', 'world', 'is', 'a', 'vampire' ]
```

아! `toLower` 함수가 배열을 처리하고 있으니, `map`으로 감싸서 각각의 요소에 적용해야 합니다.

```js
const dasherize = compose(
  intercalate("-"),
  map(toLower),
  split(" "),
  replace(/\s{2,}/gi, " ")
);

dasherize("The world is a vampire"); // 'the-world-is-a-vampire'
```

trace 함수는 디버깅을 위해 특정 시점의 데이터를 확인할 수 있게 해줍니다. Haskell이나 PureScript 같은 언어에도 개발 편의를 위한 유사한 함수들이 존재합니다.

함수 조합은 우리가 프로그램을 구성하는 도구가 될 것이며, 운 좋게도 이를 뒷받침하는 강력한 이론이 있어 모든 것이 잘 작동할 것임을 보장합니다. 이제 이 이론을 살펴보겠습니다.

## 범주론(Category Theory)

범주론(Category theory)은 집합론, 타입 이론, 군론, 논리학 등 여러 수학 분야의 개념을 형식화할 수 있는 추상적인 수학 분야입니다. 범주론은 주로 객체(objects), 사상(morphisms), 그리고 변환(transformations)을 다루는데, 이 개념들은 프로그래밍과도 매우 밀접하게 닮아 있습니다.

아래는 각기 다른 이론에서 바라본 동일한 개념들을 정리한 도표입니다.

<img src="images/cat_theory.png" alt="category theory" />

놀라시지 마세요. 이 모든 개념을 완벽히 익히실 필요는 없습니다. 제가 말씀드리고 싶은 것은, 우리가 다루는 개념들이 얼마나 많이 중복되는지 보여드려서, 바로 그 이유 때문에 범주론이 이런 것들을 하나로 통합하려는 시도를 한다는 점입니다.

범주론에서는 범주(category)라는 개념이 있습니다. 범주는 다음 요소들로 정의되는 집합입니다:

- 객체들의 집합
- 사상들의 집합
- 사상들에 대한 조합(합성) 개념
- 특별한 사상인 항등 사상(identity)

범주론은 매우 추상적이어서 여러 분야를 모델링할 수 있지만, 지금은 우리가 관심 있는 타입과 함수에 이를 적용해 보겠습니다.

**객체들의 집합**
객체들은 데이터 타입이 됩니다. 예를 들어, `String`, `Boolean`, `Number`, `Object` 등이 있습니다. 데이터 타입을 가능한 값들의 집합으로 보는 경우가 많습니다. 예를 들어 `Boolean`은 `[true, false]`라는 집합으로, Number는 가능한 모든 숫자 값들의 집합으로 생각할 수 있습니다. 타입을 집합으로 다루는 것은 집합론을 활용할 수 있어 매우 유용합니다.

**사상들의 집합**
사상은 일상에서 사용하는 순수 함수들에 해당합니다.

**사상에 대한 조합 개념**
이미 눈치채셨겠지만, 이것이 바로 우리의 새로운 도구인 `compose` 함수입니다. 앞서 이야기했듯, `compose` 함수가 결합법칙(associativity)을 따르는 것은 우연이 아니며, 범주론에서 모든 조합에 반드시 성립해야 하는 성질입니다.

아래 이미지는 함수 조합의 개념을 시각적으로 보여줍니다:

<img src="images/cat_comp1.png" alt="category composition 1" />
<img src="images/cat_comp2.png" alt="category composition 2" />

다음은 코드로 본 구체적인 예시입니다:

```js
const g = (x) => x.length;
const f = (x) => x === 4;
const isFourLetterWord = compose(f, g);
```

**특별한 사상, 항등 사상(identity)**
유용한 함수인 `id`를 소개하겠습니다. 이 함수는 입력값을 그대로 받아서 그대로 반환하는 함수입니다. 다음 코드를 살펴보세요:

```js
const id = (x) => x;
```

“이게 도대체 무슨 쓸모가 있지?”라고 생각하실 수도 있습니다. 이 함수는 앞으로의 챕터들에서 매우 자주 사용될 예정이지만, 지금은 값을 대신할 수 있는 함수, 즉 일상적인 데이터를 흉내 내는 함수라고 생각하시면 됩니다.

`id` 함수는 compose와 잘 어울려야 합니다. 다음은 모든 단항 함수(단항 함수란, 인자가 하나인 함수) f에 대해 항상 성립하는 성질입니다:

```js
// identity
(compose(id, f) === compose(f, id)) === f;
// true
```

아, 이건 마치 숫자의 항등원 성질과 똑같습니다! 만약 바로 이해되지 않는다면, 잠시 시간을 갖고 곱씹어 보세요. 그 무의미함을 이해해 보시고요. 곧 `id`가 여러 곳에서 사용되는 모습을 보게 될 겁니다. 지금은 주어진 값을 대신하는 함수라는 점만 알아두시면 충분합니다. 이것은 포인트프리 코드를 작성할 때 매우 유용합니다.

이렇게 해서, 타입과 함수로 이루어진 범주(category)를 살펴보았습니다.
만약 처음 접하셨다면 범주가 무엇인지, 왜 유용한지 아직 다소 모호할 수도 있습니다. 이 책을 진행하면서 이 지식을 차근차근 쌓아갈 예정입니다.
지금 이 장, 이 부분에서 최소한 이해할 수 있는 점은, 범주론이 함수 조합에 대한 중요한 지혜—즉, 결합법칙과 항등원의 성질—을 제공한다는 것입니다.

“그럼 다른 범주들은 뭐가 있을까요?” 라고 묻는다면, 예를 들어 방향 그래프(directed graph)에서 노드가 객체가 되고, 간선이 사상이 되며, 조합은 경로 연결이 되는 범주를 정의할 수 있습니다. 또는 숫자를 객체로 하고 `>=`를 사상으로 하는 범주(사실 부분 또는 전순서 관계도 범주가 될 수 있습니다)를 정의할 수도 있습니다. 범주는 매우 다양하지만, 이 책에서는 위에서 다룬 범주만 집중해서 다룰 예정입니다.

우리는 이제 충분히 개념의 표면을 훑었으니, 다음으로 넘어가겠습니다.

## 요약

함수 조합은 마치 일련의 파이프처럼 우리 함수들을 연결해 줍니다. 데이터는 반드시 이 파이프를 따라 흐르게 됩니다 — 순수 함수는 결국 입력을 받아 출력을 내놓기 때문에, 이 흐름을 끊는다면 출력이 무시되어 소프트웨어가 무용지물이 될 것입니다.

우리는 함수 조합을 모든 설계 원칙 위에 두고 중요하게 생각합니다. 그 이유는 함수 조합이 애플리케이션을 단순하고 합리적으로 유지하는 데 큰 역할을 하기 때문입니다. 범주론은 애플리케이션 아키텍처 설계, 부작용 모델링, 그리고 정확성 보장에 중요한 역할을 하게 될 것입니다.

이제 이 이론들을 실제로 적용해 보는 것이 도움이 될 시점입니다.
간단한 예제 애플리케이션을 만들어 보겠습니다.

[Chapter 06: Example Application](ch06.md)

## 연습문제

이후의 각 연습 문제에서는 다음과 같은 형태의 자동차(Car) 객체를 다루겠습니다:

```js
{
  name: 'Aston Martin One-77',
  horsepower: 750,
  dollar_value: 1850000,
  in_stock: true,
}
```

{% exercise %}  
`compose()`를 사용하여 아래의 함수를 다시 작성하세요.

{% initial src="./exercises/ch05/exercise_a.js#L12;" %}

```js
const isLastInStock = (cars) => {
  const lastCar = last(cars);
  return prop("in_stock", lastCar);
};
```

{% solution src="./exercises/ch05/solution_a.js" %}  
{% validation src="./exercises/ch05/validation_a.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

다음 함수를 고려해 주세요:

```js
const average = (xs) => reduce(add, 0, xs) / xs.length;
```

{% exercise %}  
헬퍼 함수 `average를` 사용하여 `averageDollarValue`를 함수 조합으로 리팩터링하세요.

{% initial src="./exercises/ch05/exercise_b.js#L7;" %}

```js
const averageDollarValue = (cars) => {
  const dollarValues = map((c) => c.dollar_value, cars);
  return average(dollarValues);
};
```

{% solution src="./exercises/ch05/solution_b.js" %}  
{% validation src="./exercises/ch05/validation_b.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

{% exercise %}  
`fastestCar` 함수를 `compose()`와 다른 함수를 사용해 포인트프리 스타일로 리팩터링해 보세요.
힌트로, `append` 함수가 도움이 될 수 있습니다.

{% initial src="./exercises/ch05/exercise_c.js#L4;" %}

```js
const fastestCar = (cars) => {
  const sorted = sortBy((car) => car.horsepower, cars);
  const fastest = last(sorted);
  return concat(fastest.name, " is the fastest");
};
```

{% solution src="./exercises/ch05/solution_c.js" %}  
{% validation src="./exercises/ch05/validation_c.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

[lodash-website]: https://lodash.com/
[underscore-website]: https://underscorejs.org/
[ramda-website]: https://ramdajs.com/
[refactoring-book]: https://martinfowler.com/books/refactoring.html
[extract-function-refactor]: https://refactoring.com/catalog/extractFunction.html
