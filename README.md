[![cover](images/cover.png)](SUMMARY.md)

## 이 책에 관하여

이 책은 함수형 패러다임 전반에 대한 책입니다. 여기선 세계에서 가장 널리 사용되는 함수형 프로그래밍 언어인 자바스크립트를 사용할 것입니다. 어떤 사람들은 이것이 현 시대, 특히 명령형 프로그래밍이 주류를 이루는 문화에 어긋나기 때문에 좋지 않은 선택이라고 생각할 수도 있습니다. 하지만 저는 여러 가지 이유로 이것이 함수형 프로그래밍을 배우는 가장 좋은 방법이라고 믿습니다.

- **여러분은 아마 매일 업무에서 JavaScript를 사용하고 있을 것입니다.**

  이는 여러분이 밤이나 주말에 생소한 함수형 언어로 개인 프로젝트를 진행하는 대신, 실제 업무에서 매일 습득한 지식을 적용하고 연습할 수 있게 해줍니다.

- **프로그램 작성을 시작하기 위해 모든 것을 처음부터 배울 필요는 없습니다.**

  순수 함수형 언어에서는 모나드를 사용하지 않고는 변수를 로깅하거나 DOM 노드를 읽을 수 없습니다. 하지만 여기서는 학습 과정에서 코드베이스를 점차 정제해 나가면서 약간의 '반칙'을 쓸 수 있습니다. 자바스크립트는 혼합 패러다임 언어이기 때문에, 지식에 공백이 있는 동안에도 기존에 익숙한 방식으로 돌아갈 수 있어 시작하기 훨씬 수월합니다.

- **자바스크립트는 최상급의 함수형 코드를 작성할 수 있는 완전한 능력을 갖추고 있습니다.**

  Scala나 Haskell 같은 언어를 흉내내기에 충분한 기능들이 있으며, 작은 라이브러리 몇 개만 있으면 됩니다. 현재는 객체지향 프로그래밍이 업계 표준처럼 자리잡고 있지만, 자바스크립트에서는 그 방식이 어색하게 느껴지는 경우가 많습니다. 마치 고속도로 옆에서 캠핑을 하거나 고무 장화를 신고 탭댄스를 추는 것과도 같습니다. `this`가 예기치 않게 바뀌는 것을 막기 위해 `bind`로 도배하고, `new` 키워드를 깜빡했을 때를 위한 여러 우회 방법들이 필요하며, 비공개 멤버는 클로저를 통해서만 사용할 수 있습니다. 많은 개발자들에게 함수형 프로그래밍은 오히려 더 자연스럽게 느껴지기도 합니다.

그럼에도 불구하고, 타입이 있는 함수형 언어들이야말로 이 책에서 소개하는 스타일로 코딩하기에 단연코 가장 좋은 환경입니다. 자바스크립트는 우리가 함수형 패러다임을 배우기 위한 수단일 뿐이며, 이를 실제 어디에 적용할지는 여러분의 선택에 달려 있습니다. 다행히도 함수형 프로그래밍의 인터페이스는 수학적 개념을 기반으로 하고 있기 때문에 어디에서든 통용됩니다. Swiftz, Scalaz, Haskell, PureScript 같은 수학적으로 성향이 강한 환경에서도 익숙하게 느껴질 것입니다.

## 온라인으로 읽어보세요

보다 나은 독서 경험을 위해, [Gitbook을 통해 온라인으로 읽어보세요](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/).

- 빠르게 접근 가능한 side bar
- 브라우저 내 연습
- 심층적인 예시

## 코드를 가지고 놀아보세요

제가 다른 이야기를 하는 동안 지루해지지 않고 효율적으로 학습하려면 이 책에 소개된 개념들을 직접 경험해 보세요. 처음에는 이해하기 어려울 수 있지만, 직접 경험해 보면 더 잘 이해할 수 있습니다. 이 책에 나오는 모든 함수와 대수적 자료 구조는 부록에 정리되어 있습니다. 해당 코드는 npm 모듈로도 제공됩니다.

```bash
$ npm i @mostly-adequate/support
```

또는, 각 장의 연습문제는 실행 가능하며 여러분의 에디터에서 직접 풀 수 있습니다! 예를 들어, `exercises/ch04` 폴더 안의 `exercise_*.js` 파일을 완성한 후 다음 명령어를 실행해 보세요 :

```bash
$ npm run ch04
```

## 다운로드하세요

사전 생성된 **PDF**와 **EPUB** 파일은 [최신 릴리스의 빌드 아티팩트](https://github.com/MostlyAdequate/mostly-adequate-guide/releases/latest)에서 확인하실 수 있습니다.

## 직접 해보세요

> ⚠️ 이 프로젝트 설정은 다소 오래되어 로컬에서 빌드할 때 여러 문제가 발생할 수 있습니다. 가능하면 Node v10.22.1과 최신 버전의 Calibre를 사용하는 것이 좋습니다.

### Node.js 버전에 대하여

권장 Node.js 버전(v10.22.1)이 다소 오래되어 시스템에 설치되어 있지 않을 가능성이 높습니다. [nvm](https://github.com/nvm-sh/nvm)을 사용하여 여러 버전의 Node.js를 시스템에 설치할 수 있습니다. 해당 프로젝트를 참조하여 설치하면 다음과 같은 작업을 수행할 수 있습니다.

- 필요한 노드 버전을 설치하세요.

```
nvm install 10.22.1
nvm install 20.2.0
```

- 그러면 노드 버전 간에 전환할 수 있습니다.

```
nvm use 10.22.1
node -v // will show v10.22.1
nvm use 20.2.0
node -v // will show v20.2.0
```

이 프로젝트에는 .nvmrc 파일이 있기 때문에, Node.js 버전을 따로 지정하지 않아도 `nvm install`과 `nvm use` 명령어를 사용할 수 있습니다.

```
// being anywhere inside this project
nvm install
node -v // will show v10.22.1
```

### 명령의 완벽한 시퀀스

시스템에 nvm이 설치되어있다는 전제하에, PDF 및 EPUB 파일을 직접 생성하기 위한 전체 명령어 순서는 다음과 같습니다.

```
git clone https://github.com/MostlyAdequate/mostly-adequate-guide.git
cd mostly-adequate-guide/
nvm install
npm install
npm run setup
npm run generate-pdf
npm run generate-epub
```

> Note! 전자책 버전을 생성하려면 `ebook-convert`을 설치해야 합니다. [설치 가이드](https://gitbookio.gitbooks.io/documentation/content/build/ebookconvert.html).

# 목차

See [SUMMARY.md](SUMMARY.md)

### Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

### Translations

See [TRANSLATIONS.md](TRANSLATIONS.md)

### FAQ

See [FAQ.md](FAQ.md)

# 미래를 위한 계획

- **Part 1** (chapters 1-7)은 기본 가이드입니다. 아직 초안이기 때문에 오류가 발견될 때마다 업데이트하고 있습니다. 많은 참여 부탁드립니다!
- **Part 2** (chapters 8-13)에서는 functors와 모나드 같은 타입 클래스부터 순회 가능 클래스까지 다룹니다. transformer와 순수 응용 프로그램도 간략하게 다루고 싶습니다.
- **Part 3** (chapters 14+)에서는 실제 프로그래밍과 학문적 불합리성 사이의 미묘한 경계를 탐구하기 시작합니다. f-알제브라(F-algebras), 프리 모나드(Free Monads), 요네다 보조정리(Yoneda), 그리고 기타 범주론적 개념들(categorical constructs)을 살펴볼 것입니다.

---

<p align="center">
  <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
    <img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" />
  </a>
  <br />
  This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
</p>
