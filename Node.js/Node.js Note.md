# Node.js 笔记

## 目录

- [fs 模块](#fs-模块)
- [path 模块](#path-模块)
- [http 模块](#http-模块)
- [模块作用域](#模块作用域)

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
