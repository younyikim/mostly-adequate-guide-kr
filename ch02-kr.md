# Chapter 02: 일급 함수

## A Quick Review

함수가 "일급(first class)"이라고 말할 때, 이는 함수가 다른 것들과 다를 바 없는 존재라는 의미입니다. 다시 말해, 그저 평범한 자료형일 뿐이라는 뜻이지요. 우리는 함수를 다른 데이터 타입처럼 배열에 저장하거나, 함수의 매개변수로 전달하거나, 변수에 할당하는 등 자유롭게 다룰 수 있습니다.

이건 자바스크립트 101이지만, Github에서 코드를 조금만 검색해 보면 이 개념을 의도적으로 회피하거나, 어쩌면 광범위한 무지가 드러날 수 있으므로 언급할 가치가 있습니다. 허황된 예를 하나 들어볼까요?

```js
const hi = (name) => `Hi ${name}`;
const greeting = (name) => hi(name);
```

여기서, `greeting` 안의 `hi`를 감싼 함수는 완전히 불필요한 중복입니다. 왜일까요? 자바스크립트에서 함수는 _호출 가능(callable)_ 하기 때문입니다. `hi`에 `()`를 붙이면 그 함수가 실행되어 값을 반환하고, 붙이지 않으면 단순히 변수에 저장된 함수 자체를 반환합니다. 확실히 하기 위해 직접 확인해 보세요.

```js
hi; // name => `Hi ${name}`
hi("jonas"); // "Hi jonas"
```

`greeting` 함수는 결국 `hi`와 동일한 인자를 사용해 그대로 호출하고 있기 때문에, 우리는 이렇게 간단히 쓸 수 있습니다 :

```js
const greeting = hi;
greeting("times"); // "Hi times"
```

다시 말해, `hi`는 이미 하나의 인자를 받도록 만들어진 함수인데, 똑같은 인자를 가지고 단순히 `hi`를 호출하는 또 다른 함수를 둘 이유가 있을까요? 말도 안되는 일이죠. 이건 마치 한여름에 두꺼운 패딩을 입고 에어컨을 빵빵하게 튼 다음, 아이스바를 달라고 하는 것과 같습니다. 전혀 말이 되지 않는 행동이에요.

단지 평가를 지연시키기 위해 함수 하나를 또 다른 함수로 감싸는 것은, 불필요하게 장황할 뿐만 아니라, 좋지 않은 습관입니다. (곧 이유를 살펴보겠지만, 유지보수 측면에서 문제가 됩니다.)

이 개념을 제대로 이해하는 것은 앞으로 나아가기 전에 매우 중요하므로, npm 패키지 라이브러리에서 가져온 몇 가지 재미있는 예제를 더 살펴보며 확인해보겠습니다.

```js
// 무지한 방법
const getServerStuff = (callback) => ajaxCall((json) => callback(json));

// 깨달음을 얻은 방법
const getServerStuff = ajaxCall;
```

세상에는 이런 식의 AJAX 코드가 널리고 널렸습니다. 하지만 사실, 아래 두 코드는 완전히 동일하게 동작합니다. 그 이유는 다음과 같습니다 :

```js
// 이 줄은
ajaxCall((json) => callback(json));

// 이렇게 쓰는 것과 동일합니다.
ajaxCall(callback);

// 그래서 getServerStuff를 이렇게 리팩토링할 수 있습니다.
const getServerStuff = (callback) => ajaxCall(callback);

// ...그리고 이건 이것과 동일합니다.
const getServerStuff = ajaxCall; // <-- ()가 없다는걸 꼭 확인하세요
```

자, 여러분 이게 바로 제대로 된 방식입니다. 제가 왜 이렇게까지 집요하게 설명하는지 다시 한 번 짚고 넘어가겠습니다 - 이유를 확실히 이해하기 위해서죠.

```js
const BlogController = {
  index(posts) {
    return Views.index(posts);
  },
  show(post) {
    return Views.show(post);
  },
  create(attrs) {
    return Db.create(attrs);
  },
  update(post, attrs) {
    return Db.update(post, attrs);
  },
  destroy(post) {
    return Db.destroy(post);
  },
};
```

이 말도 안되는 컨트롤러는 99%가 허무맹랑한 내용입니다. 다음과 같이 다시 쓸 수도 있습니다.

```js
const BlogController = {
  index: Views.index,
  show: Views.show,
  create: Db.create,
  update: Db.update,
  destroy: Db.destroy,
};
```

... 아니면 그냥 통째로 없애버리는 것도 방법입니다. 왜냐하면 이 코드는 단순히 Views와 Db를 묶기만 할 뿐, 그 이상 아무런 역할도 하지 않기 때문입니다.

## 왜 일급 함수를 선호해야 할까요?

좋습니다. 이제 일급 함수를 선호해야 하는 이유에 대해 살펴보겠습니다. `getServerStuff` 와 `BlogController` 예제에서 봤듯이, 의미 없는 간접 계층을 추가하는 것은 매우 쉽지만, 그런 계층은 추가적인 가치를 제공하지 않으며, 오히려 유지 관리하고 검색해야 할 중복 코드를 늘릴 뿐입니다.

게다가, 만약 불필요하게 감싸진 함수를 변경해야 한다면, 그 함수를 감싸는 래퍼 함수도 함께 변경해야 하므로 코드 유지 보수가 더 복잡해집니다.

```js
httpGet("/post/2", (json) => renderPost(json));
```

만약 `httpGet`이 `err`을 보낼 수 있도록 변경된다면, 우리는 "glue" 함수를 수정해야 할 필요가 있습니다.

```js
// 애플리케이션에서 모든 httpGet 호출을 찾아 err을 전달하도록 수정하세요
httpGet("/post/2", (json, err) => renderPost(json, err));
```

하지만 일급 함수로 작성했다면, 변경해야 할 부분이 훨씬 적을 것입니다 :

```js
// renderPost는 httpGet 내에서 원하는 만큼의 인자들과 함께 호출됩니다
httpGet("/post/2", renderPost);
```

불필요한 함수 제거 외에도, 우리는 인자에 이름을 붙이고 참조해야 합니다. 그런데 이름이 문제일 수 있습니다. 잘못된 이름이 있을 가능성도 있죠. 특히 코드베이스가 커지고 요구사항이 변할 때, 이런 문제는 더욱 두드러집니다.

같은 개념에 대해 여러 이름을 사용하는 것은 프로젝트에서 혼란을 일으키는 흔한 원인입니다. 또한 일반적인 코드라는 문제도 존재합니다. 예를 들어, 아래 두 함수는 완전히 동일한 동작을 하지만, 하나는 훨씬 더 일반적이고 재사용 가능한 느낌을 줍니다.

```js
// 오직 현재 블로그에서만 사용될 수 있는 함수
const validArticles = articles =>
  articles.filter(article => article !== null && article !== undefined),

// 미래의 프로젝트에서도 사용할 수 있는 함수
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

특정한 이름을 사용함으로써, 우리는 특정 데이터(이 경우 `articles`)에 묶인 것처럼 보입니다. 이런 일이 꽤 자주 발생하며, 많은 재발명의 원인이 됩니다.

또한, 객체 지향 코드에서처럼 `this`가 우리의 목을 치는 일이 생길 수 있다는 점을 반드시 언급해야 합니다. 만약 기저 함수가 `this`를 사용하고, 우리가 그것을 일급 함수로 다룬다면, 우리는 누수되는 추상화(leaky abstraction)의 피해를 입게 됩니다.

```js
const fs = require("fs");

// scary
fs.readFile("freaky_friday.txt", Db.save);

// less so
fs.readFile("freaky_friday.txt", Db.save.bind(Db));
```

자기 자신에게 바인딩된 `Db`는 그 프로토타입의 쓰레기 코드에 접근할 수 있습니다. 저는 `this`를 더러운 기저귀처럼 피합니다. 함수형 코드에서는 사실 `this`를 사용할 필요가 없습니다. 그러나 다른 라이브러리와 인터페이스를 맞춰야 할 때는 어쩔 수 없이 이 미친 세상에 순응해야 할 때도 있습니다.

일부 사람들은 `this`가 속도 최적화에 필요하다고 주장할 수 있습니다. 만약 미세 최적화(micro-optimization)에 집착하는 타입이라면, 이 책을 덮으세요. 만약 돈을 돌려받지 못한다면, 좀 더 까다로운 것으로 교환해 드릴 수도 있을겁니다.

그럼, 이제 다음 단계로 넘어갈 준비가 됐습니다.

[Chapter 03: 순수 함수로 얻는 순수한 행복](ch03-kr.md)
