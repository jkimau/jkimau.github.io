---
layout: post
category: en
title: "React Router v4 quick example"
tags: ["React Router"]
---

This is very simple example to show differences between previous versions of React router and v4.

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

This maybe the bare minimum example of React router v3. The concept here is very straightforward that `<Route />` can be listed alongside or nested insdie another `<Route />`.

If you want to use layout you can simply wrap layout, in this case `<App />`, around any inner components and you can pass children from the `<App />` component to children components.

In version 4, however, the biggest difference is that you can actually treat those each `<Route />` component just as another react UI component.

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

Being able to treat those `<Route />`s mean that you can put any React UI components inside `<Router></Router>`, so to achieve similar layout of v3 example with v4 becomes,

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

The top header navigation will appear on every page as layout. Note that `exact` attribute in the first `<Route />` is to prevent `Home` component to render together with `About` or `Contact` component again.
You can think of like `/` is `Home` and `/about` is `/ + about` which becomes `Home + About` component.

Furthermore, you can completely separate the header navigation and those three `<Route />`s into their own components.

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

That's it. The bottom line is that `<Route />`s can be treated just like any other React UI components and, for me, this makes easier to understand the whole flow and also makes easier to pass props down to other children components.
