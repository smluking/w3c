# Dom
####访问元素样式表
- dom.style.color
- dom.style.fontFamily
- dom.style.cssFloat

###DOM2
####Document类型的变化
- importNode:document.importNode(oldNode);
- defaultView:document.defaultView||document.parentView;
####Node类型变化
- isSupported:document.body.isSupported('HTML','2.0');
####框架的变化
- contentDocument: 指向表示框架内容的文档对象,不支持跨域
```sh
iframe.contentDocument||iframe.contentWindow.document
```
####样式
- 检测浏览器是否支持CSS2定义的能力
```sh
document.implementation.hasFeature('CSS','2.0')
```
- cssText: 访问style属性中的css代码
- length：css属性的数量
- parentRule：css信息的CSSRule对象
```sh
document.body.style.getPropertyValue('position');
```
- getPropertyPriority: 判断是否使用!important
```sh
document.body.style.getPropertyPriority('position');
```
- item(index): 返回给定位置的CSS属性名称,从0开始
- setProperty(propertyName,value,priority): 设置属性值，并加上优先权标志（“important”或者“”）
- removeProperty(propertyName): 从样式表中删除属性
####计算样式
-document.defaultView.getComputedStyle(node, '伪元素字符串？') || domNode.currentstyle
```sh

```

###DOM3
####Node类型变化
  - isSameNode:node1.isSameNode(node2);
  - isEqualNode: node1.isEqualNode(node2);
  - setUserData: document.body.setUserData('name','lulu',function(operation,key,value,src,dest){});
  ```sh
  document.body.setUserData('name','lulu',function(operation,key,value,src,dest){
    //operation:(1:复制;2:导入;3:删除;4:重命名)
    //key:数据键
    //value:数据值
    //src:源节点
    //dest:目标节点
  });
  ```
  - getUserData: document.body.getUserData('name');
####框架的变化

###DOM4



  - Type some Markdown on the left
  - See HTML in the right
  - Magic

Markdown is a lightweight markup language based on the formatting conventions that people naturally use in email.  As [John Gruber] writes on the [Markdown site][df1]

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually* written in Markdown! To get a feel for Markdown's syntax, type some text into the left window and watch the results in the right.

### Version
3.2.7

### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [Marked] - a super fast port of Markdown to JavaScript
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [keymaster.js] - awesome keyboard handler lib by [@thomasfuchs]
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

### Installation

You need Gulp installed globally:

```sh
$ npm i -g gulp
```

```sh
$ git clone [git-repo-url] dillinger
$ cd dillinger
$ npm i -d
$ gulp build --prod
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins

* Dropbox
* Github
* Google Drive
* OneDrive

Readmes, how to use them in your own application can be found here:

* [plugins/dropbox/README.md] [PlDb]
* [plugins/github/README.md] [PlGh]
* [plugins/googledrive/README.md] [PlGd]
* [plugins/onedrive/README.md] [PlOd]

### Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantanously see your updates!

Open your favorite Terminal and run these commands.

First Tab:
```sh
$ node app
```

Second Tab:
```sh
$ gulp watch
```

(optional) Third:
```sh
$ karma start
```

### Docker
Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 80, so change this within the Dockerfile if necessary. When ready, simply use the Dockerfile to build the image. 

```sh
cd dillinger
docker build -t <youruser>/dillinger:latest .
```
This will create the dillinger image and pull in the necessary dependencies. Once done, run the Docker and map the port to whatever you wish on your host. In this example, we simply map port 80 of the host to port 80 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 80:80 --restart="always" <youruser>/dillinger:latest
```

Verify the deployment by navigating to your server address in your preferred browser.

### N|Solid and NGINX

More details coming soon.

#### docker-compose.yml

Change the path for the nginx conf mounting path to your full path, not mine!

### Todos

 - Write Tests
 - Rethink Github Save
 - Add Code Comments
 - Add Night Mode

License
----

MIT


**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)


   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [@thomasfuchs]: <http://twitter.com/thomasfuchs>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [marked]: <https://github.com/chjj/marked>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [keymaster.js]: <https://github.com/madrobby/keymaster>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]:  <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>

