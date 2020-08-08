#  Webpack

### what and why

- webpack은 프론트엔드 리소스를 브라우저에서 이용 할 수 있는 번들로 묶고 패킹하는 모듈 번들러이다.
- 프론트엔드 리소스의 양이 방대해짐에 따라서 브라우저에서 하나의 페이지를 실행하기 위해, 모든 파일을 다운로드하면 속도가 느려지고, 부하가 올 수 밖에 없다. 이를 방지하기 위해 웹팩을 이용해 리소스를 번들해준다.
- 고로, 웹팩을 이용하면 여러 자바스크립트 소스를 다운로드 받을 필요 없이 압축 된 번들 파일 하나만 다운로드 받으면 된다.



### How

```javascript
 npm install -D webpack webpack-cli                                         
```

```javascript
npm install --save-dev style-loader css-loader  // css파일도 번들해주기 위햔 로더 설치 
```



- 하나의 페이지에 들어갈 여러개의 자바스크립트 소스를 웹팩으로 번들링 하기 위해서는, 자바스크립트 소스를 **모듈화** 시켜줘야 한다. 그리고 여러개의 모듈들을 import하여 사용하는 하나의 **엔트리 파일**이 필요하다.



#### index.js (Entry file)

```javascript
import hello_word from "./hello.js";
import world_word from "./world.js";
import css from './style.css';
document.querySelector('#root').innerHTML=hello_word +' ' + world_word;
```



- 이렇게 만들어진 엔트리 파일을 이용해 번들 하기 위해서는 webpack  설정 파일을 작성해주어야 한다.



#### webpack.config.js

```javascript
const path = require('path');

module.exports ={
    mode:"development",
    entry: { // 엔트리 파일 설정 
        index:"./src/index.js", 
    },
    output:{ // 아웃풋 설정 -> 해당 경로에 filename으로 번들된다. 
        path: path.resolve(__dirname, "public"),
        filename: '[name]_bundle.js'
    },
    module:{
        rules:[ // css를 번들해주기 위한 설정 
            {
                test:/\.css$/,
                use:[
                    'style-loader',
                    'css-loader'
                ]
            }
        ]
    }
}
```





#### 여러개의 엔트리파일과 아웃풋을 만들기 위한 설정파일

```javascript
module.exports ={
    mode:"development",
    entry: {
        index:"./src/index.js",
        about:"./src/about.js"
    },
    output:{
        path: path.resolve(__dirname, "public"),
        filename: '[name]_bundle.js'
    },
    module:{
        rules:[
            {
                test:/\.css$/,
                use:[
                    'style-loader',
                    'css-loader'
                ]
            }
        ]
    }
}
```





#### plugin을 사용하여 html 파일을 템플릿화 하기

```javascript
npm install --save-dev html-webpack-plugin
```

- html 소스를 엔트리파일과 같은 디렉토리에 위치시킨다. 
- 이전에 삽입했던 자바스크립트 src를 지워주고 순수 html 소스만 남겨둔다. 

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>practice</title>
</head>
<body>
    <h1>Hello Webpack</h1>
    <div id="root"></div>
    <a href="./about.html">HI!</a>
</body>
</html>
```



-  설정파일에 html-webpack-plugin 을 가져오고 , plubins 항목을 작성해준다.

```javascript
const path = require('path');
const HtmlWebpackPlugin=require('html-webpack-plugin');

module.exports ={
   ... 생략 ...
    plugins:[
        new HtmlWebpackPlugin({
            template: './src/index.html', // 현재 html 템플릿 위치 
            filename: './index.html', // 아웃풋이 위치하고자 하는 파일 
            chunks:['index'] // 어떤 bundle.js를 삽입할 것인가 ?  
        }),
        new HtmlWebpackPlugin({
            template: './src/about.html',
            filename: './about.html',
            chunks:['about']
        })
    ]
}
```

