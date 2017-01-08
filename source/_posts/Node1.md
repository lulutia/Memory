title: 留档：Node学习脚印一
categories: technology 
tags: [前端,计算机]
---
##资料准备部分
* [Node入门](http://www.nodebeginner.org/index-zh-cn.html)
* 深入浅出Node.js

##Node入门部分
目标：

* 用户通过浏览器使用此应用。
* 当用户请求http://domain/start时，可以看到一个欢迎页面，页面上有一个文件上传的表单。
* 用户可以选择一个图片并提交表单，随后文件将被上传到http://domain/upload，该页面完成上传后会把图片显示在页面上。

模块分析：

* Web页面：HTTP服务器
* 不同的URL：路由
* 请求处理程序
* 上传数据
* 保存展示数据

~~~javascript
//index.js
var server = require("./server");
var router = require("./router");
var requestHandlers = require("./requestHandlers");

var handle = {};
handle["/"] = requestHandlers.start;
handle["/start"] = requestHandlers.start;
handle["/upload"] = requestHandlers.upload;
handle["/show"] = requestHandlers.show;

server.start(router.router, handle);
~~~

~~~javascript
//server.js
var http = require("http");
var url = require("url");

var server = {};
server.start = function(router, handle){
    http.createServer(function(req,res){
        var pathname = url.parse(req.url).pathname;
        console.log("Request for " + pathname + " received");
        router(handle, pathname, res, req);        
    }).listen(8124);

    console.log("Server running at http://127.0.0.1:8124/");
}

module.exports = server;
~~~

~~~javascript
//router.js
var router = {};
router.router = function(handle, pathname, res, req){
    console.log("About to route a request for " + pathname);
    if (typeof handle[pathname] === "function"){
        handle[pathname](res, req);
    }
    else{
        console.log("no router " + pathname);
        res.writeHead(404, {"Content-Type": "text/plain"});
        res.write("404 Not found");
        res.end();
    }
}

module.exports = router;
~~~

~~~javascript
//requestHandlers.js
var querystring = require("querystring");
var fs = require("fs");
var formidable = require("formidable");

var requestHandlers = {};
requestHandlers.start = function(res, req){
    console.log("Request handler 'start' was called.");

      var body = '<html>'+
          '<head>'+
          '<meta http-equiv="Content-Type" '+
          'content="text/html; charset=UTF-8" />'+
          '</head>'+
          '<body>'+
          '<form action="/upload" enctype="multipart/form-data" '+
          'method="post">'+
          '<input type="file" name="upload">'+
          '<input type="submit" value="Upload file" />'+
          '</form>'+
          '</body>'+
          '</html>';

        res.writeHead(200, {"Content-Type": "text/html"});
        res.write(body);
        res.end();
}
requestHandlers.upload = function(res, req){
    console.log("upload");
    var form = new formidable.IncomingForm();
    form.parse(req, function(error, fields, files){
        console.log("parsing done");
        fs.renameSync(files.upload.path, "/tmp/test.png");
        res.writeHead("200", {"Content-Type": "text/html"});
        res.write("received image:<br/>");
        res.write("<img src='/show' />");
        res.end();
    })
}
requestHandlers.show = function(res, postData){
    console.log("SHOW");
    fs.readFile("/tmp/test.png", "binary", function(error, file){
        if(error){
            res.writeHead(500, {"Content-Type": "text/plain"});
            res.write(error + "\n");
            res.end();
        }
        else{
            res.writeHead(200, {"Content-Type": "image/png"});
            res.write(file, "binary");
            res.end();
        }
    })
}
module.exports = requestHandlers;
~~~

##注意点
* 阻塞和非阻塞的问题
