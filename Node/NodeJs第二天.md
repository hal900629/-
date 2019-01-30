[TOC]



# NodeJs第二天

## 1. 关于JavaScript结尾写不写分号的问题

### 代码风格

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

### 3.4 客户端渲染和服务端渲染

> **客户端渲染Ajax，页面不用刷新即可以响应数据，但是不利于SEO；服务端渲染的话，页面刷新稍微慢一点，但是利于SEO。所以，现在都是两种技术混着用的，需要SEO的部分用服务端渲染，不需要SEO的就用异步请求数据。**

> - **服务端渲染**
>   - **说白了就是在服务端使用模板引擎**
>   - **模板引擎最早诞生于服务端，后来才发展到了前端**
> - **服务端渲染和客户端渲染的区别**
>   - **客户端渲染不利于 SEO 搜索引擎优化**
>   - **服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的**
>   - **所以你会发现真正的网站既不是纯异步也不是纯服务端渲染出来的,而是两者结合来做的**
>   - **例如京东的商品列表就采用的是服务端渲染，目的了为了 SEO 搜索引擎优化**
>   - **而它的商品评论列表为了用户体验，而且也不需要 SEO 优化，所以采用是客户端渲染**

## 4. 留言板案例	

### 4.1 处理网站当中的静态资源

> **对于网站当中的静态资源，我们一般统一放到一个`public`的文件夹当中，将整个`public`文件夹暴露出去，然后在注意一下，服务器端的路径是url的路径，即你暴露出去的路径，不是前端页面当中所说的文件路径了，这点要注意**

> **index.html 页面**

```javascript
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>留言本</title>
  <!-- 
    浏览器收到 HTML 响应内容之后，就要开始从上到下依次解析，
    当在解析的过程中，如果发现：
      link
      script
      img
      iframe
      video
      audio
    等带有 src 或者 href（link） 属性标签（具有外链的资源）的时候，浏览器会自动对这些资源发起新的请求。
   -->
   <!-- 
      注意：在服务端中，文件中的路径就不要去写相对路径了。
      因为这个时候所有的资源都是通过 url 标识来获取的
      我的服务器开放了 /public/ 目录
      所以这里的请求路径都写成：/public/xxx
      / 在这里就是 url 根路径的意思。
      浏览器在真正发请求的时候会最终把 http://127.0.0.1:3000 拼上

      不要再想文件路径了，把所有的路径都想象成 url 地址
    -->
  <link rel="stylesheet" href="/public/lib/bootstrap/dist/css/bootstrap.css">
</head>

<body>
  <div class="header container">
    <div class="page-header">
      <h1>Example page header <small>Subtext for header</small></h1>
      <a class="btn btn-success" href="/post">发表留言</a>
    </div>
  </div>
  <div class="comments container">
    <ul class="list-group">
      {{each comments}}
      <li class="list-group-item">{{ $value.name }}说：{{ $value.message }} <span class="pull-right">{{ $value.dateTime }}</span></li>
      {{/each}}
    </ul>
  </div>
</body>

</html>
```

> **post.html 页面**

```javascript
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <link rel="stylesheet" href="/public/lib/bootstrap/dist/css/bootstrap.css">
</head>

<body>
  <div class="header container">
    <div class="page-header">
      <h1><a href="/">首页</a> <small>发表评论</small></h1>
    </div>
  </div>
  <div class="comments container">
    <!-- 
      以前表单是如何提交的？
      表单中需要提交的表单控件元素必须具有 name 属性
      表单提交分为：
        1. 默认的提交行为
        2. 表单异步提交

        action 就是表单提交的地址，说白了就是请求的 url 地址
        method 请求方法
            get
            post
     -->
    <form action="/pinglun" method="get">
      <div class="form-group">
        <label for="input_name">你的大名</label>
        <input type="text" class="form-control" required minlength="2" maxlength="10" id="input_name" name="name" placeholder="请写入你的姓名">
      </div>
      <div class="form-group">
        <label for="textarea_message">留言内容</label>
        <textarea class="form-control" name="message" id="textarea_message" cols="30" rows="10" required minlength="5" maxlength="20"></textarea>
      </div>
      <button type="submit" class="btn btn-default">发表</button>
    </form>
  </div>
</body>

</html>
```

> **后台服务器代码hello.js**

```javascript
var fs = require('fs');
var http = require('http');
var template = require('art-template');
var member = [
    {
    	name: '张三',
    	message: '今天真几把冷',
    	data: '2019-1-29'
    },
	{
    	name: '张三',
    	message: '今天真几把冷',
    	data: '2019-1-29'
    },   
	{
    	name: '张三',
    	message: '今天真几把冷',
    	data: '2019-1-29'
    },    
	{
    	name: '张三',
    	message: '今天真几把冷',
    	data: '2019-1-29'
    },    
	{
    	name: '张三',
    	message: '今天真几把冷',
    	data: '2019-1-29'
    }    
]
http.createServer(function(req, res) {
   var url = req.url;
   if(url === '/') {
		fs.readFile('./views/index.html', function(err, data) {
		   	if(err) {
		   		return res.end('404 not found');
		   	}
		   	var htmlStr = template.render(data.toString(), {
		   		member: member
		   	})
		   	res.end(htmlStr)
		   })      
   }else if(url.indexOf('/public/') === 0) {
   //判断是不是以/public/开头的url地址，如果是的话，读取文件的内容
   	fs.readFile('.' + url, function(err, data) {
   		if(err) {
   			return res.end('404 not found');
   		}
   		res.end(data);
   	})
   }else if(url === '/post') {
   	fs.readFile('./views/post.html', function(err, data) {
    	if(err) {

    		return res.end('405 not found');
    	}
    	res.end(data);
   	})
   }
   
}).listen(3000, function() {
	console.log('running...')
})
```

### 4.2 URL模块

> **有一个URL核心模块，来处理客户端传过来的请求，`url = require('url') `来引入url这个模块，我们看一下url这个模块有哪些属性和方法。**

```javascript
var url = require('url');
console.log(url);
```



```javascript
$ node 08-url模块.js
{ Url: [Function: Url],
  parse: [Function: urlParse],
  resolve: [Function: urlResolve],
  resolveObject: [Function: urlResolveObject],
  format: [Function: urlFormat],
  URL: [Function: URL],
  URLSearchParams: [Function: URLSearchParams],
  domainToASCII: [Function: domainToASCII],
  domainToUnicode: [Function: domainToUnicode],
  pathToFileURL: [Function: pathToFileURL],
  fileURLToPath: [Function: fileURLToPath] }

```

> **`url.parse()`可以将一个完整的URL地址，分为很多部分，常用的有：host、port、pathname、path、query。`url.parse(..., true)`有两个参数，第二个参数设置为true，`query`属性会生成一个对象，如果为false,则返回url对象上的`query`属性会是一个未解析，未解码的字符串，默认为false **

```javascript
var url = require('url');
var obj = url.parse('/pinglun?name=jason&message=helloworld');
console.log(obj);
```



```javascript
$ node 08-url模块.js
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?name=jason&message=helloworld',
  query: 'name=jason&message=helloworld',
  pathname: '/pinglun',
  path: '/pinglun?name=jason&message=helloworld',
  href: '/pinglun?name=jason&message=helloworld' }

```

> **加上第二个参数true之后**

```javascript
var url = require('url');
var obj = url.parse('/pinglun?name=jason&message=helloworld', true)
console.log(obj);
```

```javascript
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?name=jason&message=helloworld',
  query:[Object: null prototype] { name: 'jason', message: 'helloworld' },
  pathname: '/pinglun',
  path: '/pinglun?name=jason&message=helloworld',
  href: '/pinglun?name=jason&message=helloworld' }

```

### 4.3 留言板功能的完善

> 案例照着下面的步骤写：
>
> 1. / index.html
>
> 2. 开放 public 目录中的静态资源
>
>    当请求 /public/xxx 的时候，读取响应 public 目录中的具体资源
>
> 3. /post post.html
>
> 4. /pinglun
>
> ​        4.1 接收表单提交数据
>
> ​	4.2 存储表单提交的数据
>
> ​	4.3 让表单重定向到 
>
> ​		*statusCode*
>
> ​		setHeader

```javascript
// app application 应用程序
// 把当前模块所有的依赖项都声明再文件模块最上面
// 为了让目录结构保持统一清晰，所以我们约定，把所有的 HTML 文件都放到 views（视图） 目录中
// 我们为了方便的统一处理这些静态资源，所以我们约定把所有的静态资源都存放在 public 目录中
// 哪些资源能被用户访问，哪些资源不能被用户访问，我现在可以通过代码来进行非常灵活的控制
// / index.html
// /public 整个 public 目录中的资源都允许被访问
// 前后端融会贯通了，为所欲为

var http = require('http')
var fs = require('fs')
var url = require('url')
var template = require('art-template')

var comments = [
  {
    name: '张三',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三2',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三3',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三4',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三5',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  }
]

// /pinglun?name=的撒的撒&message=的撒的撒的撒
// 对于这种表单提交的请求路径，由于其中具有用户动态填写的内容
// 所以你不可能通过去判断完整的 url 路径来处理这个请求
// 
// 结论：对于我们来讲，其实只需要判定，如果你的请求路径是 /pinglun 的时候，那我就认为你提交表单的请求过来了

http
  .createServer(function (req, res) { // 简写方式，该函数会直接被注册为 server 的 request 请求事件处理函数
    // 使用 url.parse 方法将路径解析为一个方便操作的对象，第二个参数为 true 表示直接将查询字符串转为一个对象（通过 query 属性来访问）
    var parseObj = url.parse(req.url, true)

    // 单独获取不包含查询字符串的路径部分（该路径不包含 ? 之后的内容）
    var pathname = parseObj.pathname

    if (pathname === '/') {
      fs.readFile('./views/index.html', function (err, data) {
        if (err) {
          return res.end('404 Not Found.')
        }
        var htmlStr = template.render(data.toString(), {
          comments: comments
        })
        res.end(htmlStr)
      })
    } else if (pathname === '/post') {
      // 其它的都处理成 404 找不到
      fs.readFile('./views/post.html', function (err, data) {
        if (err) {
          return res.end('404 Not Found.')
        }
        res.end(data)
      })
    } else if (pathname.indexOf('/public/') === 0) {
      // /public/css/main.css
      // /public/js/main.js
      // /public/lib/jquery.js
      // 统一处理：
      //    如果请求路径是以 /public/ 开头的，则我认为你要获取 public 中的某个资源
      //    所以我们就直接可以把请求路径当作文件路径来直接进行读取
      fs.readFile('.' + pathname, function (err, data) {
        if (err) {
          return res.end('404 Not Found.')
        }
        res.end(data)
      })
    } else if (pathname === '/pinglun') {
      // 注意：这个时候无论 /pinglun?xxx 之后是什么，我都不用担心了，因为我的 pathname 是不包含 ? 之后的那个路径
      // 一次请求对应一次响应，响应结束这次请求也就结束了
      // res.end(JSON.stringify(parseObj.query))

      // 我们已经使用 url 模块的 parse 方法把请求路径中的查询字符串给解析成一个对象了
      // 所以接下来要做的就是：
      //    1. 获取表单提交的数据 parseObj.query
      //    2. 将当前时间日期添加到数据对象中，然后存储到数组中
      //    3. 让用户重定向跳转到首页 /
      //       当用户重新请求 / 的时候，我数组中的数据已经发生变化了，所以用户看到的页面也就变了
      var comment = parseObj.query
      comment.dateTime = '2017-11-2 17:11:22'
      comments.unshift(comment)
      // 服务端这个时候已经把数据存储好了，接下来就是让用户重新请求 / 首页，就可以看到最新的留言内容了

      // 如何通过服务器让客户端重定向？
      //    1. 状态码设置为 302 临时重定向
      //        statusCode
      //    2. 在响应头中通过 Location 告诉客户端往哪儿重定向
      //        setHeader
      // 如果客户端发现收到服务器的响应的状态码是 302 就会自动去响应头中找 Location ，然后对该地址发起新的请求
      // 所以你就能看到客户端自动跳转了
      res.statusCode = 302
      res.setHeader('Location', '/')
      res.end()
    } else {
      // 其它的都处理成 404 找不到
      fs.readFile('./views/404.html', function (err, data) {
        if (err) {
          return res.end('404 Not Found.')
        }
        res.end(data)
      })
    }
  })
  .listen(3000, function () {
    console.log('running...')
  })

// Node 不适合从来没有接触过服务端的人学习
// 如果想要真正的学号服务端，还是老牌的 Java、PHP 这些平台
// Node 不是特别适合入门服务端，但不代表 Node 不强大
// Node 很厉害，具有经验的人可以玩儿的非常的牛
// 不适合新手的原因就在于比较偏底层、而且太灵活
// Java、PHP 好入门的原因就在于：这些平台屏蔽了一些底层
// res.redirect('重定向')
```



