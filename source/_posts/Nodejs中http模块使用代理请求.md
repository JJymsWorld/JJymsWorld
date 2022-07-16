---
title: Nodejs中http模块使用代理请求
categories:
  - [gallery]
tags:
  - [NodeJs]
date: 2022-07-16 16:53:07
---

代码示例: 

```javascript
const http = require("http");  // 引入内置http模块
const url  = require("url");


// 要访问的目标页面
const targetUrl = "http://dev.kdlapi.com/testproxy";
const urlParsed   = url.parse(targetUrl);

// 代理ip,由快代理提供
const proxyIp = "47.115.36.94";  // 代理服务器ip
const proxyPort = "16816"; // 代理服务器host

// http代理验证信息
const options = {
    host    : proxyIp,
    port    : proxyPort,
    path    : targetUrl,
    method  : "GET",
    headers : {
        "Host"                : urlParsed.hostname,
    }
};

http.request(options,  (res) => {
        console.log("got response: " + res.statusCode);
        // 输出返回内容(使用了gzip压缩)
        if (res.headers['content-encoding'] && res.headers['content-encoding'].indexOf('gzip') != -1) {
            let zlib = require('zlib');
            let unzip = zlib.createGunzip();
            res.pipe(unzip).pipe(process.stdout);
        } else {
            // 输出返回内容(未使用gzip压缩)
            res.pipe(process.stdout);
        }
    })
    .on("error", (err) => {
        console.log(err);
    })
    .end()
;


```

https的要复杂一些:

首先创建HttpsProxyAgent.js文件,写入以下代码.

```javascript
var Util = require('util');
var Https = require('https');
var Tls = require('tls');

/**
 * HTTPS Agent for node.js HTTPS requests via a proxy.
 * blog.vanamco.com/connecting-via-proxy-node-js/
 */
function HttpsProxyAgent(options)
{
    Https.Agent.call(this, options);

    this.proxyHost = options.proxyHost;
    this.proxyPort = options.proxyPort;

    this.createConnection = function(opts, callback)
    {
        // do a CONNECT request
        var req = Http.request({
            host: options.proxyHost,
            port: options.proxyPort,
            method: 'CONNECT',
            path: opts.host + ':' + opts.port,
            headers: {
                host: opts.host
            }
        });

        req.on('connect', function(res, socket, head)
        {
            var cts = Tls.connect(
                {
                    host: opts.host,
                    socket: socket
                },
                function()
                {
                    callback(false, cts);
                }
            );
        });

        req.on('error', function(err)
        {
            callback(err, null);
        });

        req.end();
    }
}

Util.inherits(HttpsProxyAgent, Https.Agent);

// Almost verbatim copy of http.Agent.addRequest
// HttpsProxyAgent.prototype.addRequest = function(req, host, port, localAddress) // node v0.10.x
HttpsProxyAgent.prototype.addRequest = function(req, options)   // node v0.12.x
{
    /* node v0.10.x
    var name = host + ':' + port;
    if (localAddress)
        name += ':' + localAddress;
    */
    var name = options.host + ':' + options.port;
    if (options.path)
        name += ':' + options.path;

    if (!this.sockets[name])
        this.sockets[name] = [];

    if (this.sockets[name].length < this.maxSockets)
    {
        // if we are under maxSockets create a new one.
        // this.createSocket(name, host, port, localAddress, req, function(socket)  // node 0.10.x
        this.createSocket(name, options.host, options.port, options.path, req, function(socket: Net.Socket) // node 0.12.x
        {
            req.onSocket(socket);
        });
    }
    else
    {
        // we are over limit so we'll add it to the queue.
        if (!this.requests[name])
            this.requests[name] = [];
        this.requests[name].push(req);
    }
};

// Almost verbatim copy of http.Agent.createSocket
HttpsProxyAgent.prototype.createSocket = function(name, host, port, localAddress, req, callback)
{
    var self = this;
    var options = Util._extend({}, self.options);
    options.port = port;
    options.host = host;
    options.localAddress = localAddress;

    options.servername = host;
    if (req)
    {
        var hostHeader = req.getHeader('host');
        if (hostHeader)
            options.servername = hostHeader.replace(/:.*$/, '');
    }

    self.createConnection(options, function(err, s)
    {
        if (err)
        {
            err.message += ' while connecting to HTTP(S) proxy server ' + self.proxyHost + ':' + self.proxyPort;

            if (req)
                req.emit('error', err);
            else
                throw err;

            return;
        }

        if (!self.sockets[name])
            self.sockets[name] = [];

        self.sockets[name].push(s);

        var onFree = function()
        {
            self.emit('free', s, host, port, localAddress);
        };

        var onClose = function(err)
        {
            // this is the only place where sockets get removed from the Agent.
            // if you want to remove a socket from the pool, just close it.
            // all socket errors end in a close event anyway.
            self.removeSocket(s, name, host, port, localAddress);
        };

        var onRemove = function()
        {
            // we need this function for cases like HTTP 'upgrade'
            // (defined by WebSockets) where we need to remove a socket from the pool
            // because it'll be locked up indefinitely
            self.removeSocket(s, name, host, port, localAddress);
            s.removeListener('close', onClose);
            s.removeListener('free', onFree);
            s.removeListener('agentRemove', onRemove);
        };

        s.on('free', onFree);
        s.on('close', onClose);
        s.on('agentRemove', onRemove);

        callback(s);
    });
};
```

接着https代理请求示例:

```javascript
var HttpsProxyAgent = require("./HttpsProxyAgent");
var Https = require('https');

var agent = new HttpsProxyAgent({
    proxyHost: '210.42.41.8',
    proxyPort: 8090
});

Https.request(
    {
        host: 'github.com',
        port: 443,
        method: 'GET',
        path: '/',
        agent: agent
    },
    function(res)
    {
        res.on('data', function(data)
        {
            console.log(data.toString());
        });
    }
).end();
```

