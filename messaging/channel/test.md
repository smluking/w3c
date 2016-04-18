```sh 
//MessageChannel对象:
//MessagePort(postMessage().start().close().onmessage属性)
//继承DOM事件接口，属性共享。但是该事件没有冒泡，无法取消，也没有默认行为
// 监听从右侧框架传来的信息
window.addEventListener('message', function(evt) {
    if (evt.origin == 'http://localhost') {
        if ( evt.ports.length > 0 ) {
            // 将端口转移到其他文档
            window.frames[0].postMessage('端口打开','http://localhost', evt.ports);
        }
    }    
}, false);
```

 