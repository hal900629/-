[TOC]

# NodeJs第三天

## 1. 反馈与复习

###1.1 反馈

- 希望老师再推荐一些前端学习的书籍，谢谢！
  - 《JavaScript 高级编程》第3班
  - 学习，解决问题
  - 书本可以更好的系统的整理学过的内容，了解一些细节
  - 《JavaScript 语言精粹》
- seo的资料，嘿嘿
  - 网站运营 SEO
  - SEO 运营专员
  - 百度、Google、搜狗、
- 老师，软件版本的升级是以什么作为理论支持的，为什么跳跃间隙可以这么大？还有，看上了老师的电子图书馆，瞬间好爱学习呀，真的！
  - 软件开发版本里面涉及到软件工程学： 
  - x.x.x
    - 0.0.1
    - 0.0.2
    - 1.1.5
    - 1.9.2
    - 2（新增功能比较多，甚至可能去除了某些功能）.5(加入了新功能).0（修复bug，提升性能）
    - 大版本
    - 一般是这些客户端软件、技术框架开发者比较理解的多
    - 做网站很少涉及到版本的概念，网站的目的就是快
- art-template里面用的语法是jQuery吗， each什么的 我晕了 each,forEach, 遍历的全混了
  - art-template 和 jQuery 一毛钱关系都没有
  - each 是 art-template 的模板语法，专属的
  - {{each 数组}}
  - <li>{{ $value }}</li>
  - {{/each}} 这是 art-template 模板引擎支持的语法，只能在模板字符串中使用
  - $.each(数组, function)
  - $('div').each(function) 一般用于遍历 jQuery 选择器选择到的伪数组实例对象
  - forEach 是 EcmaScript 5 中的一个数组遍历函数，是 JavaScript 原生支持的遍历方法 可以遍历任何可以被遍历的成员
  - jQuery 的 each 方法和 forEach 几乎一致
  - 由于 forEach 是 EcmaScript 5 中的，所以低版本浏览器不支持
- 每一次的复习贼重要 老师很不错 我喜欢
- 在以后的工作中 用到node.js的地方多吗？ 在留言本的案例中 点击发表留言跳转页面的路径是url路径 和之前写的页面跳转写的文件路径还是有点分不清。
  - 技多不压身
  - Node 对于前端来讲是进阶高级前端开发工程师必备的技能
  - 屌丝最容易逆袭的职业
  - 见得东西多了你就不怕了
  - 为所欲为
- 如果从a中调用b中的数据，又从b中调用a中的数据，执行a代码，为什么把b中的执行完后才会执行a，而不是在b调用a的时候a中的代码继续执行
  - a 加载了 b
    - 执行 b 中的代码
    - 同时得到 b 中导出的接口对象：`exports`
    - 执行 b 的过程中发现 b 也在 require a
    - b 就会反过来执行 a
    - a 中又加载 b
    - b 又反过来加载 a
    - 这就是循环加载
    - 如果你一旦出现了这种情况，说明你的思路有问题。
    - jQuery.js （可能不可能出现 jQuery 依赖了 main）
    - main.js 依赖了 jQuery
    - 这个问题是矛盾。
  - b 中也加载了 a
  - 网页中所有的路径其实都是 url 路径，不是文件路径

###1.2 复习

####1.2.1 网站开发模型

- 黑盒子、哑巴
- 写代码让它变得更智能
- 按照你设计好的套路供用户使用

####1.2.2 在 Node 中使用 art-template 模板引擎

- 安装
- 加载
- `template.render()`

####1.2.3 客户端渲染和服务端渲染的区别

- 最少两次请求，发起 ajax 在客户端使用模板引擎渲染
- 客户端拿到的就是服务端已经渲染好的

- 处理留言本案例首页数据列表渲染展示
- 处理留言本案例发表留言功能
  - 路径
  - 设计好的请求路径
  - $GET 直接或查询字符串数据
  - Node 中需要咱们自己动手来解析
    - url.parse()
  - /pinglun?name=jack&message=hello
  - split('?')
  - name=jack&message=hello
  - split('&')
  - name=jack message=hello
  - forEach()
  - name=jack.split('=')
  - 0 key
  - 1 value

#### 1.2.4 掌握如何解析请求路径中的查询字符串

- `url.parse()`

####1.2.5 如何在 Node 中实现服务器重定向

- `header('location')`
  - 301 永久重定向 浏览器会记住
    - a.com b.com
    - a 浏览器不会请求 a 了
    - 直接去跳到 b 了
    - 案例：[新浪](www.sina.com)
  - 302 临时重定向 浏览器不记忆
    - a.com b.com
    - a.com 还会请求 a
    - a 告诉浏览器你往 b

- Node 中的 Console（REPL）使用
  - 主要是用来测试Node中的一些API的

## 2. 模块系统

