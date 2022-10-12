# Node.js 笔记

## 目录

- [fs 模块](#fs-模块)
- [path 模块](#path-模块)
- [http 模块](#http-模块)
- [模块作用域](#模块作用域)
- [npm 包管理器](#npm-包管理器)
- [express](#express)

<br>
<br>

## 主体内容

### `fs` 模块
- 主要用于读写文件

1. 引入模块
    ```javascript
    const fs = require('fs')
    ```

2. 读取文件
    ```javascript
    fs.readFile(path, type, function(err, data){})
    // egg:
    fs.readFile('./demo.html', 'utf-8', (err, dataStr) => {
        if (err) { return console.log(err.message) }
        // dataStr ...
    })
    ```

3. 写入文件
    ``` javascript
    fs.writeFile(path, data, type, function(err) {})
    // egg:
    fs.writeFile('./txt.html', text, 'utf-8', (err) => {
        if (err) { return console.log(err.message) }
    })
    ```


### `path` 模块
- 主要用于处理文件的 URL 路径

1. 引入模块
    ```javascript
    const path = require('path')
    ```

2. 拼接 api (往往配合__dirname)
    ```javascript
    path.join(__dirname, url)
    ```

3. More...


### `http` 模块
- 主要用于控制 Web 服务器

1. 引入模块
    ```javascript
    const http = require('http')
    ```

2. 控制服务器流程
    ```javascript
    // 引用模块
    const http = require('http')
    const fs = require('fs')
    const path = require('path')

    // 实例化 web 服务器
    const server = http.createServer()

    // 绑定 request 事件，监听 request 请求
    server.on('request', (req, res) => {
        const url = req.url
        const method = req.method
        let resPath

        // 直接拼接路径 ↓↓↓
        // resPath = path.join(__dirname,url)

        // 优化拼接路径 ↓↓↓ 
        if (url === '/' || url === '/index.html') {
            resPath = path.join(__dirname, './index.html')
        } else {
            resPath = path.join(__dirname, './more/', url)
        }

        // res.setHeader 设置 'Content-Type' 为 'text/html; charset=utf-8' 防止中文乱码
        res.setHeader('Content-Type', 'text/html; charset=utf-8')

        // 根据路径，查找并返回资源
        fs.readFile(resPath, 'utf-8', (err, dataStr) => {
            if (err) return res.end('<h1>404 NOT FOUND</h1>')

            res.end(dataStr)
        })
    })

    // 为服务器绑定端口，开始运行
    server.listen(80, () => { console.log('服务器开始运行！') })
    ```


### 模块作用域
- 和函数作用域类似，在自定义模块中定义的 ***变量、方法等成员，只能在当前模块内被访问*** ，这种模块级别的访问限制，叫做模块作用域。

- `module` 对象 ： 每个 js 模块中用于存储当前模块信息的对象
    - `module.exports` 对象 : 模块内的一个对象，属性为想要暴露给外界的 ***模块内成员***
        ```javascript
        const module = require('./xxx.js'); // 此时 module 接收到的就是 xxx.js 内部的 module.exports 对象
        ```

    - `exports` 对象 ： 为了方便操作 Node.js 提供的对象，默认和 `module.exports` 指向同一个对象。当 `module.exports` 指向其他对象，则会与 `exports` 区别开，而**最终 `require()` 接收的是 `module.exports` 所指向的对象**。  
    ***最好不要混用 `module.exports` 和 `exports` 以防二者指向不唯一***



## npm 包管理器
第三方模块又称为包，而安装 Node.js 环境自带包管理器 npm 便于我们安装 管理 和使用包。

- 安装包  
`npm install 包名`  
    - 安装包  
    `npm install express`

    - -D : 安装开发依赖包  
    `npm install webpack -D`

    - -g : 安装全局包  
    `npm install nodemon -g`

- 卸载包  
`npm uninstall 包名`
    - 卸载包  
    `npm uninstall express`  
    `npm uninstall webpack`

    - 卸载全局包  
    `npm uninstall nodemon -g`




## express 
一个第三方**包**，是基于 Node.js 平台，快速、开放、极简的 **Web 开发框架**。  
提供了快速创建 Web 服务器的便捷方法。

- 安装  
    `npm install express`

- 创建基本的 web 服务器
    ```js
    const express = require('express')          // 导入 express 模块  
    const app = express()                       // 实例化
    
    // 挂载路由，监听 GET 请求
    app.get('/index', (req, res) => { 
        // do sth.
        res.send( data )                        //将 data 响应给客户端
    })    

    app.listen(80, () => { ... })               //  在80端口启动服务器
    ```

- 获取 url 中的一些参数  
    ```js
    // eg: url = http://localhost/user/18/xiaoming?search1=xx&search2=yy

    // ":" 符号匹配动态参数, 匹配到的值存于 req.params 对象中
    app.get('/user/:id/:name', (req, res) => {  // id, name 为接收值的变量名，由程序员自定义
        console.log(req.params) // { id : 18, name : xiaoming }

        //req.query 对象查询url中的搜索
        console.log(req.query)  // { search1 : xx, search2 : yy} 
    })
    ```

- 路由  
    - 定义  
    在 Express 中，路由指的是客户端的请求与服务器处理函数之间的映射关系。Express 中的路由分 3 部分组成，分别是请求的类型、请求的 URL 地址、处理函数。格式如下  
    
        ```js
        app.METHOD(PATH,HANDLE)
        // eg: METHOD == get, PATH == '/', HANDLE == function(req, res)
        app.get('/',function(req, res) {
            ...
        })
        ```

    - 路由匹配顺序  
    每当一个请求到达服务器之后,***顺序匹配每一个路由***。当请求方式和URL都匹配成功时调用对应路由的 function 处理请求。

    - 路由模块化  
    为了方便对路由进行模块化的管理，Express 不建议将路由直接挂载到 app 上，而是推荐将路由抽离为单独的模块。将路由抽离为单独模块的步骤如下：
        1. 创建路由模块对应的 .js 文件
        1. 调用 `express.Router()` 函数创建路由对象
        1. 向路由对象上挂载具体的路由
        1. 使用 `module.exports` 向外共享路由对象
        1. 使用 `app.use()` 函数注册路由模块

            ```js
            // ----------router/user.js------------
            // 引入 express 模块并实例化 router
            const express = require('express')
            const router = express.Router()

            // 为 router 对象挂载路由
            router.get('/user',(req, res) => { ... })
            router.post('/user',(req, res) => { ... })  // 此时匹配路由的 path === /user
            

            // 向外暴露 router 路由对象
            module.exports = router

            // --------------index.js--------------
            // 导入路由模块
            const userRouter = require('./router/user.js')
            
            // 使用 app.use() 注册路由模块,添加统一访问前缀 /api
            app.use('/api', userRouter)                 // 此时匹配路由的 path === /api/user
            
            ```

- 中间件  
实际上就是对请求达到路由前进行预处理的函数。将这些函数独立成一个个模块，称之为中间件。  


    - 函数模板

        ```js
        // 相比一般路由处理函数，多一个 next 参数
        let middleWare = function(req, res, next) {

            // do sth...

            next()  // 中间件处理结束，必须调用 next() 
        }
        ```

    - 挂载方式

        ```js
        app.get('/', middleWare, (req, res) => {
            // do sth...

            res.send(data)
        })
        ```

    - 中间件模块化  
    使用单独的 js 文件独立出中间件模块，减少代码冗余，提升可复用性。类似 包 、模块。  
    ***给路由用的包***

        ```javascript
        // ---------myMiddleWare.js-----------
        function myMiddleWare(req, res, next) {
            // do sth...

            next()  // next
        }

        module.exports = myMiddleWare

        // ---------main.js------------
        const express = require('express')
        const myRouter = require('./router/myRouter.js')
        const myMiddleWare = require('./myMiddleWare.js')

        let app = express()

        app.use(myMiddleWare)   // 注册启用全局中间件
        app.use(myRouter)   // 注册启用路由

        app.listen(80, () => { ... })
        ```

    - 部署顺序  
    当一个请求到达服务器，中间件和路由按顺序匹配并处理。因此中间件应在路由之前部署，并且使用 next() 使请求继续前往下一个处理步骤。
    

- CORS跨域  
cross-origin resource sharing 跨域资源共享，通过添加**响应头**使浏览器不拦截服务器响应给客户端的资源
    - 在路由中手动添加

        ```js

        router.post(':3000/api', (req, res) => {
            // do sth...

            res.setHeader('Access-Control-Allow-Origin', '*')   // * 通配符

            res.send(data)
        })
        ```

    - 使用cors中间件

        ```js
        // --------cmd--------
        npm install cors

        //---------main.js-------
        const cors = require('cors')    // 导入
        app.use(cors())     // 配置

        ```

- JSONP  
使用 script 发送 GET 请求绕开浏览器拦截进行跨域而非 XMLHttpRequest 不属于 AJAX   
原理：接收搜索字段，将 callback 函数和需要响应的资源拼接成字符串返回

    ```js
    router.get('/api/jsonp', (req, res) => {
        let funcName = req.query.callback
        let data = ...
        
        // 返回 funcName(data)给 script 标签进行函数调用
        res.send(`${funcName}(${data})`)
    })
