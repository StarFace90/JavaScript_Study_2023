# Chapter49 Babel과 Webpack을 이용한 ES6+/ES.NEXT 개발 환경 구축

### 트랜스파일러(transpiler)
Babel은 트랜스파일러로 ES6+, ES.NEXT의 최신 ECMAScript 사양으로 구현을 하면 브라우저 버전에 따라 문제가 발생할 수 있다.  
그런 이유로 IE 혹은 구형 브라우저에서도 동작하도록 ES5 사양의 소스코드로 변환하는 것을 트랜스파일링이라 한다.  

그 중에서 트랜스파일러로 Babel을 많이 사용한다. Babel을 사용하기 위해선 몇 가지 패키지를 설치해야 한다.  

|패키지/플러그인|패키지 이름|
|------|---|
|Babel|\@babel/cli
|Babel|\@babel/core
|Babel 프리셋|\@babel/preset-env
|Babel 플러그인|\@babel/polyfill

\@babel/preset-env 패키지는 함께 사용되어야 하는 Babel 플러그인을 모아 둔 것으로 **Babel 프리셋**이라고 부른다.  
\@babel/polyfill 패키지는 트랜스파일링을 해도 지원하지 않아 변환이 안되는게 있는데(ex. Object.assign, Promise 등) 그런걸 사용하기 위해서는 polyfill을 설치해야 한다.  

babel.config.json 설정 파일에 아래와 같이 설정해줘야 한다.
```javascript
// babel.config.json
{
	"presets": ["@babel/preset-env"]
}
```

### 모듈 번들러(module bundler)
Webpack은 모듈 번들러로 의존 관계에 있는 자바스크립트, CSS, 이미지 등의 리소스들을 하나의 파일로 번들링하는 것을 말한다.  
즉, 여러 개의 파일에 있는 소스코드를 하나의 파일로 묶어주는 것을 말한다.

|패키지/플러그인|패키지 이름|
|------|---|
|Webpack|webpack
|Webpack|webpack-cli
|Webpack 플러그인|babel-loader

Webpack이 실행될 때 참조하는 설정 파일로 다음과 같다.
```javascript
// webpack.config.js
const path = require('path');

module.exports = {
	entry: ['@babel/polyfill', './src/*'],
	output: {
		path: path.resolve(__dirname, 'dist'),
		filename: 'bundle.js'
	},
	module: {
		rules: [
			test: /\.js$/,
			include: [
				path.resolve(__dirname, 'src/*')
			],
			exclude: /node_modules/,
			use: {
				loader: 'babel-loader',
				options: {
					presets: ['@babel/preset-env'],
					plugins: []
				}
			}
		]
	},
	devtool: 'source-map',
	mode: 'development'
};
```