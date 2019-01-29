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

```
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

```
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

```
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

