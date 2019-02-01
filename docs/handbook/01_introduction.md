---
permalink: /handbook/introduction
---

# 소개

<!-- Stimulus is a JavaScript framework with _modest_ ambitions. Unlike other frameworks, Stimulus doesn't take over your application's entire front-end. Rather, it's designed to augment your HTML by connecting elements to JavaScript objects automatically. -->

Stimulus는 _겸손한_ 야망을 가진 JavaScript 프레임워크입니다. 여타 다른 프레임워크들과 다르게, Stimulus는 애플리케이션의 프론트엔드의 전체를 뒤덮지 않습니다. 엘리먼트를 JavaScript 객체에 자동으로 연결하여 HTML을 보완하도록 설계되었습니다.

## HTML에 JavaScript 연결하기

<!-- Stimulus works by continuously monitoring the page, waiting for the magic `data-controller` attribute to appear. Like the `class` attribute, you can put more than one value inside it. But instead of applying or removing CSS class names, `data-controller` values connect and disconnect Stimulus _controllers_. -->

Stimulus는 마법의 `data-controller` 속성이 나타날 때 까지 지속적으로 페이지를 감시합니다. `class` 속성처럼 하나 이상의 값을 넣을 수도 있습니다. 하지만 CSS 클래스를 적용하거나 제거하는 것과 달리 `data-controller` 의 값은 Stimululs _컨트롤러_ 안에서 연결(connect)하거나 제거(disconnect)합니다.

<!-- Think of it like this: in the same way that `class` is a bridge connecting HTML to CSS, `data-controller` is a bridge from HTML to JavaScript. -->

이렇게 생각하세요: `class`는 HTML을 CSS에 연결하는 다리이며,`data-controller`는 HTML를 JavaScript에 연결하는 다리입니다.

<!-- On top of this foundation, Stimulus adds the magic `data-action` attribute, which describes how events on the page should trigger controller methods, and the magic `data-target` attribute, which gives you a handle for finding elements in the controller's scope. -->

위의 기본 원리 위에서, Stimulus는 페이지의 이벤트가 컨트롤러 메소드를 실행하는 또 하나의 마법인 `data-action` 을 가지고 있습니다. 그리고 컨트롤러의 엘리먼트를 찾기 위한 방법인 `data-target`도 있습니다.

## 관심사의 분리

<!-- Stimulus' magic attributes let you cleanly separate content from behavior in the same way you already separate content from presentation with CSS. Plus, Stimulus' conventions naturally encourage you to group related code by name. -->

Stimulus의 속성들을 이용하면 CSS가 컨텐츠와 스타일을 분리하는 것처럼 컨텐츠와 동작들을 구분할 수 있습니다. Stimulus의 관례에 따라 관련한 코드를 그룹화하는 것이 좋습니다.

<!-- This arrangement helps you build reusable, trait-like controllers, giving you just enough structure to keep your code from devolving into "JavaScript soup." -->

이는 재활용할 수 있는 컨트롤러를 만들어 "엉망진창인 JavaScript"가 되지 않도록 하는데 충분한 구조를 제공합니다.

## 읽을 수 있는 문서

<!-- When your JavaScript behavior is mapped out in magic attributes, you can _read_ a fragment of HTML and know what's going on. That's a welcome relief when you return to a template six months later and don't recall exactly how things fit together. -->

당신의 JavaScript가 Stimulus의 속성들과 매핑되면 HTML 조각만 읽어도 무슨일이 일어나는지 알 수 있습니다. 6개월이나 지나 잘 기억나지 않더라도 안심할 수 있습니다.

<!-- Readable markup also means that others on your team can easily look at templates—or even the developer console on a production page—to quickly trace behavior or diagnose an issue. -->

읽기 좋은 마크업은 다른 팀원이 템플릿 또는 운영중인 페이지의 개발자 콘솔에서 쉽게 동작을 추적하거나 문제를 진단할 수 있음을 의미합니다.

## 따뜻한 물의 온기를 느껴보세요

Now's a great time to dip your toes in and discover how Stimulus works. Keep reading to learn how to build your first controller.
