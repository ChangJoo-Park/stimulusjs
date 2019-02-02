---
permalink: /handbook/hello-stimulus
---

# 안녕하세요, Stimulus

<!-- The best way to learn how Stimulus works is to build a simple controller. This chapter will show you how. -->

Stimulus를 배우려면 가장 단순한 컨트롤러를 만드는 것이 가장 좋습니다. 이 장에서는 어떻게 만드는지 알아봅니다.

## 사전 준비

<!-- To follow along, you'll need a running copy of the [`stimulus-starter`](https://github.com/stimulusjs/stimulus-starter) project, which is a preconfigured blank slate for exploring Stimulus.  -->

따라하려면, [`stimulus-starter`](https://github.com/stimulusjs/stimulus-starter) 프로젝트를 복사해야합니다. 이 프로젝트에는 Stimulus를 알아보기위한 기본 설정들이 있습니다.


<!-- We recommend [remixing `stimulus-starter` on Glitch](https://glitch.com/edit/#!/import/github/stimulusjs/stimulus-starter) so you can work entirely in your browser without installing anything: -->

우리는 [remixing `stimulus-starter` on Glitch](https://glitch.com/edit/#!/import/github/stimulusjs/stimulus-starter) 를 이용하는 것을 추천합니다. 아무것도 설치하지 않아도 브라우저에서 실행해볼 수 있습니다.


[![Remix on Glitch](https://cdn.glitch.com/2703baf2-b643-4da7-ab91-7ee2a2d00b5b%2Fremix-button.svg)](https://glitch.com/edit/#!/import/github/stimulusjs/stimulus-starter)

<!-- Or, if you'd prefer to work from the comfort of your own text editor, you'll need to clone and set up `stimulus-starter`: -->

사용하는데 익숙한 텍스트 편집기를 이용하려면 간단히 `stimulus-starter`를 클론하세요.

```
$ git clone https://github.com/stimulusjs/stimulus-starter.git
$ cd stimulus-starter
$ yarn install
$ yarn start
```

<!-- Then visit http://localhost:9000/ in your browser. -->

이제 브라우저에서 http://localhost:9000/ 으로 접속해보세요.

<!-- (Note that the `stimulus-starter` project uses the [Yarn package manager](https://yarnpkg.com/) for dependency management, so make sure you have that installed first.) -->

(`stimulus-starter` 프로젝트는 [Yarn package manager](https://yarnpkg.com/) 를 이용해 의존성을 관리합니다. 프로젝트를 진행하기 전에 미리 설치해두세요.)

## 모든 시작은 HTML로부터

<!-- Let's begin with a simple exercise: a text field with a button. When you click the button, we'll display the value of the text field in the console. -->

버튼을 가진 텍스트 필드처럼 쉬운 것부터 시작해봅시다. 버튼을 누르면 텍스트필드에 있는 내용을 콘솔에 출력합니다.

<!-- Every Stimulus project starts with HTML, and this project is no exception. Open `public/index.html` and add the following markup just after the opening `<body>` tag: -->

모든 Stimulus 프로젝트는 HTML에서 시작합니다. 그리고 이 프로젝트는 역시나 예외가 아닙니다. `public/index.html` 을 열고 아래의 마크업을 `<body>` 아래에 적으세요.

```html
<div>
  <input type="text">
  <button>Greet</button>
</div>
```

<!-- Reload the page in your browser and you should see the text field and button. -->

브라우저에서 페이지를 새로고침하면 텍스트필드와 버튼이 있을겁니다.

## 컨트롤러는 HTML에 생명을 불어넣습니다.

<!-- At its core, Stimulus' purpose is to automatically connect DOM elements to JavaScript objects. Those objects are called _controllers_. -->

Stimulus의 핵심 목적은 DOM 엘리먼트를 JavaScript 객체에 자동으로 연결하는 것입니다. 연결된 객체는 _컨트롤러_ 라고 합니다.

<!-- Let's create our first controller by extending the framework's built-in `Controller` class. Create a new file named `hello_controller.js` in the `src/controllers/` folder. Then place the following code inside: -->

프레임워크에 내장된 `Controller` 를 확장해 첫 컨트롤러 클래스를 만들어봅니다. 새 컨트롤러 파일은 `src/controllers` 폴더에 위치하고, 이름은 `hello_controller.js` 입니다. 전체 코드는 아래와 같습니다.

```js
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
}
```

## 식별자는 DOM과 컨트롤러를 연결합니다.

<!-- Next, we need to tell Stimulus how this controller should be connected to our HTML. We do this by placing an _identifier_ in the `data-controller` attribute on our `<div>`: -->

다음으로, Stimulus 가 어떻게 컨트롤러와 HTML에 연결되는지 알아봅니다. `<div>` 태그에 `data-controller` 속성을 추가한 다음 _식별자_ 를 지정합니다.

```html
<div data-controller="hello">
  <input type="text">
  <button>Greet</button>
</div>
```

<!-- Identifiers serve as the link between elements and controllers. In this case, the identifier `hello` tells Stimulus to create an instance of the controller class in `hello_controller.js`. You can learn more about how automatic controller loading works in the [Installation Guide]({% link docs/handbook/06_installing_stimulus.md %}). -->

식별자는 엘리먼트와 컨트롤러의 연결고리입니다. 위 경우, 식별자 `hello` 는 Stimulus 에게 `hello_controller.js`에 있는 컨트롤러 클래스의 인스턴스를 만들 수 있도록 알려줍니다. 어떻게 컨트롤러가 자동으로 불러오는지는 [설치 가이드]({% link docs/handbook/06_installing_stimulus.md %})에서 다룹니다.

## 뭐가 일어난거죠?

<!-- Reload the page in your browser and you'll see that nothing has changed. How do we know whether our controller is working or not? -->

브라우저를 새로고침하면 아무것도 바뀐게 없습니다. 컨트롤러가 작동하는 것인지 아닌지 어떻게 알 수 있을까요?

<!-- One way is to put a log statement in the `connect()` method, which Stimulus calls each time a controller is connected to the document. -->

한가지 방법은 `connect()` 메소드에 로그를 심는 것 입니다. Stimulus는 컨트롤러가 각각의 엘리먼트에 연결될 때 호출합니다.

<!-- Implement the `connect()` method in `hello_controller.js` as follows: -->

`hello_controller.js`안에 `connect()` 메소드를 구현합니다.

```js
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```

<!-- Reload the page again and open the developer console. You should see `Hello, Stimulus!` followed by a representation of our `<div>`. -->

페이지를 새로고침하고 개발자 도구를 열어보세요. `Hello, Stimulus!`를 볼 수 있습니다. 우리가 만든 `<div>` 가 화면에 그려진 이후의 상황입니다.

## 액션은 DOM 이벤트에 반응합니다.

<!-- Now let's see how to change the code so our log message appears when we click the "Greet" button instead. -->

이제 "Greet" 버튼을 누르면 로그를 출력하도록 바꾸어봅니다.

<!-- Start by renaming `connect()` to `greet()`: -->

`connect()`를 `greet()`로 변경합니다.

```js
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  greet() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```

<!-- We want to call the `greet()` method when the button's `click` event is triggered. In Stimulus, controller methods which handle events are called _action methods_. -->

버튼의 `click` 이벤트가 호출되면 `greet()` 메소드를 실행되어야합니다. Stimulus에서는 이벤트를 처리하는 컨트롤러 메소드를 _액션 메소드_ 라고 부릅니다.

<!-- To connect our action method to the button's `click` event, open `public/index.html` and add a magic `data-action` attribute to the button: -->

액션 메소드를 버튼 `click` 이벤트와 연결하려면 `public/index.html` 을 열어 마법의 `data-action` 속성을 버튼에 추가하세요.

```html
<div data-controller="hello">
  <input type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```

> ### 액션 디스크립터에 대하여
>
> `data-action` 속성의 값인 `click->hello#greet` 는 _액션 디스크립터_ 라고 부릅니다.  디스크립터의 각 부분은 이렇게 부릅니다.
> * `click` 은 이벤트 이름입니다.
> * `hello` 은 컨트롤러 식별자입니다.
> * `greet` 는 실행될 메소드 이름입니다.

<!-- Load the page in your browser and open the developer console. You should see the log message appear when you click the "Greet" button. -->

브라우저를 새로고침하고 개발자 도구를 열어놓고, "Greet" 버튼을 클릭하면 로그를 보실 수 있습니다.

## 타겟 엘리먼트를 컨트롤러 속성에 매핑하기

<!-- We'll finish the exercise by changing our action to say hello to whatever name we've typed in the text field. -->
텍스트 필드에 입력한 이름에 대한 인사를 하는 액션을 만들고, 이 장을 마치겠습니다.

<!-- In order to do that, first we need a reference to the input element inside our controller. Then we can read the `value` property to get its contents. -->
이를 위해, 첫째로 폼 인풋 엘리먼트가 컨트롤러를 참조합니다. 그리고 `value` 속성을 이용해 값을 가져옵니다.

<!-- Stimulus lets us mark important elements as _targets_ so we can easily reference them in the controller through corresponding properties. Open `public/index.html` and add a magic `data-target` attribute to the input element: -->
Stimulus는 중요한 엘리먼트를 _targets_ 로 표시하므로 컨트롤러에서 해당 속성을 통해 쉽게 참조할 수 있습니다. `public/index.html`을 열어 마법의 `data-target` 속성을 인풋 엘리먼트에 추가합니다.

```html
<div data-controller="hello">
  <input data-target="hello.name" type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```

> ### 타겟 디스크립터 설명
>
> `data-target` 속성 값인 `hello.name` 를 _타겟 디스크립터_ 라고 합니다. 디스크립터의 각 부분은 이렇게 부릅니다.
> * `hello` 컨트롤러 식별자입니다.
> * `name` 타겟 이름입니다.

<!-- When we add `name` to our controller's list of target definitions, Stimulus automatically creates a `this.nameTarget` property which returns the first matching target element. We can use this property to read the element's `value` and build our greeting string. -->
`name` 을 컨트롤러의 타겟 목록에 추가하고, Stimulus는 자동으로 처음 만나는 엘리먼트에 연결한 `this.nameTarget` 속성을 만듭니다.  이 속성으로 엘리먼트의 `value` 를 이용해 greeting 문자열을 만들 수 있습니다.

<!-- Let's try it out. Open `hello_controller.js` and update it like so: -->
한번 해봅시다. `hello_controller.js` 를 열어 아래의 내용으로 바꿉니다.

```js
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    const element = this.nameTarget
    const name = element.value
    console.log(`Hello, ${name}!`)
  }
}
```

<!-- Then reload the page in your browser and open the developer console. Enter your name in the input field and click the "Greet" button. Hello, world! -->

브라우저에서 새로고침 한 다음 개발자 도구를 열어주세요. 인풋 필드에 이름을 입력하고 "Greet" 버튼을 누르세요. Hello World!

## 간단히 컨트롤러 리팩토링하기

<!-- We've seen that Stimulus controllers are instances of JavaScript classes whose methods can act as event handlers. -->
Stimulus 컨트롤러는 메소드가 이벤트 핸들러 역할을 할 수있는 JavaScript 클래스의 인스턴스입니다.

<!-- That means we have an arsenal of standard refactoring techniques at our disposal. For example, we can clean up our `greet()` method by extracting a `name` getter: -->
이 말은 표준적인 리팩토링 테크닉을 사용할 수 있다는 것과 같습니다. 예를 들어 `greet()` 메소드는 `name` getter로 추출할 수 있습니다.

```js
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    console.log(`Hello, ${this.name}!`)
  }

  get name() {
    return this.nameTarget.value
  }
}
```

## 다음은,

<!-- Congratulations—you've just written your first Stimulus controller! -->

축하합니다! 첫번째 Stimulus 컨트롤러를 만들었습니다.

<!-- We've covered the framework's core concepts: controllers, identifiers, actions, and targets. In the next chapter, we'll see how to put those together to build a real-life controller taken right from Basecamp. -->

이 장은 프레임워크의 핵심 컨셉인 컨트롤러, 식별자, 액션, 타겟을 알아보았습니다. 다음 장에서는 실제 Basecamp에서 가져온 컨트롤러를 만드는 방법을 알아보겠습니다.