---
permalink: /handbook/building-something-real
---

# 실제 작동하는 무언가를 만들어봅시다.

<!-- We've implemented our first controller and learned how Stimulus connects HTML to JavaScript. Now let's take a look at something we can use in a real application by recreating a controller from Basecamp. -->
첫번째 컨트롤러에서 Stimulus가 HTML과 JavaScript를 연결하도록 만들었습니다. 이제, Basecamp에서 가져온 컨트롤러를 이용해 실제 앱에서 사용하는 무언가를 만들어보겠습니다.

## DOM 클립보드 API 감싸기

<!-- Scattered throughout Basecamp's UI are buttons like these: -->
Basecamp의 UI에는 아래와 같은 버튼이 있습니다.

<img src="../../assets/bc3-clipboard-ui.png" width="500" height="122" class="docs__screenshot">

<!-- When you click one of these buttons, Basecamp copies a bit of text, such as a URL or an email address, to your clipboard. -->
이 버튼을 누르면, Basecamp는 URL이나 이메일 주소 등의 텍스트를 클립보트에 복사합니다.

<!-- The web platform has [an API for accessing the system clipboard](https://www.w3.org/TR/clipboard-apis/), but there's no HTML element that does what we need. To implement the Copy button, we must use JavaScript. -->
웹 플랫폼은 [an API for accessing the system clipboard](https://www.w3.org/TR/clipboard-apis/)를 가지고 있습니다. 하지만 HTML 엘리먼트가 필요하지는 않습니다. Copy 버튼을 구현하려면 JavaScript를 이용해야합니다.

## 복사 버튼 만들기

<!-- Let's say we have an app which allows us to grant someone else access by generating a PIN for them. It would be convenient if we could display that generated PIN alongside a button to copy it to the clipboard for easy sharing. -->
PIN을 이용해 다른 사람에게 접근 권한을 주는 앱이 있다고 할 때, 만들어진 PIN과 함께 버튼을 두어 클립보드에 편하게 복사할 수 있도록 합니다.

<!-- Open `public/index.html` and replace the contents of `<body>` with a rough sketch of the button: -->
`public/index.html`을 열어 `<body>` 아래에 다음의 내용을 입력하세요.

```html
<div>
  PIN: <input type="text" value="1234" readonly>
  <button>Copy to Clipboard</button>
</div>
```

## 컨트롤러 만들기

<!-- Next, create `src/controllers/clipboard_controller.js` and add an empty method `copy()`: -->
`src/controllers/clipboard_controller.js` 에 `copy()` 메소드를 만듭니다.

```js
// src/controllers/clipboard_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  copy() {
  }
}
```

<!-- Then add `data-controller="clipboard"` to the outer `<div>`. Any time this attribute appears on an element, Stimulus will connect an instance of our controller: -->
그다음 `data-controller="clipboard"` 을 외부의 `<div>`에 추가합니다. 언제든지 엘리먼트에 이 속성이 나타나면 Stimulus는 컨트롤러 인스턴스와 연결합니다.

```html
<div data-controller="clipboard">
```

## 타겟을 정하는 방법

<!-- We'll need a reference to the text field so we can select its contents before invoking the clipboard API. Add `data-target="clipboard.source"` to the text field: -->
텍스트필드에대한 참조가 필요하므로 클립보드 API가 호출되기 전에 내용을 가져와야합니다. `data-target="clipboard.source"`를 텍스트필드에 추가합니다.

```html
  PIN: <input data-target="clipboard.source" type="text" value="1234" readonly>
```

<!-- Now add a target definition to the controller so we can access the text field element as `this.sourceTarget`: -->
이제 컨트롤러의 타겟에 텍스트 필드의 엘리먼트를 추가합니다.

```js
export default class extends Controller {
  static targets = [ "source" ]

  // ...
}
```

<!-- > ### What's With That `static targets` Line?
>
> When Stimulus loads your controller class, it looks for target name strings in a static array called `targets`. For each target name in the array, Stimulus adds three new properties to your controller. Here, our `"source"` target name becomes the following properties:
>
> * `this.sourceTarget` evaluates to the first `source` target in your controller's scope. If there is no `source` target, accessing the property throws an error.
> * `this.sourceTargets` evaluates to an array of all `source` targets in the controller's scope.
> * `this.hasSourceTarget` evaluates to `true` if there is a `source` target or `false` if not.
 -->

> ###  `static targets` 은 무엇인가요?
> Stimulus가 컨트롤러 클래스를 불러올 때, static `targets` 배열에서 타겟이 되는 이름을 찾고 Stimulus는 컨트롤러에 세가지 추가 속성을 추가합니다. 여기서 `"source"` 타겟의 새로운 속성은 다음과 같습니다.
>
> * `this.sourceTarget` 컨트롤러가 속한 영역에서 처음 만나는 `source` 타겟으로 간주합니다. `source` 가 없으면 에러를 던집니다.
> * `this.sourceTargets` 컨트롤러가 속한 영역에서 모든 `source`를 타겟으로 간주합니다.
> * `this.hasSourceTarget` `true` 이면 처리할 `source` 타겟이 있는 것이고 없다면 `false` 를 리턴합니다.

## 액션과 연결하기

<!-- Now we're ready to hook up the Copy button. -->
이제 Copy 버튼을 처리할 차례입니다.

<!-- We want a click on the button to invoke the `copy()` method in our controller, so we'll add `data-action="clipboard#copy"`: -->
버튼을 클릭하면 컨트롤러 안의 `copy()` 메소드를 실행합니다. 이제 `data-action="clipboard#copy"`를 추가합니다.

```html
  <button data-action="clipboard#copy">Copy to Clipboard</button>
```

<!-- > ### Common Events Have a Shorthand Action Notation
>
> You might have noticed we've omitted `click->` from the action descriptor. That's because Stimulus defines `click` as the default event for actions on `<button>` elements.
>
> Certain other elements have default events, too. Here's the full list:
>
> Element           | Default Event
> ----------------- | -------------
> a                 | click
> button            | click
> form              | submit
> input             | change
> input type=submit | click
> select            | change
> textarea          | change
 -->
> ### 일반적인 이벤트에는 약어가 있습니다.
>
> 액션 디스크립터에서 `click->`를 생략한 것을 알고 있을 것 입니다. Stimulus는 `<button>` 엘리먼트의 기본 동작을 `click` 으로 판단합니다.
>
> 다른 엘리먼트들도 기본 이벤트를 가지고 있습니다. 전체 목록입니다.
>
> Element           | Default Event
> ----------------- | -------------
> a                 | click
> button            | click
> form              | submit
> input             | change
> input type=submit | click
> select            | change
> textarea          | change

<!-- Finally, in our `copy()` method, we can select the input field's contents and call the clipboard API: -->
마지막으로, `copy()` 메소드를 구현할 차례입니다. 입력 필드의 컨텐트를 가져와 클립보드에 복사합니다.

```js
  copy() {
    this.sourceTarget.select()
    document.execCommand("copy")
  }
```

<!-- Load the page in your browser and click the Copy button. Then switch back to your text editor and paste. You should see the PIN `1234`. -->

브라우저에서 새로고침 한 다음 Copy 버튼을 누르세요. 그리고 텍스트 에디터에 붙여넣기를 해보세요. PIN `1234` 를 볼 수 있을 것입니다.

## 유연한 사용자 인터페이스 설계하기

<!-- Although the clipboard API is [well-supported in current browsers](https://caniuse.com/#feat=clipboard), we might still expect to have a small number of people with older browsers using our application. -->
클립보드 API는 [well-supported in current browsers](https://caniuse.com/#feat=clipboard) 하지만, 일부 사용자는 오래된 브라우저를 사용하고 있을 것 입니다.

<!-- We should also expect people to have problems accessing our application from time to time. For example, intermittent network connectivity or CDN availability could prevent some or all of our JavaScript from loading. -->
사람들이 애플리케이션을 사용하는데 어려움이 있을 수 있을 것입니다. 예를 들면, 간헐적인 인터넷 끊김 또는 CDN에 접속하는데 문제가 있어 일부 또는 전체 JavaScript를 불러 올 수 없는 상황을 말합니다.

<!-- It's tempting to write off support for older browsers as not worth the effort, or to dismiss network issues as temporary glitches that resolve themselves after a refresh. But often it's trivially easy to build features in a way that's gracefully resilient to these types of problems. -->

오래된 브라우저 지원을 무시하거나, 새로고침으로 일시적인 네트워크 이슈를 해소할 수 있습니다. 그러나 이러한 문제들을 유연하게 대처하는 방향으로 개발 하는 것이 좋습니다.

<!-- This resilient approach, commonly known as _progressive enhancement_, is the practice of delivering web interfaces such that the basic functionality is implemented in HTML and CSS, and tiered upgrades to that base experience are layered on top with CSS and JavaScript, progressively, when their underlying technologies are supported by the browser. -->

일반적으로 알려진 _점진적인 개선_이라는 탄력적인 방식은 HTML과 CSS로 기본적인 기능을 구현하고, 기본 경험에 대한 추가적인 업그레이드를 CSS와 JavaScript를 이용해 단계별로 개선해야합니다. 

## 점진적으로 PIN 필드 개선하기

<!-- Let's look at how we can progressively enhance our PIN field so that the Copy button is invisible unless it's supported by the browser. That way we can avoid showing someone a button that doesn't work. -->

브라우저가 PIN 을 복사하는 Copy 버튼을 지원하지 않는다면 보여주지 않는 것이 좋습니다. 누군가에게 작동하지 않는 버튼을 보여주는 방식은 피해야합니다.

<!-- We'll start by hiding the Copy button in CSS. Then we'll _feature-test_ support for the Clipboard API in our Stimulus controller. If the API is supported, we'll add a class name to the controller element to reveal the button. -->

Copy 버튼을 CSS를 이용해 숨기는 것으로부터 시작합니다. 그 다음 Stimulus 컨트롤러에서 Clipboard를 지원하는 브라우저인지 _기능테스트_를 합니다. 지원한다면 컨트롤러에서 버튼을 보여주도록 합니다.

<!-- Start by adding `class="clipboard-button"` to the button element: -->
`class="clipboard-button"`을 버튼 엘리먼트에 추가합시다.

```html
  <button data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</button>
```

<!-- Then add the following styles to `public/main.css`: -->
`public/main.css` 에 스타일을 적습니다.

```css
.clipboard-button {
  display: none;
}

.clipboard--supported .clipboard-button {
  display: initial;
}
```

<!-- Now implement a `connect()` method in the controller which adds a class name to the controller's element when the API is supported: -->
이제 컨트롤러의 `connect()` 에서 브라우저가 API를 지원한다면 클래스를 적용합니다.

```js
  connect() {
    if (document.queryCommandSupported("copy")) {
      this.element.classList.add("clipboard--supported")
    }
  }
```

<!-- If you wish, disable JavaScript in your browser, reload the page, and notice the Copy button is no longer visible. -->
JavaScript를 사용하지않고 브라우저를 이용하고 있다면, 페이지를 새로고침하세요 이제 Copy 버튼이 더이상 보이지 않습니다.

<!-- We have progressively enhanced the PIN field: its Copy button's baseline state is hidden, becoming visible only when our JavaScript detects support for the clipboard API. -->

PIN 필드를 점진적으로 개선해보았습니다. Copy 버튼의 기본 상태는 숨겨져 있고, JavaScript가 클립보드 API를 지원하는 경우에만 보여줍니다.

## Stimulus 컨트롤러는 재활용하기

<!-- So far we've seen what happens when there's one instance of a controller on the page at a time. -->
지금까지 한 페이지에 한개의 컨트롤러만 있는 경우를 알아보았습니다.

<!-- It's not unusual to have multiple instances of a controller on the page simultaneously. For example, we might want to display a list of PINs, each with its own Copy button. -->
페이지에 여러개의 컨트롤러 인스턴스가 있는 것은 드문 일이 아닙니다. 그 예로, 여러개의 PIN 목록과 각각 항목마다 Copy 버튼을 가지고 있는 경우를 들 수 있습니다.

<!-- Our controller is reusable: any time we want to provide a way to copy a bit of text to the clipboard, all we need is markup on the page with the right annotations. -->
컨트롤러는 재사용할 수 있습니다. 언제나 텍스트를 클립보드에 넣을 일이 생긴다면, 약간의 어노테이션만 추가하면 됩니다.

<!-- Let's go ahead and add another PIN to the page. Copy and paste the `<div>` so there are two identical PIN fields, then change the `value` attribute of the second: -->
또 다른 PIN을 페이지에 추가해봅니다. `<div>` 전체를 복사 / 붙여넣기 해 두개의 독립적인 PIN 필드를 만들고 `value` 를 바꾸어줍니다.

```html
<div data-controller="clipboard">
  PIN: <input data-target="clipboard.source" type="text" value="3737" readonly>
  <button data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</button>
</div>
```

<!-- Reload the page and confirm that both buttons work. -->
새로고침하고 모든 버튼이 잘 작동하는지 확인해보세요

## 액션과 타겟은 어떤 종류의 엘리먼트라도 사용가능합니다.

<!-- Now let's add one more PIN field. This time we'll use a Copy _link_ instead of a button: -->
이제 PIN 필드를 하나 더 추가합니다. 이제는 Copy 버튼 대신 _link_ 를 이용합니다.

```html
<div data-controller="clipboard">
  PIN: <input data-target="clipboard.source" type="text" value="3737" readonly>
  <a href="#" data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</a>
</div>
```

<!-- Stimulus lets us use any kind of element we want as long as it has an appropriate `data-action` attribute. -->
Stimulus 는 `data-action` 속성을  원하는 어떤 엘리먼트에도 추가할 수 있습니다.

<!-- Note that in this case, clicking the link will also cause the browser to follow the link's `href`. We can cancel this default behavior by calling `event.preventDefault()` in the action: -->
이 경우 링크를 클릭하면 브라우저가 링크의 `href`로 이동하려 합니다. 브라우저의 기본 동작을 막기 위해 `event.preventDefault()`를 액션에 추가합니다.

```js
  copy(event) {
    event.preventDefault()
    this.sourceTarget.select()
    document.execCommand("copy")
  }
```

<!-- Similarly, our `source` target need not be an `<input type="text">`. The controller only expects it to have a `value` property and a `select()` method. That means we can use a `<textarea>` instead: -->

비슷하게 `source` 타겟도 꼭 `<input type="text">` 가 아니어도 됩니다. 컨트롤러는 언제나 `value` 속성만을 바라보고 `select()` 메소드를 실행합니다. 이말은 `<textarea>` 를 사용해도 된다는 말입니다.

```html
  PIN: <textarea data-target="clipboard.source" readonly>3737</textarea>
```

## 다음은,

<!-- In this chapter we looked at a real-life example of wrapping a browser API in a Stimulus controller. We gently modified our controller to be resilient against older browsers and degraded network conditions. We saw how multiple instances of the controller can appear on the page at once. Finally, we explored how actions and targets keep your HTML and JavaScript loosely coupled. -->
이 장에서는 브라우저 API를 Stimulus 컨트롤러로 래핑해보았습니다.  구형 브라우저와 네트워크 끊김을 처리하는 방법을 알아보았으며, 한 페이지에 여러개의 컨트롤러를 사용해보았습니다. 마지막으로 어떻게 HTML과 JavaScript를 느슨하게 결합하는지 알게 되었습니다.

<!-- Next, we'll learn about how Stimulus controllers manage state. -->
다음은 Stimulus 컨트롤러의 상태관리 방법을 알아봅니다.
