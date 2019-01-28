[TOC]



# NodeJs第二天

## 1. 关于JavaScript结尾写不写分号的问题

### 代码风格

```
function say() {
  console.log('hello world')
}

// TypeError: say(...) is not a function
say()

;(function () {
  console.log('hello')
})()

// ;['苹果', '香蕉'].forEach(function (item) {
//   console.log(item)
// })

// ` 是 EcmaScript 6 中新增的一种字符串包裹方式，叫做：模板字符串
// 它支持换行和非常方便拼接变量
// var foo = `
// 大家好
// hello                            床前明月光
// world
// 哈哈哈`
// console.log(foo)

;`hello`.toString()

// 当你采用了无分号的代码风格的时候，只需要注意以下情况就不会有上面的问题了：
//    当一行代码是以：
//        (
//        [
//        `
//        开头的时候，则在前面补上一个分号用以避免一些语法解析错误。
//    所以你会发现在一些第三方的代码中能看到一上来就以一个 ; 开头。
//  结论：
//    无论你的代码是否有分号，都建议如果一行代码是以 (、[、` 开头的，则最好都在其前面补上一个分号。
//    有些人也喜欢玩儿一些花哨的东西，例如可以使用 ! ~ 等。
```



```javascript
var foo = 'bar'
var foo ='bar'
var foo= 'bar'
var foo = "bar"

if (true) {
  console.log('hello') 
}

if (true) {
    console.log('hello') 
}

if (true ){
      console.log('hello') 
}
```

为了约定大家的代码风格，所以在社区中诞生了一些比较规范的代码风格规范：dnsajkndkjsabnjkdnjksandjknsajkdnjkasnjkdnjksandjknsajkdnjksajkdnas

- [JavaScript Standard Style](https://standardjs.com/)

- Airbnb JavaScript Style

  

  > **总结，加上分号**

## 2. Apache功能模拟

### 2.1 初步实现Apache功能

```
var http = require('http')
var fs = require('fs')

// 1. 创建 Server
var server = http.createServer()

// 2. 监听 Server 的 request 请求事件，设置请求处理函数
//    请求
//      处理
//    响应
//    一个请求对应一个响应，如果在一个请求的过程中，已经结束响应了，则不能重复发送响应。
//    没有请求就没有响应。
// 
// 咱们以前使用过 Apache 服务器软件，这个软件默认有一个 www 目录，所有存放在 www 目录中的资源都可以通过网址来浏览
// 127.0.0.1:80/a.txt
// 127.0.0.1:80/index.html
// 127.0.0.1:80/apple/login.html

var wwwDir = 'D:/Movie/www'
//有利于更改www目录的地址
server.on('request', function (req, res) {
  var url = req.url
  // / index.html
  // /a.txt wwwDir + /a.txt
  // /apple/login.html wwwDir + /apple/login.html
  // /img/ab1.jpg wwwDir + /img/ab1.jpg
  if (url === '/') {
    fs.readFile(wwwDir + '/index.html', function (err, data) {
      // if (err) {
      //   res.end('404 Not Found.')
      // } else {

      // }

      if (err) {
        // return 有两个作用：
        //  1. 方法返回值
        //  2. 阻止代码继续往后执行
        return res.end('404 Not Found.')
      }
      res.end(data)
    })
  } else if (url === '/a.txt') {
    fs.readFile(wwwDir + '/a.txt', function (err, data) {
      if (err) {
        return res.end('404 Not Found.')
      }
      res.end(data)
    })
  } else if (url === '/index.html') {
    fs.readFile(wwwDir + '/index.html', function (err, data) {
      if (err) {
        return res.end('404 Not Found.')
      }
      res.end(data)
    })
  } else if (url === '/apple/login.html') {
    fs.readFile(wwwDir + '/apple/login.html', function (err, data) {
      if (err) {
        return res.end('404 Not Found.')
      }
      res.end(data)
    })
  }
})

// 3. 绑定端口号，启动服务
server.listen(3000, function () {
  console.log('running...')
})
```

### 2.2 Apache功能函数的优化

> **Apache功能模拟函数的内部肯定不能像上面那么写，我们试着找找规律就会发现，`request.url`返回的就是我们输入的url地址，规律找到了，接下来就来实现它。

```
var http = require('http')
var fs = require('fs')

var server = http.createServer()

var wwwDir = 'D:/Movie/www'

server.on('request', function (req, res) {
  var url = req.url
  // / index.html
  // /a.txt wwwDir + /a.txt
  // /apple/login.html wwwDir + /apple/login.html
  // /img/ab1.jpg wwwDir + /img/ab1.jpg
  

  var filePath = '/index.html'
  //什么都不输入的时候就默认打开index.html页面
  if (url !== '/') {
    filePath = url
    //如果输入了url，就将url的值赋给filePath，当然，前提是我们知道有哪几个文件和页面。
  }

  fs.readFile(wwwDir + filePath, function (err, data) {
    if (err) {
      return res.end('404 Not Found.')
    }
    res.end(data)
  })
})

// 3. 绑定端口号，启动服务
server.listen(3000, function () {
  console.log('running...')
})
```

### 2.3 读取文件名以及文件类型

> **我们在运行Apache的时候，如果www目录当中没有index.html页面的话。会出现www文件夹的目录，这要实现的话需要一个新的API：`fs.readdir`(注意`readdir`全小写格式)**

```
var fs = require('fs')

fs.readdir('./', function (err, files) {
//第一个参数是这个模块相对于文件夹的路径，第二个参数为一个回调函数，返回一个error对象以及一个files对象
  if (err) {
    return console.log('目录不存在')
  }
  console.log(files)
})
```

> **既然能够读取到文件夹中的文件名和文件类型信息就好办了，我们可以动态的生成文件信息。**

```
var http = require('http')
var fs = require('fs')

var server = http.createServer()

var wwwDir = 'D:/Movie/www'

server.on('request', function (req, res) {
  var url = req.url
  fs.readFile('./template.html', function (err, data) {
    if (err) {
      return res.end('404 Not Found.')
    }
    // 1. 如何得到 wwwDir 目录列表中的文件名和目录名
    //    fs.readdir
    // 2. 如何将得到的文件名和目录名替换到 template.html 中
    //    2.1 在 template.html 中需要替换的位置预留一个特殊的标记（就像以前使用模板引擎的标记一样）
    //    2.2 根据 files 生成需要的 HTML 内容
    // 只要你做了这两件事儿，那这个问题就解决了
    fs.readdir(wwwDir, function (err, files) {
      if (err) {
        return res.end('Can not find www dir.')
      }

      // 2.1 生成需要替换的内容
      var content = ''
      files.forEach(function (item) {
        // 在 EcmaScript 6 的 ` 字符串中，可以使用 ${} 来引用变量
        content += `
          <tr>
            <td data-value="apple/"><a class="icon dir" href="/D:/Movie/www/apple/">${item}/</a></td>
            <td class="detailsColumn" data-value="0"></td>
            <td class="detailsColumn" data-value="1509589967">2017/11/2 上午10:32:47</td>
          </tr>
        `
      })

      // 2.3 替换
      data = data.toString()
      data = data.replace('^_^', content)

      // 3. 发送解析替换过后的响应数据
      res.end(data)
    })
  })
})
server.listen(3000, function () {
  console.log('running...')
})
```

## 3. 模板引擎

### 3.1 在浏览器中使用模板引擎

```
<!--  art-template
art-template 不仅可以在浏览器使用，也可以在 node 中使用

安装：
   npm install art-template
   该命令在哪执行就会把包下载到哪里。默认会下载到 node_modules 目录中
   node_modules 不要改，也不支持改。 -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>06-在浏览器中使用art-template</title>
</head>
<body>
  <!-- 
    注意：在浏览器中需要引用 lib/template-web.js 文件

    强调：模板引擎不关心你的字符串内容，只关心自己能认识的模板标记语法，例如 {{}}
    {{}} 语法被称之为 mustache 语法，八字胡啊。
   -->
  <script src="node_modules/art-template/lib/template-web.js"></script>
  <script type="text/template" id="tpl">
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Document</title>
    </head>
    <body>
      <p>大家好，我叫：{{ name }}</p>
      <p>我今年 {{ age }} 岁了</p>
      <h1>我来自 {{ province }}</h1>
      <p>我喜欢：{{each hobbies}} {{ $value }} {{/each}}</p>
    </body>
    </html>
  </script>
  <script>
    var ret = template('tpl', {
      name: 'Jack',
      age: 18,
      province: '北京市',
      hobbies: [
        '写代码',
        '唱歌',
        '打游戏'
      ]
    })

    console.log(ret)
  </script>
</body>
</html>
```

### 3.2 在Node中引入模板引擎

> **在Node中没有script标签，所以要通过`fs.readFile`来读取文件当中的模板字符（{{ value }}），通过回调函数中的data，将读到的模板传给template的API。**

```
// 在 Node 中使用 art-template 模板引擎
// 模板引起最早就是诞生于服务器领域，后来才发展到了前端。
// 
// 1. 安装 npm install art-template
// 2. 在需要使用的文件模块中加载 art-template
//    只需要使用 require 方法加载就可以了：require('art-template')
//    参数中的 art-template 就是你下载的包的名字
//    也就是说你 isntall 的名字是什么，则你 require 中的就是什么
// 3. 查文档，使用模板引擎的 API


var template = require('art-template')
var fs = require('fs')

// 这里不是浏览器
// template('script 标签 id', {对象})

// var tplStr = `
// <!DOCTYPE html>
// <html lang="en">
// <head>
//   <meta charset="UTF-8">
//   <title>Document</title>
// </head>
// <body>
//   <p>大家好，我叫：{{ name }}</p>
//   <p>我今年 {{ age }} 岁了</p>
//   <h1>我来自 {{ province }}</h1>
//   <p>我喜欢：{{each hobbies}} {{ $value }} {{/each}}</p>
// </body>
// </html>
// `

fs.readFile('./tpl.html', function (err, data) {
  if (err) {
    return console.log('读取文件失败了')
  }
  // 默认读取到的 data 是二进制数据
  // 而模板引擎的 render 方法需要接收的是字符串
  // 所以我们在这里需要把 data 二进制数据转为 字符串 才可以给模板引擎使用
  var ret = template.render(data.toString(), {
    name: 'Jack',
    age: 18,
    province: '北京市',
    hobbies: [
      '写代码',
      '唱歌',
      '打游戏'
    ],
    title: '个人信息'
  })

  console.log(ret)
})
```

> `tpl.html`

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{{ title }}</title>
</head>
<body>
  <p>大家好，我叫：{{ name }}</p>
  <p>我今年 {{ age }} 岁了</p>
  <h1>我来自 {{ province }}</h1>
  <p>我喜欢：{{each hobbies}} {{ $value }} {{/each}}</p>
  <script>
    var foo = '{{ title }}'
  </script>
</body>
</html>
```

### 3.3 在Apache功能函数当中加入模板引擎

> **在Apache功能函数当中加入模板引擎，目录需要通过回调函数的中的files对象传入，模板通过data对象传入。**

```
//模板的代码
<tbody id="tbody">
      {{each files}}
      <tr>
        <td data-value="apple/"><a class="icon dir" href="/D:/Movie/www/apple/">{{$value}}/</a></td>
        <td class="detailsColumn" data-value="0"></td>
        <td class="detailsColumn" data-value="1509589967">2017/11/2 上午10:32:47</td>
      </tr>
      {{/each}}
    </tbody>
  </table>
```

```
var http = require('http')
var fs = require('fs')
var template = require('art-template')

var server = http.createServer()

var wwwDir = 'D:/Movie/www'

server.on('request', function (req, res) {
  var url = req.url
  fs.readFile('./template-apache.html', function (err, data) {
    if (err) {
      return res.end('404 Not Found.')
    }
    // 1. 如何得到 wwwDir 目录列表中的文件名和目录名
    //    fs.readdir
    // 2. 如何将得到的文件名和目录名替换到 template.html 中
    //    2.1 在 template.html 中需要替换的位置预留一个特殊的标记（就像以前使用模板引擎的标记一样）
    //    2.2 根据 files 生成需要的 HTML 内容
    // 只要你做了这两件事儿，那这个问题就解决了
    fs.readdir(wwwDir, function (err, files) {
      if (err) {
        return res.end('Can not find www dir.')
      }

      // 这里只需要使用模板引擎解析替换 data 中的模板字符串就可以了
      // 数据就是 files
      // 然后去你的 template.html 文件中编写你的模板语法就可以了
      var htmlStr = template.render(data.toString(), {
        title: '哈哈',
        files: files
      })

      // 3. 发送解析替换过后的响应数据
      res.end(htmlStr)
    })
  })
})
```

