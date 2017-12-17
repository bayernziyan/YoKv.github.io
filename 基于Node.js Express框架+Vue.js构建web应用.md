---
title: 基于Node.js Express框架 + Vue.js构建web应用
date: 2017-07-28 19:39:15
categories:
- 全栈之路
---



## 环境

### 介绍
手头上有一个需求，需要一个简单的web项目，非常适合部署成微服务。考虑使用Node.js的Express框架作为后端，Vue.js作为前端框架。

<!--more-->
遇到的问题是前后端分离，需要部署两套服务，背离了项目的初衷。最后发现npm官方提供了一个将两者结合的库*express-vue*。把vue服务端口渲染和express 的res.render()结合起来，在一个node服务器上实现了前后端。

官方的两个demo：

* [express-vue-super-simple](https://github.com/express-vue/express-vue-super-simple)
* [express-vue-example](https://github.com/express-vue/express-vue-example)

### 安装
* 安装Node.js
* 安装npm

package.json:
```json
{
  "name": "personalpan",
  "version": "0.0.1",
  "description": "express-vue qiniu",

  "dependencies": {
    "express": "^4.15.2",
    "express-vue": "^3.14.2",
    "qiniu": "^7.0.5"
  }
}
```

```
npm install
```


## 相关代码

### 服务端
*express 的后端代码：*

```js
const path = require('path');
const express = require('express');
const expressVue = require('express-vue');
const qiniu = require('qiniu');
const app = express();

app.engine('vue', expressVue);
app.set('view engine', 'vue');
app.set('views', path.join(__dirname, '/views'));
app.set('vue', {
    componentsDir: __dirname + '/views/components'
});

var scope = {
    vue: {
        head: {
            title: '小仓库'
        }
    }
};
app.get('/', function (req, res) {
    res.render('index', scope);
});

app.get('/login', function (req, res) {
    var code = req.query.code;

    if (typeof code === 'undefined') {
        res.render('login', scope);
    } else if (code === 'xxx') {
        var accessKey = 'xxx';
        var secretKey = 'xxx';
        var mac = new qiniu.auth.digest.Mac(accessKey, secretKey);
        var options = {
            scope:  "file-data",
        };
        var putPolicy = new qiniu.rs.PutPolicy(options);
        var uploadToken = putPolicy.uploadToken(mac);
        var data = {
            data: {
           /*     bucketKey: 'file-data',*/
                uploadToken: uploadToken
            }
        };

        res.render('file', data);
    } else {
        res.render('index', scope);
    }

});

app.listen(3333);
console.log('Express server listening on port 3333');

```

### 前端
*相对应的几个vue页面：*
*layout.vue:*
```
<template>
    <!DOCTYPE html>
    <html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <script src="https://unpkg.com/vue/dist/vue.js"></script>
    </head>
    <body class="body">

        {{{app}}}
        {{{script}}}

    </body>
    </html>
</template>

<script>
</script>

<style lang="css">
</style>
```
*index.vue:*
```
<template lang="html">
    <div>
       <a href="/login">登陆</a>


    </div>
</template>

<script>
export default {
    data: function() {
        return {
        }
    }
}
</script>

<style lang="css">
</style>
```

*login.vue:*
```
<template lang="html">
    <div>
       <form>
            <input name="code"/>
             <input type="submit"  value="确定"/>
       </form>

    </div>
</template>

<script>
export default {
    data: function() {
        return {
        }
    }
}
</script>

<style lang="css">
</style>
```
*file.vue:*
```
<template lang="html">
    <div>
    <form method="post" action="http://upload.qiniu.com/"
     enctype="multipart/form-data">
<!--      <input name="key" type="hidden" v-bind:value="bucketKey" />-->
<!--      <input name="x:<custom_name>" type="hidden" value="<custom_value>">-->
      <input name="token" type="hidden" v-bind:value="uploadToken" />
      <input name="file" type="file" />
      <!--<input name="crc32" type="hidden" />-->
<!--      <input name="accept" type="hidden" />-->
      <input type="submit"  value="确定"/>
    </form>


    </div>
</template>

<script>
export default {
    data: function() {
        return {
        }
    }
}
</script>

<style lang="css">
</style>
```

## 总结

项目使用了express-vue 的封装，后端代码都在index.js上，前端代码在.vue页面上，简单粗暴地实现了一个上传到七牛云的服务。第一次使用node.js 目的已经达到，后期学习相关代码分层相关知识。

重构时的思路是把业务代码和路由从index.js分离，解耦，index.js起分发作用，代码组织策略有待修改。

遇到的坑点，node.js项目部署在阿里云服务器上，退出终端，node.js项目就被关闭了。使用PM2解决。