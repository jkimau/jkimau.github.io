---
layout: post
category: kor
title: "리액트 라우터 v4 간단 예제"
tags: ["리액트 라우터"]
---

리액트 라우터 v4의 아주 간단한 예제를 통해 내가 생각하는 이전 버전과의 가장 큰 차이점에 관한 글이다.

```javascript
// react-router v3
render((
  <Router>
    <Route path="/" component={App}>
      <IndexRoute component={Home} />
      <Route path="about" component={About} />
      <Route path="contact" component={Contact} />
    </Route>
  </Router>
), document.body)
```

위 예제가 아마도 리액트 라우터 v3를 이용해 표현할 수 있는 가장 간단한 예제가 아닐까 생각 한다. 특별한 설명이 필요 없을 정도로 아주 간단 명료하다. `<Route />`를 단순 나열 할 수도 있고 `<Route />` 안에 `<Route />`를 넣을 수도 있다.

만약 라우터를 이용해 레이아웃을 잡고자 한다면 위 예제처럼 `<Route />` 안에 `<Route />`를 넣고 바깥 `<Route />`에 레이아웃으로 사용 할 컴포넌트를 지정해 주고 내부 `<Route />`에는 컨텐츠에 해당하는 컴포넌트를 지정해 주면 된다.

v4에서 가장 눈에 띄는 차이점은 일단 `<Route />`를 다른 `<Route />` 안에 더이상 넣을 수 없다는 점이고 또 하나는 `<Route />`를 여느 리액트 UI 컴포넌트와 똑같은 방식으로 사용 할 수 있다는 것이다. 즉, `<Route />`를 그냥 하나의 리액트 컴포넌트라 생각 하면 된다.

```javascript
// react-router v4
render((
  <Router>
    <Route exact path="/" component={App} />
    <Route path="/about" component={About} />
    <Route path="/contact" component={Contact} />
  </Router>
), document.body)
```

따라서 v3와 달리 v4에서는 일반 리액트 컴포넌트를 `<Router></Router>` 안에서 사용할 수 있게 된다. 위에서 보았던 v3 레이아웃 예제를 v4로 다시 쓴다면 아래와 비슷해 진다.

```javascript
// react-router v4
render((
  <Router>
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">Aount</Link></li>
        <li><Link to="/contact">Contact</Link></li>
      </ul>
    </div>

    <Route exact path="/" component={Home} />
    <Route path="/about" component={About} />
    <Route path="/contact" component={Contact} />
  </Router>
), document.body)
```

위와 같이 라우터를 설정하면 따로 레이아웃용 컴포넌트를 따로 사용하지 않아도 `<ul>`에 해당하는 메뉴는 메 페이지마다 렌더링 되게 된다.

`exact`의 경우 `Home` 컴포넌트가 `About` 또는 `Contact` 컴포넌트와 함께 렌더링 되는 것을 방지 하기 위한 속성이다. 쉽게 설명 하자면, `/about`를 `/ + about`. 즉, `Home + About` 컴포넌트라 생각하면 될 듯 하다.

또는 아래와 같이 메인 메뉴와 `<Route />`들을 다른 파일로 나누어 완전 분리 할 수도 있다.

```javascript
// index.js

import HeaderNav from 'HeaderNav';
import ContentContainer from 'ContentContainer';

render((
  <Router>
    <HeaderNav />
    <ContentContainer />
  </Router>
), document.body)
```

```javascript
// HeaderNav.js

render(
  <div>
    <ul>
      <li><Link to="/">Home</Link></li>
      <li><Link to="/about">Aount</Link></li>
      <li><Link to="/contact">Contact</Link></li>
    </ul>
  </div>
)
```

```javascript
// ContentContainer.js

render(
  <div>
    <Route exact path="/" component={Home} />
    <Route path="/about" component={About} />
    <Route path="/contact" component={Contact} />
  </div>
)
```

따라서 결론은 리액트 라우터 v4가 이전 버젼과 다른 점은 위에 설명한 것 보다 훨씬 많지만 개인 관점에서 가장 먼저 눈에 띄는 차이점은 v4에서의 `<Route />`는 일반 리액트 컴포넌트와 동일한 개념으로 사용할 수 있다는 것이고, 내 경우 v3보다 확실히 레이아웃 사용하는 것도 더 직관적으로 바뀌었고 특히 여러 prop들을 다른 컴포넌트로 패스하는 것이 전 보다 훨씬 편해진 것 같다는 생각이다.
