---
layout: post
title: '리액트 인라인 스타일링과 스타일드 컴포넌트'
category: 'kor'
tags: ['리액트', '스타일드 컴포넌트']
---

최근 리액트를 사용하면서 컴포넌트 개념이 프론트엔드 개발에 얼마나 많은 편리함을 가져다 주는지 절실히 느끼고 있다. 물론 엥귤라나 백본을 이용해서도 UI를 컴포넌트화 시킬 수 있지만 리액트는 태생 목적이 그러하듯 다른 툴들에 비해 훨씬 쉽고 직관적으로 컴포넌트를 생성하고 이용할 수 있게 만들어 준다.

특히 인라인 스타일링을 통해 방대하고 리팩터링 하기 어려운 지점까지 간 CSS 파일들을 더 이상 보지 않아도 된다는 점 하나만으로도 엄청난 매력이 있는 것 같다. 인라인 스타일링을 선호하지 않는 개발자들도 많이 있지만 이미 UI부분을 컴포넌트화 하기 위해 리액트를 사용하는 것이라면 굳이 하나의 컴포넌트에만 해당하는 CSS를 모두 모아 다른 곳에서 관리할 필요는 없다고 생각 한다.

## 리액트 인라인 스타일링의 장점

```javascript
import React from 'react';

export default function Button() {
  const buttonStyle = {
    padding: '10px 20px',
    fontSize: '16px',
    backgroundColor: true ? 'green' : 'white',
    borderRadius: '4px'
  }

  return <button style={buttonStyle}>Submit</button>;
}
```

1. **자바스크립트 오브젝트**

  위는 리액트 문서에서 추천하는 자바스크립트 오브젝트를 이용한 스타일링의 예인데 JSX를 이용한 실제 자바스크립트 오브젝트를 이용하여 스타일링을 하기 때문에 자바스크립트, 템플릿 그리고 CSS 파일들을 오가며 컨택스트를 바꾸지 않아도 된다. 이는 어떻게 보면 별 것 아닌것 같지만 실제 개발 과정은 물론이거니와 특히 디버깅시 한 파일에서 비지니스 로직과 템플릿 그리고 스타일링을 한 눈에 볼 수 있기 때문에 생각 보다 훨씬 효율적이다.

2. **HTML CSS 매핑**

  또한 특정 CSS가 특정 DOM 노드에만 적용되게 하기 위해 HTML에 클래스명을 나열하고 CSS에서는 해당 노드와 매핑하는 과정이 존재하는 기존의 방식에 비해 HTML과 CSS간의 매핑을 아주 간소화 해준다는데에도 의미가 있다. 위 코드에서 볼 수 있듯이 클라스명을 이용한 매핑에 비하면 코드가 현저히 깔끔해진 것을 볼 수 있다.

3. **런타임시 스타일 적용**

  특히 가장 큰 장점은 변수를 이용해 런타임에서 스타일을 결정하는 것이 가능해짐으로써 여러 클래스명의 조합을 이용해 복잡하게 스타일링할 필요가 없게 되었다는 점이다. 실제 개발 과정에선 이 부분이 가장 큰 장점으로 느껴졌다.

하지만 리액트 인라인 스타일에 장점만 있는 것은 아니다.
사실 결정적인 큰 단점이 있다. 그것은 바로 진짜 CSS가 아니라는 것이다.
이는 단순히 `font-size`를 `fontSize`로 해야 한다던지, 프로퍼티 값들을 스트링으로 해야 한다는 등의 어색함에서 끝나는게 아니라 `:before` `:after` `:hover`등과 같은 pseudo 셀렉터들을 전혀 사용할 수 없고 또 미디어 쿼리도 사용할 수 없다는 점이다. 이는 너무 매력적인 리액트 인라인 스타일링을 순식간에 무용지물로 만들어 버릴 만큼 크게 다가 왔다.

그 때 이 모든 것을 해결해 주는 [styled-component](https://github.com/styled-components/styled-components)라는 라이브러리를 알게 되었고 사용해 본 순간 바로 완소 패키지가 되었다.

styled-components를 적용시킨 예를 보자.

```javascript
import React from 'react';
import sc from 'styled-components';

export default function Button() {
  const Button = sc.button`
    padding: 10px 20px;
    font-size: 16px;
    ${props => props.bg && 'background-color: blue'}
    border-radius: 4px;
  `;

  return <Button bg>Click Here</Button>;
}
```

위 코드에서 볼 수 있듯 이 패키지를 이용하면 런타임 스타일링을 스코프 변수 뿐만 아니라 컴포넌트에 prop 형태로 바로 주입할 수 있고 또 아래와 같이 스타일 자체를 컴포넌트화 시킬 수 있을 뿐 아니라 스타일 된 컴포넌트를 상속 받아 사용하는 것이 굉장히 직관적이다.

```javascript
// Button.js
import sc from 'styled-components';

// 스타일만으로 구성된 컴포넌트
export default const Button = sc.button`
  padding: 10px 20px;
  font-size: 16px;
  ${props => props.bg && 'background-color: blue'}
  border-radius: 4px;
`;

// form.js
import React from 'react';
import Button from 'Button';

// Button 스타일을 상속 받음
const HoverEffectButton = sc(Button)`
  &:hover {
    background-color: red;
  }
`;

export default function App() {
  return <HoverEffectButton>Submit</HoverEffectButton>;
}
```

무엇보다 중요한건 리액트 인라인 스타일링과 달리 이 패키지를 이용해 작성하는 CSS는 실제 CSS와 동일한 것이다. 따라서 pseudo 셀렉터 뿐만아니라 CSS에서 사용할 수 있는 모든 문법이나 미디어 쿼리등을 사용할 수 있다.
