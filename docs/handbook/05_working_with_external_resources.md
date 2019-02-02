---
permalink: /handbook/working-with-external-resources
---

# 외부 리소스 이용하기

<!-- In the last chapter we learned how to load and persist a controller's internal state using the Data API -->
이전 장에서 Data API를 이용해 컨트롤러의 내부 상태를 불러오고 유지하는 방법을 알아보았습니다.

<!-- Sometimes our controllers need to track the state of external resources, where by _external_ we mean anything that isn't in the DOM or a part of Stimulus. For example, we may need to issue an HTTP request and respond as the request's state changes. Or we may want to start a timer and then stop it when the controller is no longer connected. In this chapter we'll see how to do both of those things. -->
때로는 컨트롤러가 외부 리소스의 상태를 추적해야하는 경우가 있는데, _외부_란 DOM이나 Stimulus의 일부가 아닌 것을 의미합니다. 예를 들어 HTTP 요청을 수행하고 요청의 상태가 변경 될 때 응답해야 할 수 있습니다. 또는 타이머를 시작한 다음 연결을 해제할 때 타이머를 중지 할 수 있습니다. 이 장에서는 두 가지를 모두 수행하는 방법을 살펴 보겠습니다.

## 비동기로 HTML 불러오기

<!-- Let's learn how to populate parts of a page asynchronously by loading and inserting remote fragments of HTML. We use this technique in Basecamp to keep our initial page loads fast, and to keep our views free of user-specific content so they can be cached more effectively. -->
HTML의 원격 코드 조각을 불러온 후 삽입하는 방식으로 페이지의 일부분을 비동기 적으로 채우는 방법을 알아봅니다. Basecamp에서는 이 방법을 사용하여 초기 페이지로드를 빠르게 유지하고 사용자 별 콘텐츠가 없는 상태에서 더 효과적으로 캐시 할 수 있습니다.

<!-- We'll build a general-purpose content loader controller which populates its element with HTML fetched from the server. Then we'll use it to load a list of unread messages like you'd see in an email inbox. -->
우리는 서버에서 가져온 HTML로 엘리먼트를 채우는 범용 콘텐츠 로더 컨트롤러를 만들 것입니다. 그런 다음 이메일받은 편지함에 표시되는 것과 같이 읽지 않은 메일 목록을 로드하는 데 사용합니다.

<!-- Begin by sketching the inbox in `public/index.html`: -->
받은 편지함을 `public/index.html`에 마크업하는 것으로 시작합니다.

```html
<div data-controller="content-loader"
     data-content-loader-url="/messages.html"></div>
```

<!-- Then create a new `public/messages.html` file with some HTML for our message list: -->
그런 다음 메시지 목록에 사용할 HTML 파일이있는 새 `public/messages.html` 파일을 만듭니다.

```html
<ol>
  <li>New Message: Stimulus Launch Party</li>
  <li>Overdue: Finish Stimulus 1.0</li>
</ol>
```

<!-- (In a real application you'd generate this HTML dynamically on the server, but for demonstration purposes we'll just use a static file.) -->
실제 애플리케이션에서는 이 HTML을 서버에서 동적으로 생성하지만 데모 목적으로 정적 파일 만 사용합니다.

<!-- Now we can implement our controller: -->
이제 컨트롤러를 구현합니다.

```js
// src/controllers/content_loader_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    this.load()
  }

  load() {
    fetch(this.data.get("url"))
      .then(response => response.text())
      .then(html => {
        this.element.innerHTML = html
      })
  }
}
```

<!-- When the controller connects, we kick off a [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) request to the URL specified in the element's `data-content-loader-url` attribute. Then we load the returned HTML by assigning it to our element's `innerHTML` property. -->

컨트롤러가 연결되면 엘리먼트의`data-content-loader-url` 속성에 지정된 URL로 [Fetch] (https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 요청을 합니다. 그런 다음 반환 된 HTML을 요소의 `innerHTML` 속성에 할당하여 로드합니다.

<!-- Open the network tab in your browser's developer console and reload the page. You'll see an initial full page request to `index.html`, followed by our controller's subsequent request to `messages.html`. -->

브라우저 개발자도구의 네트워크 탭을 열어놓은 상태로 새로고침하세요. 처음에 `index.html` 요청이 있는 다음 `messages.html` 을 요청합니다.

## 타이머를 이용한 자동 새로고침

<!-- Let's improve our controller by changing it to periodically refresh the inbox so it's always up-to-date. -->
컨트롤러를 주기적으로 새로고침하여 항상 최신 상태로 유지하도록 변경하여 컨트롤러를 개선합시다.

<!-- We'll use the `data-content-loader-refresh-interval` attribute to specify how often the controller should reload its contents, in milliseconds: -->
`data-content-loader-refresh-interval` 속성을 사용하여 컨트롤러가 내용을 얼마나 자주 다시 불러와야 하는 지를 밀리 초 단위로 지정합니다.

```html
<div data-controller="content-loader"
     data-content-loader-url="/messages.html"
     data-content-loader-refresh-interval="5000"></div>
```

<!-- Now we can update the controller to check for the interval and, if present, start a refresh timer: -->
이제 컨트롤러를 업데이트하여 간격을 확인하고 리프레시 타이머를 시작할 수 있습니다 (있는 경우).

```js
  connect() {
    this.load()

    if (this.data.has("refreshInterval")) {
      this.startRefreshing()
    }
  }

  startRefreshing() {
    setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }
}
```

<!-- Reload the page and observe a new request once every five seconds in the developer console. Then make a change to `public/messages.html` and wait for it to appear in the inbox. -->
개발자 콘솔에서 페이지를 새로고침하고 5초마다 한 번씩 새 요청을 확인하십시오. 그런 다음 `public/messages.html`을 변경하고 받은편지함에 나타날 때까지 기다리십시오.

## 추적하던 자원 해제하기

<!-- We start our timer when the controller connects, but we never stop it. That means if our controller's element were to disappear, the controller would continue to issue HTTP requests in the background. -->
컨트롤러가 연결되면 타이머가 시작되지만 멈추지 않습니다. 즉, 컨트롤러의 요소가 사라져도 컨트롤러는 백그라운드에서 HTTP 요청을 계속 발행합니다.

<!-- We can fix this issue by modifying the `startRefreshing()` method to keep a reference to the timer. Then, in our `disconnect()` method, we can cancel it. -->
타이머에 대한 참조를 유지하기 위해 `startRefreshing()` 메소드를 수정하여이 해결할 수 있습니다. 그런 다음 우리의`disconnect()`메소드에서 우리는 그것을 취소 할 수 있습니다.

```js
  disconnect() {
    this.stopRefreshing()
  }

  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }

  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
}
```

<!-- Now we can be sure a content loader controller will only issue requests when it's connected to the DOM. -->
이제는 컨텐트 로더 컨트롤러가 DOM에 연결될 때만 요청을 보냅니다.

<!-- Let's take a look at our final controller class: -->
최종 컨트롤러 클래스를 살펴 보겠습니다.

```js
// src/controllers/content_loader_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    this.load()

    if (this.data.has("refreshInterval")) {
      this.startRefreshing()
    }
  }

  disconnect() {
    this.stopRefreshing()
  }

  load() {
    fetch(this.data.get("url"))
      .then(response => response.text())
      .then(html => {
        this.element.innerHTML = html
      })
  }

  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }

  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
}
```

## 다음은,

<!-- In this chapter we've seen how to acquire and release external resources using Stimulus lifecycle callbacks. -->
이 장에서는 Stimulus 라이프사이클 콜백을 사용하여 외부 리소스를 처리하는 방법을 살펴 보았습니다.

<!-- Next we'll see how to install and configure Stimulus in your own application. -->
다음으로 애플리케이션에 Stimulus를 설치하고 구성하는 방법을 살펴볼 것입니다.