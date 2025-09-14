---
draft: true
---

RSS是一个简易信息聚合，可以帮助人们快速获取最新信息。而RSSHub是一个简单易用开源的RSS生成器，它可以帮助没有RSS的网站生成RSS

### 搭建RSSHub

\[alert\]在RSSHub部署时，需要连接一些大陆内被墙网站，所以不推荐在大陆内的服务器搭建RSSHub\[/alert\]

通过以下命令下载RSSHub

```
git clone https://github.com/DIYgod/RSSHub.git

```

随后进入目录

```
cd RSSHub
```

输入以下命令安装依赖项

```
npm install
```

随后输入以下命令部署

```
npm run build
```

随后你可以直接运行RSSHub

```
npm run start
```

或者可以用nohup后台运行

```
nohup npm run start > 你想设置的输出保存路径 &
```
