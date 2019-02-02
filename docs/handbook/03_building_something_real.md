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

> ### What's With That `static targets` Line?
>
> When Stimulus loads your controller class, it looks for target name strings in a static array called `targets`. For each target name in the array, Stimulus adds three new properties to your controller. Here, our `"source"` target name becomes the following properties:
>
> * `this.sourceTarget` evaluates to the first `source` target in your controller's scope. If there is no `source` target, accessing the property throws an error.
> * `this.sourceTargets` evaluates to an array of all `source` targets in the controller's scope.
> * `this.hasSourceTarget` evaluates to `true` if there is a `source` target or `false` if not.


> ###  `static targets` 은 무엇인가요?
>
> When Stimulus loads your controller class, it looks for target name strings in a static array called `targets`. For each target name in the array, Stimulus adds three new properties to your controller. Here, our `"source"` target name becomes the following properties:
> Stimulus가 컨트롤러 클래스를 불러올 때, static `targets` 배열에서 타겟이 되는 이름을 찾고 Stimulus는 컨트롤러에 세가지 추가 속성을 추가합니다. 여기서 `"source"` 타겟의 새로운 속성은 다음과 같습니다.
>
> * `this.sourceTarget` evaluates to the first `source` target in your controller's scope. If there is no `source` target, accessing the property throws an error.
> * `this.sourceTargets` evaluates to an array of all `source` targets in the controller's scope.
> * `this.hasSourceTarget` evaluates to `true` if there is a `source` target or `false` if not.

## 액션과 연결하기

Now we're ready to hook up the Copy button.

We want a click on the button to invoke the `copy()` method in our controller, so we'll add `data-action="clipboard#copy"`:

```html
  <button data-action="clipboard#copy">Copy to Clipboard</button>
```

> ### Common Events Have a Shorthand Action Notation
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

Finally, in our `copy()` method, we can select the input field's contents and call the clipboard API:

```js
  copy() {
    this.sourceTarget.select()
    document.execCommand("copy")
  }
```

Load the page in your browser and click the Copy button. Then switch back to your text editor and paste. You should see the PIN `1234`.

## 유연한 사용자 인터페이스 설계하기

Although the clipboard API is [well-supported in current browsers](https://caniuse.com/#feat=clipboard), we might still expect to have a small number of people with older browsers using our application.

We should also expect people to have problems accessing our application from time to time. For example, intermittent network connectivity or CDN availability could prevent some or all of our JavaScript from loading.

It's tempting to write off support for older browsers as not worth the effort, or to dismiss network issues as temporary glitches that resolve themselves after a refresh. But often it's trivially easy to build features in a way that's gracefully resilient to these types of problems.

This resilient approach, commonly known as _progressive enhancement_, is the practice of delivering web interfaces such that the basic functionality is implemented in HTML and CSS, and tiered upgrades to that base experience are layered on top with CSS and JavaScript, progressively, when their underlying technologies are supported by the browser.

## 점진적으로 PIN 필드 개선하기

Let's look at how we can progressively enhance our PIN field so that the Copy button is invisible unless it's supported by the browser. That way we can avoid showing someone a button that doesn't work.

We'll start by hiding the Copy button in CSS. Then we'll _feature-test_ support for the Clipboard API in our Stimulus controller. If the API is supported, we'll add a class name to the controller element to reveal the button.

Start by adding `class="clipboard-button"` to the button element:

```html
  <button data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</button>
```

Then add the following styles to `public/main.css`:

```css
.clipboard-button {
  display: none;
}

.clipboard--supported .clipboard-button {
  display: initial;
}
```

Now implement a `connect()` method in the controller which adds a class name to the controller's element when the API is supported:

```js
  connect() {
    if (document.queryCommandSupported("copy")) {
      this.element.classList.add("clipboard--supported")
    }
  }
```

If you wish, disable JavaScript in your browser, reload the page, and notice the Copy button is no longer visible.

We have progressively enhanced the PIN field: its Copy button's baseline state is hidden, becoming visible only when our JavaScript detects support for the clipboard API.

## Stimulus 컨트롤러는 재활용하기

So far we've seen what happens when there's one instance of a controller on the page at a time.

It's not unusual to have multiple instances of a controller on the page simultaneously. For example, we might want to display a list of PINs, each with its own Copy button.

Our controller is reusable: any time we want to provide a way to copy a bit of text to the clipboard, all we need is markup on the page with the right annotations.

Let's go ahead and add another PIN to the page. Copy and paste the `<div>` so there are two identical PIN fields, then change the `value` attribute of the second:

```html
<div data-controller="clipboard">
  PIN: <input data-target="clipboard.source" type="text" value="3737" readonly>
  <button data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</button>
</div>
```

Reload the page and confirm that both buttons work.

## 액션과 타겟은 어떤 종류의 엘리먼트라도 사용가능합니다.

Now let's add one more PIN field. This time we'll use a Copy _link_ instead of a button:

```html
<div data-controller="clipboard">
  PIN: <input data-target="clipboard.source" type="text" value="3737" readonly>
  <a href="#" data-action="clipboard#copy" class="clipboard-button">Copy to Clipboard</a>
</div>
```

Stimulus lets us use any kind of element we want as long as it has an appropriate `data-action` attribute.

Note that in this case, clicking the link will also cause the browser to follow the link's `href`. We can cancel this default behavior by calling `event.preventDefault()` in the action:

```js
  copy(event) {
    event.preventDefault()
    this.sourceTarget.select()
    document.execCommand("copy")
  }
```

Similarly, our `source` target need not be an `<input type="text">`. The controller only expects it to have a `value` property and a `select()` method. That means we can use a `<textarea>` instead:

```html
  PIN: <textarea data-target="clipboard.source" readonly>3737</textarea>
```

## 다음은,

In this chapter we looked at a real-life example of wrapping a browser API in a Stimulus controller. We gently modified our controller to be resilient against older browsers and degraded network conditions. We saw how multiple instances of the controller can appear on the page at once. Finally, we explored how actions and targets keep your HTML and JavaScript loosely coupled.

Next, we'll learn about how Stimulus controllers manage state.
