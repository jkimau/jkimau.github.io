---
layout: post
title: "웹팩(Webpack) 시작하기"
---

웹팩은 최근 프론트엔드 개발에서 아주 많이 사용되는 번들러인데, 막상 사용해 볼려니 공식 문서가 너무 밑도 끝도 없는 느낌이 있어 많은 사람들이 처음 웹팩을 시작하는데 많은 어려움을 겪고 있는 듯 하다. 나 또한 많은 삽질을 했고 여기 저기 많은 글들을 읽고 직접 세팅해 보면서 알게된 기본 사항들을 간단한 글로 공유하고자 한다.

## 번들링
싱글 페이지 어플리케이션에서 많이 사용하는 방식인데, 쉽게 말해 자바스크립 모듈들을 디펜던시 순서를 맞게 한 파일로 묶어주는 과정이라 할 수 있다.
웹팩 이전에도 require.js 또는 browserify를 이용하거나 또는 Grunt나 Gulp등의 테스크 러너를 이용해 번들링을 해 왔었다. 다만 최근들어 웹팩이 주목을 받고 순식간에 유저들을 끌어들인데에는 간단한 사용법이 큰 역할을 했다고 볼 수 있다.
한 가지 단점이라면, 공식 문서가 상당히 구리기 때문에 처음 웹팩을 접한 입장에서는 사용법이 결코 간단하지 않다는 것이다.

## 설정
모든 설정이나 옵션을 자세히 설명하고 싶지만 그럴만한 실력이 안되므로 가장 기본적으로 사용하는 설정들 몇 개만 살펴 보자.

```
context:  __dirname
```

컨텍스트는 현재 앱이 있는 루트 경로라 생각하면 된다. 노드를 사용할 경우 간단히 `__dirname` 변수를 사용하는게 일반적이다.

```
entry: ‘.app/app.js’
```

자바스크립트 앱이 시작되는 첫 파일이다. 이 파일을 기점으로 앱을 정의하고 모듈을 불러오기 시작한다. 엔트리 프로퍼티(property)는 String또는 Object나 Array 형태로도 입력 할 수 있는데 각각 다른점이 있다.

```
output: {
  path: ‘./public’,
  publicPath: ‘’,
  filename: ‘bundle.js’
}
```

output은 번들된 결과 파일을 어디에 저장할지 결정하는 부분이다. 위 경우 모든 자바스크립트 파일을 번들링 한 후 그 결과 파일 이름을 bundle.js로 하고 public이라는 디렉토리에 저장하라는 명령이다.
만약 엔트리가 Object 형태로 여러 파일들이 설정되었다면, 최종 번들되 파일은 app_bundel.js, vendor_bundle.js 두 개의 번들 파일이 생성된다.

```
entry: {
  app: './app.js',
  vendor: './jquery.js'
},
output: {
  filename: '[name]_bundle.js',
  ...
}
```

주의할 점은, 다수의 엔트리 파일을 지정했다면 output.filename에는 반드시 [name]이라는 변수를 사용해 번들 파일을 구분 해야 한다. 그렇지 않으면 두 개의 번들 파일이 모두 bundle.js라는 같은 이름으로 생성되기 때문이다.

publicPath는 마크업 템플릿에서 정적 파일들을 불러올때의 경로를 설정하는 것인데 특히 cdn등의 외부 서버를 따로 사용 할 경우 cdn 호스트 주소를 적어주면 템플릿에서는 매번 cdn 호스트 주소를 적지 않고 호스트 주소 이후의 내부 경로만 지정해서 파일들을 불러 올 수 있다.
예를 들어 모든 이미지가 https://example.cdn.com/hash/img 라는 메인 경로에 있을 경우 publicPath: ‘https://example.cdn.com/hash/’ 라고 지정하면 템플릿에서는 아래와 같이 cdn 호스트 경로 없이 내부 경로만 이용해 사용할 수 있게 해준다.

```
// 웹팩 설정
output: {
  publicPath: ‘https://example.cdn.com/hash/’,
  ...
}

// 템플릿
<img src=”img/momo.png” />
```

다음은 개인적인 생각에 웹팩을 다른 번들러 보다 가장 돋보이게 하는 부분이라 생각하는 모듈 로더 부분이다.

```
module: {
  loaders: [
    { test: /\.js$/, exclude: '/node_modules', loader: 'babel' },
    { test: /\.scss$/, loaders: ['style', 'css', 'sass'] },
    { test: /\.html$/, loaders: ['html'] }
  ]
}
```

Gulp나 Grunt 등의 테스크 러너를 사용하면 src(), pipe(), dest() 등을 이용해 파이프라인을 만들고 미들웨어들을 사용해 중간 중간 원하는 작업을 해가며 다소 복잡하게 로딩을 해야 하는데 웹팩에서는 아주 간단하게 한줄로 설정이 끝난다.

scss 로더 부분을 설명하자면 모듈 번들링시 .scss로 끝나는 파일이 있으면 sass-loader를 이용해 css로 변환하고 다시 css-loader를 이용해 style로 변환하고 style-loader를 이용해 다큐먼트에 css를 적용한다는 의미다.

주의 할 점은 style, css, scss 순서가 중요하다는 점이다. 그리고 style-loader, css-loader, sass-loader 및 node-sass 등을 npm을 통해 설치해야 한다.
loaders(복수) 프로퍼티 대신 loader(단수) 프로퍼티를 사용하면 아래와 같이 표현해야 한다.

```
{ test: /\.scss$/, loader: style!css!scss }
```

또한 exclude 프로퍼티를 이용해 제외할 디렉토리를 정하거나 include 프로퍼티를 이용해 적용시킬 디렉토리만 따로 설정 할 수도 있다.
resolve 설정은 app 디렉토리 안에 있는 모든 .js 파일은 .js라는 확장자명이 없어도 파일을 인식할 수 있게 해 준다. js 파일 이외에도 모두 적용할 수 있다.

```
resolve: {
    root: __dirname + '/app',
    extensions: ['', '.js']
}
```

그리고 웹팩 자체에서 제공하거나 따로 노드 모듈을 설치해 각종 플러그인 설정도 할 수 있는데, 수 많은 플러그인들이 있으므로 상황에 맞는 플러그인을 설치해 사용 하면 된다. 그 중 기본적으로 가장 많이 쓰이는 것 중 두 가지는 optimize 플러그인 들인 것 같다. 플러그인 설정의 간편함 또한 개인적으로 웹팩의 큰 장점 중 하나라고 생각한다.

```
plugins: [
  new webpack.optimize.DedupePlugin(),
  new webpack.optimize.UglifyJsPlugin({
    minimize: true,
    output: {
      comments: false
  }})
]
```

마지막으로 devtool은 아래와 같이 소스맵을 이용하는 등 개발환경에서 필요한 것을 설정할 수 있고 devServer는 커멘드 라인에서 webpack-dev-server를 실행 할때 옵션으로 사용하는 명령어를 미리 설정하는 부분이다.

```
devtool: ‘source-map’,
devServer: {
  port: 9000,
  contentBase: __dirname + ‘/app’,
  inline: true
}
```

예를 들어, 위 설정이 없으면 웨팹 서버를 실행할 때 아래와 같은 명령을 해야 하지만

```
webpack-dev-server --inline --port 9000 --content-base /app
```

devServer설정이 되어 있다면 간단하게 아래와 같이 명령하면 된다.

```
webpack-dev-server --hot
```

주의 할 점은 devServer 설정에서 hot: true 라는 프로퍼티를 설정하고 실제 커맨드라인에서는 hot 옵션 생략하는 경우 HMR(hot module replacement) 기능이 적용되지 않는다. 따라서 HMR 기능을 사용하고 싶다면 devServer 프로퍼티에서의 설정 여부에 상관없이 커멘드라인에서 반드시 따로 hot 옵션을 명시해 줘야 한다.

사실 프론트엔드 프레임웍에 CLI들이 따라오는 경우가 점점 많아지고 있는 걸로 봐서 몇 년 지나지 않아 웹팩도 더이상 사용하지 않게 될 수도 있지만 현재로써는 특히 리액트 진영에서 거의 필수로 사용되고 있는 번들러이기 때문에 리액트 사용자라면 적어도 한동안은 웹팩의 덕을 톡톡히 볼 수 있을거라 생각 한다.
