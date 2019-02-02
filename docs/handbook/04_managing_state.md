---
permalink: /handbook/managing-state
---

# 상태관리

<!-- Most contemporary frameworks encourage you to keep state in JavaScript at all times. They treat the DOM as a write-only rendering target, reconciled by client-side templates consuming JSON from the server. -->
대부분의 최신 프레임워크는 항상 JavaScript를 이용해 상태관리를 하도록 권장합니다. DOM를 쓰기전용 렌더링 타겟으로 취급하고, 서버에서 JSON을 받아 처리하는 템플릿으로 여깁니다.

<!-- Stimulus takes a different approach. A Stimulus application's state lives as attributes in the DOM; controllers themselves are largely stateless. This approach makes it possible to work with HTML from anywhere—the initial document, an Ajax request, a Turbolinks visit, or even another JavaScript library—and have associated controllers spring to life automatically without any explicit initialization step. -->

Stimulus는 다른 접근법을 갑니다. Stimulus 애플리케이션은 DOM 속성이 상태를 가집니다. 컨트롤러 자체는 보통의 경우 상태가 없습니다. 이러한 접근법으로 명시적인 초기화 없이 자동으로 컨트롤러가 작동하여 언제나 -최초 문서, Ajax 요청, Turbolinks 방문 또는 다른 JavaScript 라이브러리 사용 등의 경우- HTML이 동작할 수 있습니다.

## 슬라이드쇼 만들기

<!-- In the last chapter, we learned how a Stimulus controller can maintain simple state in the document by adding a class name to an element. But what do we do when we need to store a value, not just a simple flag? -->
이전 장에서, Stimulus 컨트롤러가 클래스 이름을 추가하는 방식으로 상태를 관리하는 것을 보았습니다.  단순한 플래그가 아닌 값을 가지고 있으려면 어떻게 해야할까요?

<!-- We'll investigate this question by building a slideshow controller which keeps its currently selected slide index in an attribute. -->
속성에서 선택된 슬라이드 인덱스를 가지고 있는 슬라이드쇼 컨트롤러를 만들어 이 질문에 답을 할 것입니다.

<!-- As usual, we'll begin with HTML: -->
어느때와 같이, HTML에서 시작합니다.

```html
<div data-controller="slideshow">
  <button data-action="slideshow#previous">←</button>
  <button data-action="slideshow#next">→</button>

  <div data-target="slideshow.slide" class="slide">🐵</div>
  <div data-target="slideshow.slide" class="slide">🙈</div>
  <div data-target="slideshow.slide" class="slide">🙉</div>
  <div data-target="slideshow.slide" class="slide">🙊</div>
</div>
```

<!-- Each `slide` target represents a single slide in the slideshow. Our controller will be responsible for making sure only one slide is visible at a time. -->
슬라이드쇼에서 각 `slide` 타겟은 하나의 슬라이드를 가지고 있습니다. 컨트롤러는 한번에 한 슬라이드만 보여주어야 합니다.

<!-- 
We can use CSS to hide all slides by default, only showing them when the `slide--current` class is applied: 
-->

모든 슬라이드는 기본값이 숨김입니다. 그리고 `slide-current` 클래스가 있는 슬라이드만 보여지도록 합니다.

```css
.slide {
  display: none;
}

.slide.slide--current {
  display: block;
}
```

<!-- Now let's draft our controller. Create a new file, `src/controllers/slideshow_controller.js`, as follows: -->
컨트롤러의 초안을 만들어 봅시다. 새 파일을 `src/controllers` 에 `slideshow_controller.js` 파일 이름으로 만듭니다.

```js
// src/controllers/slideshow_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "slide" ]

  initialize() {
    this.showSlide(0)
  }

  next() {
    this.showSlide(this.index + 1)
  }

  previous() {
    this.showSlide(this.index - 1)
  }

  showSlide(index) {
    this.index = index
    this.slideTargets.forEach((el, i) => {
      el.classList.toggle("slide--current", index == i)
    })
  }
}
```

<!-- 
Our controller defines a method, `showSlide()`, which loops over each slide target, toggling the `slide--current` class if its index matches. 
-->
컨트롤러에 `showSlide()` 메소드를 정의했습니다. 전체 슬라이드 루프를 돌아 인덱스와 같은 타겟에 `slide-current` 클래스를 추가합니다.

<!-- We initialize the controller by showing the first slide, and the `next()` and `previous()` action methods advance and rewind the current slide. -->

컨트롤러가 초기화되면 첫번쨰 슬라이드를 보여줍니다. 그리고 `next()`, `previous()` 액션이 호출되면 현재 슬라이드를 기준으로 다음 또는 이전 슬라이드를 보여줍니다.

<!-- > ### Lifecycle Callbacks Explained
>
> What does the `initialize()` method do? How is it different from the `connect()` method we've used before?
>
> These are Stimulus _lifecycle callback_ methods, and they're useful for setting up or tearing down associated state when your controller enters or leaves the document.
>
> Method       | Invoked by Stimulus…
> ------------ | --------------------
> initialize() | Once, when the controller is first instantiated
> connect()    | Anytime the controller is connected to the DOM
> disconnect() | Anytime the controller is disconnected from the DOM
 -->

> ### 라이프사이클 콜백 소개
>
> `initialize()` 메소드는 무엇을 할까요? `connect()` 메소드와는 무슨 차이가 있나요?
>
> 이러한 _라이프사이클 콜백_ 메소드는 컨트롤러 있는 페이지에 들어오는 경우의 최초 설정 나가는 경우의 해제에 유용하게 사용할 수 있습니다. 
>
> 메소드       | Stimulus가 호출하는 시점
> ------------ | --------------------
> initialize() | 한번, 컨트롤러가 초기화 되는 경우에 불립니다.
> connect()    | 언제나 DOM에 컨트롤러가 연결될 때 호출됩니다.
> disconnect() | 언제나 DOM과 컨트롤러의 연결이 끊길 때 호출됩니다.

<!-- Reload the page and confirm that the Next button advances to the next slide. -->

페이지를 새로고침하고 다음 버튼을 눌러 다음 슬라이드를 볼 수 있는지 확인하세요.

## DOM에서 초기 상태 읽기

<!-- Notice how our controller tracks its state—the currently selected slide—in the `this.index` property. -->

어떻게 컨트롤러가 `this.index` -여기서는 선택된 슬라이드 입니다.- 속성을 이용해서 상태를 추적하는지 알아보았습니다. 

<!-- Now say we'd like to start one of our slideshows with the second slide visible instead of the first. How can we encode the start index in our markup? -->
첫번째 슬라이드가 아닌 두번째 슬라이드가 맨 처음에 보이려면 어떻게 해야할까요? 마크업에 첫번째 인덱스를 인코딩 하려면 어떻게 할까요?

<!-- One way might be to load the initial index with an HTML `data` attribute. For example, we could add a `data-slideshow-index` attribute to the controller's element: -->
첫번째 방법은 HTML의 `data` 속성을 이용하는 방식입니다. `data-slideshow-index` 속성을 컨트롤러의 엘리먼트에 추가하면 됩니다.

```html
<div data-controller="slideshow" data-slideshow-index="1">
```

`initialize()` 메소드에서 위 속성을 가져옵니다. 그리고 정수로 바꾼 후 `showSlide()` 에 보냅니다.


```js
  initialize() {
    const index = parseInt(this.element.getAttribute("data-slideshow-index"))
    this.showSlide(index)
  }
```

<!-- Working with `data` attributes on controller elements is common enough that Stimulus provides an API for it. Instead of reading the attribute value directly, we can use the more convenient `this.data.get()` method: -->

컨트롤러 엘리먼트의 `data` 속성을 이용하는 것은 Stimulus가 API를 제공할 정도로 일반적인 방법입니다. 속성을 직접 읽는 대신, 더 편리한 `this.data.get()` 메소드를 사용할 수 있습니다.

```js
  initialize() {
    const index = parseInt(this.data.get("index"))
    this.showSlide(index)
  }
```

<!-- > ### The Data API Explained
>
> Each Stimulus controller has a `this.data` object with `has()`, `get()`, and `set()` methods. These methods provide convenient access to `data` attributes on the controller's element, scoped by the controller's identifier.
>
> For example, in our controller above:
> * `this.data.has("index")` returns `true` if the controller's element has a `data-slideshow-index` attribute
> * `this.data.get("index")` returns the string value of the element's `data-slideshow-index` attribute
> * `this.data.set("index", index)` sets the element's `data-slideshow-index` attribute to the string value of `index`
>
> If your attribute name consists of more than one word, reference it as `camelCase` in JavaScript and `attribute-case` in HTML. For example, you can read the `data-slideshow-current-class-name` attribute with `this.data.get("currentClassName")`.
 -->
> ### Data API 소개
>
> 각 Stimulus 컨트롤러는 `has()`, `get()`, `set()` 메소드가 있는 `this.data` 객체를 가지고 있습니다. 이 메소드들은 컨트롤러 엘리먼트의 `data` 속성에 접근하기 편하게 도와줍니다.
>
> 위에 있는 컨트롤러를 예로 들면,
> * `this.data.has("index")`는 `data-slideshow-index`가 있다면 `true` 를 리턴합니다.  
> * `this.data.get("index")`는  `data-slideshow-index` 속성을 리턴합니다. 단, 문자열입니다.
> * `this.data.set("index", index)`는  엘리먼트의 `data-slideshow-index` 에 `index` 를 설정합니다.
>
> 속성 이름이 두 단어 이상이라면 JavaScript에서는 `camelCase`로 HTML에서는 `attribute-case` 를 사용합니다.  HTML의 `data-slideshow-current-class-name` 속성은 JavaScript에서 `this.data.get("currentClassName")`로 가져올 수 있습니다.


<!-- Add the `data-slideshow-index` attribute to your controller's element, then reload the page to confirm the slideshow starts on the specified slide. -->

`data-slideshow-index` 속성을 컨트롤러 엘리먼트에 추가한 다음 지정한 번호의 슬라이드가 처음에 나오는지 확인해보세요.

## DOM의 지속 상태

<!-- We've seen how to bootstrap our slideshow controller's initial slide index by reading it from a `data` attribute. -->
슬라이드쇼 컨트롤러의 초기 슬라이드 인덱스를 `data`에서 가져와 초기화 하는 방법을 보았습니다.

<!-- As we navigate through the slideshow, however, that attribute does not stay in sync with the controller's `index` property. If we were to clone the controller's element in the document, the clone's controller would revert back to its initial state. -->
슬라이드쇼를 움직여보면 속성이 `index` 와 동기화되지 않습니다. 컨트롤러 엘리먼트를 복사하면 컨트롤러 값이 초기화됩니다.

<!-- We can improve our controller by defining a getter and setter for the `index` property which delegates to the Data API: -->
데이터 API가 사용할 `index` 속성을 위한 getter와 setter를 정의함으로써 컨트롤러를 개선할 수 있습니다.

```js
// src/controllers/slideshow_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "slide" ]

  initialize() {
    this.showCurrentSlide()
  }

  next() {
    this.index++
  }

  previous() {
    this.index--
  }

  showCurrentSlide() {
    this.slideTargets.forEach((el, i) => {
      el.classList.toggle("slide--current", this.index == i)
    })
  }

  get index() {
    return parseInt(this.data.get("index"))
  }

  set index(value) {
    this.data.set("index", value)
    this.showCurrentSlide()
  }
}
```

<!-- Here we've renamed `showSlide()` to `showCurrentSlide()` and changed it to read from `this.index`. The `get index()` method returns the controller element's `data-slideshow-index` attribute as an integer. The `set index()` method sets that attribute and then refreshes the current slide. -->
`showSlide()` 메소드를 `showCurrentSlide()` 로 이름을 바꾸었습니다. 그리고 `this.index`를 읽도록 변경했습니다. `get index()` 메소드는 컨트롤러 엘리먼트의 `data-slideshow-index` 를 정수로 가져옵니다. `set index()` 메소드는 속성을 지정하고 슬라이드를 보여주도록 바꿉니다.

<!-- Now our controller's state lives entirely in the DOM. -->
이제 DOM과 컨트롤러의 상태는 완전히 같습니다.

## 다음은,

<!-- In this chapter we've seen how to use the Stimulus Data API to load and persist the current index of a slideshow controller. -->
이 장에서는 Stimulus Data API를 사용하여 슬라이드 쇼 컨트롤러의 현재 인덱스를 불러오고 유지하는 방법을 살펴 보았습니다.

<!-- From a usability perspective, our controller is incomplete. Consider how you might revise the controller to address the following issues: -->
사용성 측면에서 현재 컨트롤러는 불완전합니다. 아래의 문제를 해결하기 위해 컨트롤러를 수정해보세요

<!-- * The Previous button appears to do nothing when you are looking at the first slide. Internally, the `index` value decrements from `0` to `-1`. Could we make the value wrap around to the _last_ slide index instead? (There's a similar problem with the Next button.) -->
* 이전 버튼은 첫번째 슬라이드에서 아무것도 할수 없지만 화면에 보입니다. 내부적으로 `index` 값은 `0` 에서 `-1` 이 됩니다. 이 대신에 _마지막_ 슬라이드 번호로 바꿀 수 있을까요? (이 문제는 다음 버튼에서도 동일하게 발생합니다.)
<!-- * If we forget to specify the `data-slideshow-index` attribute, the `parseInt()` call in our `get index()` method will return `NaN`. Could we fall back to a default value of `0` in this case? -->
* `data-slideshow-index` 를 초기화하는 것을 잊으면, `parseInt()` 했을 때 `get index()` 메소드는 `NaN` 을 리턴합니다. 이 경우 `0` 으로 지정되도록 바꿀 수 있습니까?

<!-- Next we'll look at how to keep track of external resources, such as timers and HTTP requests, in Stimulus controllers. -->

다음은 타이머 및 HTTP 요청과 같은 외부 리소스를 Stimulus 컨트롤러에서 추적하는 방법을 살펴 보겠습니다.