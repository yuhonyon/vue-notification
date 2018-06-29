## @fastweb/notification

** 遗弃 **
 vue通知框

## 安装

```bash
    yarn add @fastweb/notification --dev
```


## 用法

```html
    <notification :infos="[{
                'id': 12121,
                'icon': 'http://oihdlfeky.bkt.clouddn.com/17-4-12/98821747-file_1491981596854_10263.png',
                'title': '预警通知',
                'content': '姓名，性别，年龄，手机号，血糖值3.3，低/高于与预警值，请及关注并时联系！'
            }]" @close="close" @clickHandle="clickHandle"></notification>
```

