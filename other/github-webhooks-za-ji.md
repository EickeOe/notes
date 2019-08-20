# GitHub Webhooks杂记

我的blog的文章是使用gitbook关联github仓库，用gitbook写文章，然后push到github。这就产生了问题，怎么把文章关联到我的blog呢？这就需要使用GitHub的 Webhooks了。

## GitHub  Webhooks

Webhooks是GitHub提供的一个API。Webhooks可以在GitHub仓库\(repositories\)发生事件\(比如提交代码,创建分支，发布版本\)时，通知到其他服务器。官方介绍点[这里](https://help.github.com/en/articles/about-webhooks)

### 用途

Webhooks可以用来触发CI，更新备份，更新生产环境等。

## 怎么使用GitHub  Webhooks？

我们需要创建一个web服务器来接收Webhooks的请求，这里我使用nodejs来搭建：

```javascript
const polka = require('polka');
const crypto = require('crypto');
const { json } = require('body-parser');

polka()
.use(json())
.post('/github-webhooks', (req, res) => {
    res.writeHead(200);
    res.end('webhooks success');
})
.listen(4000, err => {
    if (err) throw err;
    console.log(`> Running on localhost:4000`);
});
```

{% hint style="info" %}
polka是一个很小的http服务器，这里当成express来使用就可以了
{% endhint %}

很简单的一个例子，接收`post`请求，返回'webhooks success'，具体业务就不写啦。

接下来，需要打开我们的GitHub仓库界面，点这里的`Settings`:

![](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g4zd7l78csj30rz0o4ju1.jpg)

然后再点左侧栏的`Webhooks`,右边有`Add werbhook`按钮,点击增加`Webhook`:

![](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g4zdb3jqicj30s40dat9s.jpg)

`Payload URL`中输入我们的接口地址

`Content type`选择`application/json`

`Secret`是用来做加密验证的,可以随便填一些字符串,我们这里推荐填随机生成的字符串.也就是当GitHub访问我们的接口时会带上签名,我们的服务器需要进行验证,验证通过才能执行业务逻辑

![add webhook](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g4zdcv9thyj30rz0ly0uf.jpg)

输入完成后,点击下面的绿色`Add webpack`按钮,GitHub会ping我们的接口,测试我们的接口能不能正常接收请求.

这个页面下面就出现一条ping的请求，可以看到，我们的例子接口是可以正常请求的。

![](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g4zd8xk4p1j30rs0rmwgl.jpg)

### Secret签名验证怎么做？

我们在GitHub上输入Secret后，需要在服务器中去验证签名，GitHub签名是使用 HMAC 来做加密验证的，我们需要把GitHub的hook传递过来的request.body转成字符串，然后去进行加密，加密后得到的字符串去跟请求头中的\`x-hub-signature\`参数做比较，如果一致则验证通过。下面是nodejs的代码实现：

```javascript
const polka = require('polka');
const crypto = require('crypto');
const crypto = require('crypto');
const { json } = require('body-parser');

const SECRET_TOKEN = '在Secret中输入的字符串'

polka()
.use(json())
.post('/github-webhooks', (req, res) => {
  const reg = /^sha1=/;
  let sha1 = req.headers['x-hub-signature'];
  if (reg.test(sha1)) {
  
    sha1 = sha1.replace(reg, '');
  
    const payload = JSON.stringify(req.body);
  
    const hash = crypto.createHmac('sha1', SECRET_TOKEN).update(payload);
  
    const token = hash.digest('hex');
  
    if (token !== sha1) {
      res.writeHead(401)
      res.end('unauthorized');
      return;
    }
    res.writeHead(200);
    res.end('webhooks success');
    return;
  } else {
    res.writeHead(401)
    res.end('unauthorized');
    return;
  }
})
.listen(4000, err => {
    if (err) throw err;
    console.log(`> Running on localhost:4000`);
});
```

这样，加密签名验证就完成啦。



