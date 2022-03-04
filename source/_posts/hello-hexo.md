---
title: Hello Hexo
date: 2019-03-07 12:00:45
tags: 技术
categories: 工具
---

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

> hexo 基于node.js的一个快速搭建博客的框架,我们只需要选择喜欢的theme和编写markdown即可搭建,且不需要服务器,借助github的服务器即可搭建的框架。

## 使用步骤

1. hexo安装与简单操作：[hexo入门教程](https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.htm)

2. 使用总结

```
1. 如何本地部署预览并发布上线?
在hexo目录,git bash以下命令即可
hexo s -g #生成并本地预览
hexo d -g #生成并上传

2. 如何选择主题调整样式?

这里用的主题是hexo-theme-next(推荐) : 
https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/README.md

官方文档：
https://theme-next.iissnan.com/getting-started.html#avatar-setting

调整样式：
hexo根目录_config.yml 选择主题
在themes文件价下选择对应主题的_config.yml 修改样式

本人改过的有：打赏图片,favicon(https://www.logosc.cn/logo/favicon?s=%E9%BE%84%E6%99%8B),backgroud

3. 如何编写内容?
cd source/_posts
vi Docker.md

文件开始添加以下内容,hexo会自动给你整理
---
title: Docker
date: 2021-03-07 12:00:45
tags:
categories: 工具
---

```

3. 常出现的问题

```
1. hexo与node版本不兼容问题
http://localhost:4000/tags/%20
%20是空格,新版的hexo无空格,在theme中的config.yml修改配置即可

https://www.zhihu.com/question/353097489/answer/888107103

hexo和node都升级到最近版本即可
https://novnan.github.io/Hexo/update_hexo/
2. 若出现莫名其妙的问题请先 
hexo clean
hexo g  -- generate 
hexo s  -- start dev
hexo d  -- deploy prod
```