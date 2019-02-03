---
permalink: /handbook/installing
---

# 당신의 애플리케이션에 Stimulus 설치하기
{:.no_toc}

<!-- To install Stimulus in your application, add the [`stimulus` npm package](https://www.npmjs.com/package/stimulus) to your JavaScript bundle. Or, load [`stimulus.umd.js`](https://unpkg.com/stimulus/dist/stimulus.umd.js) in a `<script>` tag. -->

Stimulus를 이미 작업중인 애플리케이션에 추가하기 위해서 [`stimulus` npm package](https://www.npmjs.com/package/stimulus)를 JavaScript 번들에 포함해야합니다. 아니면, [`stimulus.umd.js`](https://unpkg.com/stimulus/dist/stimulus.umd.js)을 `<script>` 태그에 추가해도 됩니다.

* TOC
{:toc}

## 웹팩 사용하기

<!-- Stimulus integrates with the [webpack](https://webpack.js.org/) asset packager to automatically load controller files from a folder in your app. -->

Stimulus를 [webpack](https://webpack.js.org/)에 통합하여 자동으로 앱의 폴더에서 컨트롤러 파일을 자동으로 불러올 수 있습니다.

<!-- Call webpack's [`require.context`](https://webpack.js.org/api/module-methods/#require-context) helper with the path to the folder containing your Stimulus controllers. Then, pass the resulting context to the `Application#load` method using the `definitionsFromContext` helper: -->

webpack의 [`require.context`](https://webpack.js.org/api/module-methods/#require-context) 헬퍼에 Stimulus 컨트롤러가 있는 폴더를 호출하면 됩니다. 그 다음 컨텍스트를`definitionsFromContext` 헬퍼를 사용하여`Application#load` 메소드에 전달하면 됩니다.


```js
// src/application.js
import { Application } from "stimulus"
import { definitionsFromContext } from "stimulus/webpack-helpers"

const application = Application.start()
const context = require.context("./controllers", true, /\.js$/)
application.load(definitionsFromContext(context))
```

### 컨트롤러 파일이름을 식별자에 매핑하기

<!-- Name your controller files `[identifier]_controller.js`, where `identifier` corresponds to each controller's `data-controller` identifier in your HTML. -->
컨트롤러 파일의 이름은 `[identifier]_controller.js` 으로 지정합니다. `identifier`는 HTML 에서 `data-controller` 식별자로 사용합니다. 

<!-- Stimulus conventionally separates multiple words in filenames using underscores. Each underscore in a controller's filename translates to a dash in its identifier. -->

Stimulus는 일반적으로 밑줄을 사용하여 파일 이름에서 여러 단어를 구분합니다. 컨트롤러의 파일 이름에있는 각각의 밑줄은 식별자의 대시로 변환됩니다.

<!-- You may also namespace your controllers using subfolders. Each forward slash in a namespaced controller file's path becomes two dashes in its identifier. -->

또한 하위 폴더를 사용하여 컨트롤러의 네임스페이스를 지정할 수도 있습니다. 네임스페이스가 있는 컨트롤러 파일의 경로에서 각 슬래시는 해당 식별자에서 두 개의 대시를 이용합니다.

<!-- If you prefer, you may use dashes instead of underscores anywhere in a controller's filename. Stimulus treats them identically. -->

원하는 경우 컨트롤러의 파일 이름의 아무 곳에 나 밑줄 대신 대시를 사용할 수 있습니다. Stimulus는 그것들을 동일하게 취급합니다.

컨트롤러 파일 이름 | 식별자는 이렇게 됩니다.
--------------------------------- | -----------------------
clipboard_controller.js           | clipboard
date_picker_controller.js         | date-picker
users/list_item_controller.js     | users\-\-list-item
local-time-controller.js          | local-time

## 다른 빌드 시스템 사용하기

<!-- Stimulus works with other build systems, too, but without support for controller autoloading. Instead, you must explicitly load and register controller files with your application instance: -->
Stimulus는 다른 빌드 시스템에서도 작동하지만 컨트롤러를 자동으로 불러오지는  않습니다. 대신 컨트롤러 파일을 애플리케이션 인스턴스에 명시적으로 로드하고 등록해야합니다.

```js
// src/application.js
import { Application } from "stimulus"

import HelloController from "./controllers/hello_controller"
import ClipboardController from "./controllers/clipboard_controller"

const application = Application.start()
application.register("hello", HelloController)
application.register("clipboard", ClipboardController)
```

## 바벨 사용하기

<!-- If you're using [Babel](https://babeljs.io/) with your build system, you'll need to install the [@babel/plugin-proposal-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties) and add it to your configuration: -->
[Babel](https://babeljs.io/)을 빌드시스템과 함꼐 사용한다면 [@babel/plugin-proposal-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)를 설정에 추가하세요.

```js
// .babelrc
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

## 빌드시스템 없이 사용하기

<!-- If you prefer not to use a build system, you can load Stimulus in a `<script>` tag and it will be globally available through the `window.Stimulus` object. -->
빌드시스템을 사용하고 있지 않다면 `<script>` 태그를 이용해 불러 온 다음  `window.Stimulus`객체를 이용하세요.

<!-- Define targets using `static get targets()` methods instead of `static targets = […]` class properties, which aren't supported natively [yet](https://github.com/tc39/proposal-static-class-features/). -->
아직은 네이티브 [class에서 static 기능](https://github.com/tc39/proposal-static-class-features/)을 지원하지 않기 때문에, 타겟을 정의하는 `static targets = […]`는 `static get targets()` 메소드를 사용해야합니다.  

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <script src="https://unpkg.com/stimulus/dist/stimulus.umd.js"></script>
  <script>
    (() => {
      const application = Stimulus.Application.start()

      application.register("hello", class extends Stimulus.Controller {
        static get targets() {
          return [ "name" ]
        }

        // …
      })
    })()
  </script>
<head>
<body>
  <div data-controller="hello">
    <input data-target="hello.name" type="text">
    …
  </div>
</body>
</html>
```

## 브라우저 지원

<!-- Stimulus supports all evergreen, self-updating desktop and mobile browsers out of the box. -->
Stimulus는 대부분의 데스크탑과 모바일 브라우저 기능을 모두 지원합니다.

<!-- If your application needs to support older browsers like Internet Explorer 11, include the [`@stimulus/polyfills`](https://www.npmjs.com/package/@stimulus/polyfills) package before loading Stimulus. -->
Internet Explorer 11과 같이 오래된 브라우저를 지원하기 위해서는[`@stimulus/polyfills`](https://www.npmjs.com/package/@stimulus/polyfills) 패키지를 포함하세요.

```js
import "@stimulus/polyfills"
import { Application } from "stimulus"

const application = Application.start()
// …
```
