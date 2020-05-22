egg爬虫

[git地址](https://github.com/JInann/egg-demo)

发起请求

```javascript
const { data } = await this.ctx.curl('http://www.xbiquge.la/53/53881/22735533.html', {
	dataType: 'text', // json or text
});
```



cheerio  服务端的jquery

```javascript
const $ = cherrio.load(data)
```

fs

```javascript
import fs = require('fs')
```

读文件

```javascript
fs.readFileSync('D:/demo/egg-demo/db/config.json').toString()
```

写文件

```javascript
fs.writeFileSync('D:/demo/egg-demo/db/config.json', JSON.stringify({}));
fs.appendFileSync('D:/demo/egg-demo/db/book2.txt', content);
```

