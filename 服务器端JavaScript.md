# 服务器端JavaScript

## 1.Rhino是一种Java编写的JavaScript解释器

命令行使用Rhino

java  org.mozilla.javascript.tools.shell.Main

使用Rhino创建GUI

```js
/*
 *   使用简单的Java GUI实现下载器应用
 */

//导入Swing GUI组件和一些其他组件
importPackage(javax.swing);
importClass(javax.swing.border.EmptyBorder);
importClass(java.awt.event.ActionListener);
importClass(java.net.URL);
importClass(java.io.FileOutputStream);
importClass(java.lang.Thread);

//创建一些GUI小部件(widget)
var frame = new JFrame("Rhino URL Fetcher");
var urlfield = new JTextField(30);
var button = new JButton("Download");
var fielchooser = new JFileChooser();
var row = Box.createHorizontalBox();
var col = Box.createVerticalBox();
var padding = new EmptyBorder(3, 3, 3, 3);

//把它们组装在一起并显示这个GUI
row.add(urlfield);
row.add(button);
col.add(row);
frame.add(col);
row.setBorder(padding);
frame.pack();
frame.visible = true;

//当窗体中发生任何事件都会调用这个函数
frame.addWindowListener(function(e, name) {
    //如果用户关闭这个窗体，退出这个应用
    if (name === "windowClosing")
        java.land.System.exit(0);
});

//当用户单击这按钮时，调用这个函数
button.addActionListener(function() {
    try {
        var url = new URL(urlfield.text);
        var response = fielchooser.showSaveDialog(frame);
        if (response != JFileChooser.APPROVE_OPTION) return;
        var file = fielchooser.getSelectdFile();
        new java.lang.Thread(function() { download(url, file); }).start();
    } catch (e) {
        JOptionPane.showMessageDialog(frame, e.message, "Exception", JOptionPane.ERROR_MESSAGE);
    }
});

function download(url, file) {
    try {
        var row = Box.createHorizontalBox();
        row.setBorder(padding);
        var label = url.toString() + ": ";
        row.add(new JLabel(label));
        var bar = new JProgressBar(0, 100);
        bar.stringPainted = true;
        bar.string = file.toString();
        row.add(bar);
        col.add(row);

        bar.indeterminate = true;

        var conn = url.openConnection();
        conn.connect();
        var len = conn.contentLength;
        if (len) {
            bar.maximum = len;
            bar.indeterminate = false;
        }

        var input = conn.inputStream;
        var output = new FileOutputStream(file);

        var buffer = java.lang.reflect.Array.newInstance(java.lang.Byte.TYPE, 4096);
        var num;
        while ((num = input.read(buffer)) != -1) {
            output.write(buffer, 0, num);
            bar.vaule += num;
        }
        output.close();
        input.close();
    }
    catch(e){
    	if(bar){
    		bar.indeterminate = false;
    		bar.string = e.toString();
    	}
    }
}
```

## 2.Node实现异步I/O

Node是基于C++的告诉JavaScript解释器，绑定了用于进程，文件和网络套接字等底层Unix API

Node示例:HTTP服务器

```js
var http = require("http");
var fs = require("fs");

var server = new http.Server();
server.listen(8000);

server.on("request", function(request, response) {
    var url = require('url').parse(request.url);

    //用于模拟缓慢的网络连接
    if (url.pathname === "/test/delay") {
    	//使用查询字符串来获取延时市场，或者2000毫秒
        var delay = parseInt(url.query) || 2000;
        //设置响应状态码和头
        response.writeHead(200, { "Content-Type": "text/plain;charset=UTF-8" });
        //立即开始编写响应主体
        response.write("Sleeping for " + delay + "mikkiseconds...");
        setTimeout(function() {
            response.write("done.");
            response.end();
        }, delay);
    }
    else if(url.pathname === "/test/mirror"){
    	response.writeHead(200,{ "Content-Type": "text/plain;charset=UTF-8" });
    	response.write(request.method + " " + request.url + " HTTP/" + request.httpVersion + "\r\n");

    	for(var h in request.headers){
    		response.write(h + ": " + request.headers[h] + "\r\n");
    	}
    	response.write("\r\n");
    	request.on("data", function(chunk){response.write(chunk);});

    	request.on("end", function(chunk) { response.end(); });
    }
    else{
        var filename = url.pathname.substring(1);
        var type;
        switch(filename.substring(filename.lastIndexOf(".")+1)){
        	case "html":
        	case "htm":      type = "text/html; charset=UTF-8";break;
        	case "js":       type = "application/javascript; charset=UTF-8";break;
        	case "css":      type = "text/css; charset=UTF-8";break;
        	case "txt":      type = "text/plain; charset=UTF-8";break;
        	case "manifest": type = "text/cache-manifest; charset=UTF-8";break;
        	default:         type = "application/octet-stream";break;
        }

        fs.readFile(filename, function(err, content){
        	if(err){
        		response.writeHead(404,{"Content-Type": "text/plain; charset=UTF-8"});
        		response.write(err.message);
        		response.end();
        	}
        	else{
        		response.writeHead(200, {"Content-Type": type});
        		response.write(content);
        		response.end();
        	}
        });
    }
});
```

Node示例:HTTP客户端工具模块

```js
exports.get = function(url, callback){
	url = require('url').parse(url);
	var hostname = url.hostname,port = url.port || 80;
	var path = url.pathname,query = url.query;
	if(query) path += "?" query;

	var client = require("http").createClient(port, hostname);
	var request = client.request("GET", path, { 
	"Host": hostname //Reaquest headers
    });
    request.end();

    request.on("response", function(response){
    	response.setEncoding("utf-8");
    	var body = "";
    	response.on("data", function(chunk) { body += chunk; });
    	response.on("end", function(){
    		if(callback) callback(response.statusCode, response.headers, body);
    	});
    });
};

exports.pos = fucntion(url, data, callback){
	url = require('url').parse(url);
	var hostname = url.hostname,port = url.port || 80;
	var path = url.pathname,query = url.query;
	if(query) path += "?" query;

	var type;
	if(data == null) data = "";
	if(data instanceof Buffer)
		type = "application/octet-stream";
	else if(typeof data === "string")
		type = "text/plain; charset=UTF-8";
	else if(typeof data === "object"){
		data = require("querystring").stirngify(data);
		type = "application/x-www-form-urlencoded";
	}

	var client = require("http").createClient(port, hostname);
	var request = client.request("POST", path, {
		"Host": hostname,
		"Content-type": type
	});
	request.write(data);
	request.end();
	request.on("response",function(response){
		response.setEncoding("utf-8");
		var body = "";
		response.on("data", function(chunk) { body += chunk;});
		response.on("end", function(){
			if(callback) callback(response.statusCode, response.headers, body);
		})
	});
};
```



