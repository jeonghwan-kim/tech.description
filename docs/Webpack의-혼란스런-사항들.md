> 이 문서는 https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9#.idkxw015s 를 번역한 내용입니다.

# Webpack - 헷갈리는 부분
 Webpack은 React와 Redux 앱을 위한 주요한 module bundler 이다. Angular2와 다른 프레임워크를 사용하는 사람들 또한 많이 사용하는 것 같다.

 나는 처음 Webpack 설정 파일을 봤을 때, 매우 헷갈리고 외계인 같아 보였다. 몇시간 동안 Webpack을 둘러본 후  Webpack은 처음에 혼란을 야기 할 수 있는 고유한 문법과 새로운 철학 가지고 있습니다. 부수적으로, 이러한 철학들은 널리 퍼지게 할 책임이 있습니다. 

 처음 시작하기가 혼란스럽기 때문에, 다른 사람이 쉽게 시작 하고 파워풀한 기능을 사용할 수 있도록  포스트를 작성할 것입니다.

## Webpack 핵심 철학
 Webpack의 두가지 핵심 철학:
* 모든것은 module이다.
 * JS 파일들은 모듈(modules)이 될 수 있다. 또한 다른 모든 것(CSS, Images, HTMLS..)들도 모듈(modules)이 될 수 있다. 즉 `require(“myJSfile.js”)` 또는 `require(“myCSS.css”)` 를 할 수 있다. 이는 다루기 쉬운 작은 조각으로 나누거나, 재사용할 수 있다는걸 의미 합니다.

* 당신이 필요한것 그리고 필요한 때에 불러진다(Load).
 * 전형적인 **module bundlers**는 모든 모듈을 가져 와서, 큰 `bundler.js` 파일 하나를 생성한다. 그러나 실제 웹에서 `bundle.js`는 10~15MB이고 영원히 로드 되고 있을수 있다. 그래서 Webpack은 코드 분할 과 여러 “bundle” 파일을 생성하고 비동기적으로 앱의 일부를 로드 하는 다양한 기능이 있으며, 또한 당신이 필요한 것, 필요한 때에 로드 하게 해준다.

이제 헷갈려하는 부분에 대해 알아보자.

## Development(개발모드) VS Production(배포모드)
 첫번째 알아야할 점은 Webpack에는 많은 기능을 가지고 있다는 것이다. 일부는 “개발용”, 다른 일부는 “배포용” 그외 일부는 “배포-개발용”에 대한 것이다.

![A sample dev v/s prod Webpack files](https://d262ilb51hltx0.cloudfront.net/max/1600/1*WCAdMi04IFEWdngK8bkFcw.png)

> 일반적으로 대부분의 프로젝트는 2가지의 Webpack 설정 파일을 가지고 사용한다.

그리고 bundels 파일을 만들기 위하여 `package.json` 안에 스크립트를 다음과 같이 작성한다.

````javascript
 “scripts”: {
  //npm run build to build production bundles
  “build”: “webpack --config webpack.config.prod.js”,
  //npm run dev to generate development bundles and run dev. server
  “dev”: “webpack-dev-server”
 }
````

## webpack CLI(Command Line Interface) VS webpack-dev-server
*module bundler*인 **Webpack**은 2가지 인터페이스를 제공하는 점에 유의해야 한다.

1. Webpack CLI tool - 기본 인터페이스(Webpack이 알아서 설치한다.)
2. webpack-dev-server - Node.js server 사용(nodeJS 설치요망)

### Webpack CLI ( 배포 빌드엔 굿! )
 이 도구는 CLI 및 설정 파일(default : webpack.config.js)을 통해 옵션을 가져와서 *bundle*로 제공하기 위해 Webpack에 제공한다.

> CLI를 사용하여 Webpack을 배우기 시작할 수도 있지만, 대부분 CLI는 배포 빌드할때나 사용 할 것이다.

#### Usage:

##### OPTION 1: 

```` shell
//Install it globally
$ npm install webpack —g

//Use it at the terminal 
$ webpack //<--Generates bundle using webpack.config.js
````

##### OPTION 1: 
```` shell
//Install it locally & add it to package.json
$ npm install webpack —save

//Add it to package.json's script 
“scripts”: {
 “build”: “webpack --config webpack.config.prod.js -p”,
 ...
 }

//Use it by running the following:
"npm run build"

````

### webpack-dev-server ( 개발모드로 굿 )
 이것은 8080 포트로 실행되는 Express(node.js)서버입니다. 서버는 내부적으로 Webpack을 호출 합니다. 그러면 브라우저 리로딩(실시간 로딩), “Hot Module Replacement” 와 같은 추가적인 기능을 사용할 수 있는 이점이 있습니다.

#### Usage:

##### OPTION 1: 
````
//Install it globally
npm install webpack-dev-server --save

//Use it at the terminal
$ webpack-dev-server --inline --hot
````

##### OPTION 2:
````
// Add it to package.json's script 
“scripts”: {
 “start”: “webpack-dev-server --inline --hot”,
 ...
 }

// Use it by running 
$ npm start

Open browser at:
http://localhost:8080
```` 

### Webpack Vs webpack-dev-server options
  `inline`, `hot` 과 같은 옵션은 오로지 webpack-dev-server에만 있다는 것에 주목할 필요가 있다. 그리고 `hide-modules`은 CLI에만 있는 옵션이다.

### webpack-dev-server CLI options Vs config options
 관심 가질 또다른 점은 webpack-dev-server로 옵션을 전달 할 수 있는 2가지 방법이 있다.

* webpack.config.js를 통해서 devServer 객체를 사용한다.

```` javascript
//Via webpack.config.js
devServer: {
 inline: true,
 hot:true
 }
````

* 때론 devServer 설정이 작동하지 않는 경우가 있다. 그래서 다음과 같은 CLI 옵션을 좀더 선호한다.

````
//Via CLI
//package.json
{
scripts: 
   {“start”: “webpack-dev-server --hot --inline”}
}
````

### “hot” Vs “inline” webpack-dev-server options
`inline` 은 전체 페이지에 대한 실시간 리로딩(“Live Reloading”) 옵션이며, `hot` 은 컴포넌트가 수정 될 경우 그 수정된 부분만 리로드 해주는 부분 모듈 리로딩(“Hot Module Reloading”) 옵션이다. 만약 두개 옵션을 모두 지정할 경우 “Hot Module Reloading”이 처음 발생한다. 그리고 “Hot Module Reloading”이 안되면 전체 페이지 로딩을 한다.

```` shell
//1. 페이지를 로딩하지 않는다.
$ webpack-dev-server

//2. 전체 페이지를 로딩 한다.
$ webpack-dev-server --inline

//3. 부분 로딩  또는 전체 페이지 로딩
$ webpack-dev-server  --inline --hot
````

## “entry” — String Vs Array Vs Object
 “entry”는 root 모듈 또는 시작 지점이 무엇인지 Webpack에게 알려준다. “entry”는 String, Array, Object 형태로 될 수 있다. 이것이 바로 혼란스러운 점인데 다른 타입은 결국 다른 목적에 사용된다고 생각하면 된다.
  
만약 당신이 시작지점이 1개라면 다음과 같은 output format을 사용할 수 있다.

![다른 형식이지만 결국 같은 output](https://d262ilb51hltx0.cloudfront.net/max/1600/1*OnXpfv4zjL-5zO2Ha6mXDw.png)

### entry - Array
 만약 당신이 서로간 의존성이 없는 여러개의 파일을 사용하고 싶다면 Array 형식으로 사용하면 된다.
예를 들어 HTML에 “googleAnalystic.js” 가 필요 할수 있다. 그러면 “bundle.js” 끝에 다음과 같이 추가하도록 지정하면 원하는 output이 된다.

![의존성 없는 여러 파일 output](https://d262ilb51hltx0.cloudfront.net/max/1600/1*yLVdS3oN4Xo8KInoTIfi0A.png)

### entry-object
 만약 당신이 SPA(Single Page App)가 아니라 다중 페이지 어플리케이션을 개발한다면, entry-object를 사용하여 한번에 다중 bundle을 만들어서 사용 할 수 있다.  
아래 설정 파일은 2개의 bundle JS파일을 생성한다.(indexEntry.js , profileEntry.js 가 나오며 index.html, profile.html에 각각에 사용할 수 있다.)

![다중 bundle output](https://d262ilb51hltx0.cloudfront.net/max/1600/1*xB51RRC4ik6BBP2lJ90Iuw.png)

#### Usage:

```` html
//profile.html
<script src=”dist/profileEntry.js”></script>

//index.html
<script src=”dist/indexEntry.js”></script>
````

> Note : “entry”의 객체 key가 파일 이름으로 매핑된다.

### entry-combination
당신은 entry Object안에 Array 타입을 사용 할 수 있다. 예를 들어 아래 설정을 보면 3가지 파일이 생성된다.(3개의 파일이 있는 vendor.js 와 index.js , profile.js가 생성된다.)

![Object안에 Array output](https://d262ilb51hltx0.cloudfront.net/max/1600/1*yz76QY1fVzBGKJ-6X6Eleg.png)


## output — “path” Vs “publicPath”
 “output” 은 어디, 그리고 어떻게 결과 파일이 저장되는지에 대한 것이다. “output”은 ‘path’와 ‘publicPath’ 2가지 속성이 있어서 매우 헷갈린다.  

* path : 어디에 결과가 저장되는지 에 관한 것
* publicPath : 배포 빌드 할 때 Webpack plugins(ulr-loader,file-loader 같은..), CSS나 HTML파일 안에 URL들을 업데이트 해주기 위한 것(prefix개념)

![개발 vs 배포안에 publicPath](https://d262ilb51hltx0.cloudfront.net/max/1600/1*63Zta4mbC_3o44QdycrD7Q.png)

예를 들어, CSS에서 *localhost*에 `./test.png`를 로드 할 수 있는 URL이 있을수 있다. 하지만 *production*인 경우 `test.png`는  CDN이나 다른 지정된 경로로 실행 중 일 수있다. 그래서 수동으로 `./test.png`의 url을 수정 해주어야 배포 모드에서 올바른 위치에 파일에 접근 할 수 있다.  
수동으로 수정하지 않고 Webpack ‘publicPath’를 사용하면 수많은 플러그인과 다수의 CSS, HTML파일안에 URL들을 자동으로 업데이트 해준다.

![배포모드에서 publicPath](https://d262ilb51hltx0.cloudfront.net/max/1600/1*aOM5ZF8alWLr4BC0CfZe0w.png)

```` css
// 결과

// Development: Both Server and the image are on localhost
.image { 
  background-image: url(‘./test.png’);
 }

// Production: Server is on Heroku but the image is on a CDN
.image { 
  background-image: url(‘https://someCDN/test.png’);
 }
````

## Loaders And Chaining Loaders
“Loaders”는 JS,CSS와 같은 브라우저 허용 포맷과 같은 다양한 타입의 파일을 ‘load’ 또는 ‘import’ 하게 도와주는 추가 *node modules*이다. 게다가 “Loaders”는 `require` 또는 ES6의 `import` 를 JS파일을  가져올 수 있게 해준다.  

예를 들어 당신은 ES6로 쓰여진 JS를 변환해주는 `babel-loader`를 사용 할 수 있다.

```` javascript
module: {
 loaders: [{
  test: /\.js$/, ←Test for ".js" file, if it passes, use the loader
  exclude: /node_modules/, ←Exclude node_modules folder
  loader: ‘babel’ ←use babel (short for ‘babel-loader’)
 }]
````

### Chaining Loaders (오른쪽에서 왼쪽방향으로 실행된다.)
여러 *Loaders*를 연결하여 동일한 파일 형식으로 작업 할 수 있다.체이닝은 “!” 로 구분하고, 오른쪽에서 왼쪽 방향으로 작업을 수행한다.  

예를 들어 우리는 “myCssFile.css”를 가 있고, 그 파일을 HTML안에 `<style>CSS content</style>` 로 넣고 싶다. 우리는 `css-loader`, `style-loader` 인 2개의 loader로 진행 할 수 있다.

```` javascript
module: {
 loaders: [{
  test: /\.css$/,
  loader: ‘style!css’ <--(short for style-loader!css-loader)
 }]
````

#### 작동 방식

![](https://d262ilb51hltx0.cloudfront.net/max/1600/1*nes9iLmskmsD8Fp4Ek3u-A.png)

1. Webpack은 모듈안에 의존적인 CSS파일들을 검색한다. webpack은 `require(‘myCssFie.css’)`을 가지고 있는지 JS파일을 체크한다. 만약 찾았다면, 먼저 해당 파일을 `css-loader` 수행한다.
2. `css-loader`는 모든 CSS와 그안에 의존적인 다른 CSS(ex: `import otherCSS`) 를 JSON파일로 로드한다. 그후 Webpack은 결과들을 `style-loader`로 보낸다.
3. `style-loader`로 JSON을 가져와서, `<style>CSS content</style>` tag 를 추가하고, index.html 파일안에 tag를 삽입한다.

## Loaders 자체 구성 가능

> “Loader” 자체는 매개변수에 의해 다르게 작동하도록 설정 할 수 있다.

 아래 이미지에 나온 예를 보면, 1024bytes 보다 큰 이미지는 URL을 사용하고, 1024bytes 보다 작은 이미지는 DataURLs을 사용 하기 위한 `url-loader` 설정을 할 수 있다. "limit"매개 변수를 전달하는 방법은 다음 두 가지가 있다.

![url-loader 2가지 방법](https://d262ilb51hltx0.cloudfront.net/max/1600/1*-qVdcA3E8JSdtszxHqfIdA.png)

## .babelrc file
 **babel-loader** `presets`을 통해 **ES6** 에서 **ES5** 로 어떻게 변환 해야 하는지 그리고 React **JSX** 에서 **JS** 어떻게 파싱 하는지 대해 설정을 할 수 있게 해준다.

```` javascript
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel',
      query: {
        presets: ['react', 'es2015']
      }
    }
  ]
}
````

하지만 대부분에 큰 프로젝트에서는 babel 설정이 굉장히 방대할 수가 있다. 그래서 당신은 **.babelrc** 설정파일을 통해 **babel-loader** 설정을 대신 할 수 있다. 만약 **.babelrc** 파일이 있다면 **babel-loader** 는 자동으로 읽어들인다.

#### Usage:

```` javascript
//webpack.config.js 
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /(node_modules|bower_components)/,
      loader: 'babel'
    }
  ]
}

//.bablerc
{
 “presets”: [“react”, “es2015”]
}
````

## Plugins
> **Plugins**는 보통 결과물(**bundle**)에 작동하는 추가적인 **node_modules** 입니다.

예를 들어**uglifyJSPlugin**은 파일 사이즈를 줄이고, minimized를 한 bunlde 파일을 준다.

유사하게 **extract-text-webpack-plugin** 는 내부적으로 **css-loader**, **style-loader**를 사용하여 한곳에 CSS파일을 수집하고 외부 **style.css** 파일로 결과를 추출 하여 **index.html** 안에 **style.css** 를 삽입 하면 된다.

```` javascript
//webpack.config.js
//Take all the .css files, combine their contents and it extract them to a single "styles.css"
var ETP = require("extract-text-webpack-plugin");

module: {
 loaders: [
  {test: /\.css$/, loader:ETP.extract("style-loader","css-loader") }
  ]
},
plugins: [
    new ExtractTextPlugin("styles.css") //Extract to styles.css file
  ]
}
````

> 만약 당신이 HTML 안에 style 태그로 CSS 를 인라인하고 싶다면 **extract-text-webpack-plugin** 사용하지 않고 아래처럼 CSS와 *Style loaders*를 사용하면 된다.

```` javascript
module: {
 loaders: [{
  test: /\.css$/,
  loader: ‘style!css’ <—(줄임말 for style-loader!css-loader)
 }]
````

## Loaders VS Plugins
 아시다시피 **Loaders**는 번들이 생성되는 동안 또는 생성되기 전에 개별 파일 수준에서 작업한다.  
그리고 **Plugins**은 bundle 이나 chunk 레벨 그리고 bundle이 생성 된 후에 작동한다.  
**commonsChunksPlugins**와 같은 일부 플러그인은 번들 자체가 어떻게 생성되는지 수정한다.

## Resolving File Extensions
 대부분의 **Webpack** 설정 파일은 아래와 같이 빈 string 을 포함한 **resolve extensions** 라는 속성을 가지고 있다. 빈 string 은 `require(‘./myFile’)` 과 같은 빈 확장자를 import하게 도와주는 역할이다. 

```javascript
{
 resolve: {
   extensions: [‘’, ‘.js’, ‘.jsx’]
 }
}
```
