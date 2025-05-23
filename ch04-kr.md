# Chapter 04: 커링

## 당신 없이는 살 수 없어요

아버지께서는 예전에 이렇게 말씀하셨습니다. 어떤 것들은 갖기 전까지는 없어도 잘 살 수 있지만, 한 번 갖게 되면 그 이후로는 없이는 살 수 없다고요. 전자렌지가 그런 물건 중 하나고, 스마트폰도 마친가지입니다. 나이가 좀 있으신 분들은 인터넷 없이도 충분히 만족스러운 삶을 살았던 시절을 기억하실 겁니다. 저에게는 커링(currying)이라는 개념도 그런 것 중 하나입니다.

개념은 단순합니다. 함수에 필요한 인자보다 적은 인자를 전달하면, 함수는 나머지 인자를 받은 또 다른 함수를 반환합니다.

모든 인자를 한 번에 전달할 수도 있고, 하나씩 나눠서 전달할 수도 있습니다.

```js
const add = (x) => (y) => x + y;
const increment = add(1);
const addTen = add(10);

increment(2); // 3
addTen(2); // 12
```

여기에서는 `add`라는 함수를 만들었습니다. 이 함수는 하나의 인자를 받아 또 다른 함수를 반환합니다. 이 반환된 함수는 클로저를 통해 처음 전달된 인자를 기억하게 됩니다. 하지만 이렇게 두 인자를 한 번에 전달해 호출하는 방식은 조금 번거롭기 때문에, 이런 함수를 더 쉽게 정의하고 사용할 수 있도록 도와주는 보조 함수인 `curry`를 사용할 수 있습니다.

지금부터는 재미 삼아 몇 가지 커링된 함수를 만들어 보겠습니다. 앞으로는 [부록 A - Essential Function Support](./appendix_a.md)에 정의된 `curry` 함수를 불러와 사용할 것입니다.

```js
const match = curry((what, s) => s.match(what));
const replace = curry((what, replacement, s) => s.replace(what, replacement));
const filter = curry((f, xs) => xs.filter(f));
const map = curry((f, xs) => xs.map(f));
```

제가 따르고 있는 패턴은 단순하지만 중요한 원칙입니다. 우리가 다루게 될 데이터(문자열이나 배열 등)를 마지막 인자로 위치시키도록 전략적으로 배치했습니다. 왜 이렇게 했는지는 실제로 사용해 보면 자연스럽게 이해되실 겁니다.

(`/r/g` 라는 문법은 정규 표현식으로 'r'이라는 글자를 _모두_ 찾는다는 의미입니다. 정규 표현식에 대해 더 알고 싶으시다면 [여기](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)를 참고해 보세요.)

```js
match(/r/g, "hello world"); // [ 'r' ]

const hasLetterR = match(/r/g); // x => x.match(/r/g)
hasLetterR("hello world"); // [ 'r' ]
hasLetterR("just j and s and t etc"); // null

filter(hasLetterR, ["rock and roll", "smooth jazz"]); // ['rock and roll']

const removeStringsWithoutRs = filter(hasLetterR); // xs => xs.filter(x => x.match(/r/g))
removeStringsWithoutRs(["rock and roll", "smooth jazz", "drum circle"]); // ['rock and roll', 'drum circle']

const noVowels = replace(/[aeiou]/gi); // (r,x) => x.replace(/[aeiou]/ig, r)
const censored = noVowels("*"); // x => x.replace(/[aeiou]/ig, '*')
censored("Chocolate Rain"); // 'Ch*c*l*t* R**n'
```

여기서 보여주는 것은, 함수에 하나 또는 두 개의 인자를 미리 "선 입력(pre-load)"해서, 해당 인자들을 기억하는 새로운 함수를 만들어낼 수 있다는 기능입니다.

Mostly Adequate 저장소 `git clone
https://github.com/MostlyAdequate/mostly-adequate-guide.git`를 클론해서 직접 실습해 보시길 권장드립니다. 위에 나온 코드를 복사해서 REPL에서 실행해 보세요. curry 함수는 물론, 부 록에 정의된 거의 모든 기능들이 `support/index.js` 모듈에 포함되어 있습니다.

또는, `npm`에 배포된 버전을 확인해 보셔도 좋습니다 :

```
npm install @mostly-adequate/support
```

## 말장난 그 이상 / 특별한 비법

커링은 다양한 상황에서 유용하게 쓰일 수 있습니다. 기본 함수에 일부 인자만 전달함으로써 새로운 함수를 만들어낼 수 있습니다. 예를 들어, `hasLetterR`, `removeStringsWithoutRs`, `censored`와 같은 함수들이 그러한 방식으로 만들어졌습니다.

또한 개별 요소에 작동하는 어떤 함수든 `map`으로 감싸기만 하면 배열 전체에 작동하는 함수로 바꿀 수 있는 능력도 갖게 됩니다.

```js
const getChildren = (x) => x.childNodes;
const allTheChildren = map(getChildren);
```

함수에 필요한 인자보다 적은 인자를 전달하는 것을 일반적으로 _부분 적용 애플리케이션(partial application)_ 이라고 합니다. 함수를 부분적으로 적용하면 보일러플레이트 코드를 많이 줄일 수 있습니다. 예를 들어, 위의 `allTheChildren` 함수가 loadash의 un-curry된 `map`을 사용하면 어떻게 될지 생각해보세요. (인자의 순서가 다름을 확인해 주세요.)

```js
const allTheChildren = (elements) => map(elements, getChildren);
```

우리는 일반적으로 배열에 작동하는 함수를 따로 정의하지 않습니다. 왜냐하면 `map(getChildren)`처럼 인라인으로 바로 호출할 수 있기 때문입니다. `sort`, `filter`와 같은 다른 고차 함수들도 마찬가지입니다. (*고차 함수*란, 함수를 인자로 받거나 함수를 반환하는 함수를 의미합니다.)

우리가 _순수 함수_ 에 대해 이야기할 때, 순수 함수는 하나의 입력에 대해 하나의 출력을 반환한다고 했습니다. 커링은 바로 이것을 합니다. 각 단일 인자는 나머지 인자를 기대하는 새로운 함수를 반환합니다. 그게 바로, 하나의 입력에 하나의 출력입니다.

출력이 다른 함수일지라도, 그것은 순수 함수로 간주됩니다. 우리는 한 번에 여러 인자를 전달하는 것도 허용하지만, 이것은 안지 편의상 추가적인 `()`를 없애는 것으로 볼 수 있습니다.

## 요약

커링은 매우 유용하며, 저는 매일 커링된 함수들을 사용하면서 많은 즐거움을 느낍니다. 이는 함수형 프로그래밍을 덜 번잡하고 지루하지 않게 만들어주는 유용한 도구입니다.

우리는 몇 가지 인자만 전달함으로써 새로운 유용한 함수를 즉석에서 만들 수 있으며, 보너스로 여러 인자가 있더라도 여전히 수학적 함수 정의를 유지할 수 있습니다.

이제 또 다른 필수 도구인 `compose`를 배워봅시다.

[Chapter 05: 구성(Composing)을 통한 코딩](ch05-kr.md)

## 연습 문제

#### 연습 문제에 대한 안내

이 책을 읽다 보면, 이번 섹션처럼 '연습 문제'가 포함된 부분을 만나실 수 있습니다. 연습 문제는 [gitbook](https://mostly-adequate.gitbooks.io/mostly-adequate-guide)에서 책을 읽고 계시면, 브라우저에서 바로 해결하실 수 있습니다. (추천하는 방법)

Note that, for all exercises of the book, you always have a handful of helper functions
available in the global scope. Hence, anything that is defined in [Appendix A](./appendix_a.md),
[Appendix B](./appendix_b.md) and [Appendix C](./appendix_c.md) is available for you! And, as
if it wasn't enough, some exercises will also define functions specific to the problem
they present; as a matter of fact, consider them available as well.

책의 모든 연습 문제에서는 항상 전역 범위에서 사용할 수 있는 몇 가지 보조 함수들이 제공됩니다. 따라서 [부록 A](./appendix_a.md),
[부록 B](./appendix_b.md), [부록 C](./appendix_c.md)에 정의된 내용들은 모두 사용 가능합니다! 게다가, 일부 연습 문제는 문제를 해결하기 위한 특정 함수도 정의해 주므로, 그것들 역시 사용하실 수 있습니다.

> Hint: 내장된 에디터에서 `Ctrl + Enter`를 눌러 정답을 제출하실 수 있습니다!

#### 로컬 머신에서 연습 문제 실행하기 (선택 사항)

만약 연습 문제를 직접 파일로 작성하여 본인의 에디터에서 해결하고 싶으시면, 다음 단계를 따라주세요 :

- 저장소를 클론합니다 (`git clone git@github.com:MostlyAdequate/mostly-adequate-guide.git`)
- _exercises_ 폴더로 이동합니다 (`cd mostly-adequate-guide/exercises`)
- 추천하는 node 버전 v10.22.1을 사용하고 있는지 확인합니다 (e.g. `nvm install`). 이에 대한 자세한 내용은 책의 [README](./README.md#about-the-nodejs-version)에서 확인할 수 있습니다.
- [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)을 사용하여 필요한 의존성들을 설치합니다 (`npm install`)
- 각 챕터의 폴더 내 \*exercise\_\*\* 로 끝나느 파일들을 수정하여 답안을 작성합니다.
- npm으로 실행해 정답을 확인합니다 (e.g. `npm run ch04`)

유닛 테스트가 여러분의 답안에 대해 실행되며, 실수한 부분이 있으면 힌트를 제공합니다. 참고로, 연습 문제의 정답은 \*solution\_\*\* 로 끝나는 파일에서 확인할 수 있습니다.

#### 연습해봅시다!

{% exercise %}  
함수를 부분 적용하여 모든 인자를 제거하도록 리팩터링하세요

{% initial src="./exercises/ch04/exercise_a.js#L3;" %}

```js
const words = (str) => split(" ", str);
```

{% solution src="./exercises/ch04/solution_a.js" %}  
{% validation src="./exercises/ch04/validation_a.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

{% exercise %}  
Refactor to remove all arguments by partially applying the functions.

{% initial src="./exercises/ch04/exercise_b.js#L3;" %}

```js
const filterQs = (xs) => filter((x) => match(/q/i, x), xs);
```

{% solution src="./exercises/ch04/solution_b.js" %}  
{% validation src="./exercises/ch04/validation_b.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

Considering the following function:

```js
const keepHighest = (x, y) => (x >= y ? x : y);
```

{% exercise %}  
Refactor `max` to not reference any arguments using the helper function `keepHighest`.

{% initial src="./exercises/ch04/exercise_c.js#L7;" %}

```js
const max = (xs) => reduce((acc, x) => (x >= acc ? x : acc), -Infinity, xs);
```

{% solution src="./exercises/ch04/solution_c.js" %}  
{% validation src="./exercises/ch04/validation_c.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}
