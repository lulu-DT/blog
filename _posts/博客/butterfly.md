# Butterfly

> 说明：
>
> 配置文件有两个，一个是根目录下的`_config.yml`，另一个是butterfly仓库下的`_config.yml`
>
> 以下简称utterfly仓库下的_config.yml为`主题配置`，根目录下的为`配置`

## 快速开始

### 安装

```shell
# 在hexo根目录
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

### 修改配置

```yaml
# 将主题更换为butterfly
theme: butterfly
```

### 安装插件

```shell
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

## Front-matter

### Page Front-matter

```markdown
---
title:
date:
updated:
type:
comments:
description:
keywords:
top_img:
mathjax:
katex:
aside:
aplayer:
highlight_shrink:
random:
---
```



|       标签       |                    功能                    |
| :--------------: | :----------------------------------------: |
|      title       |              （必填）页面标题              |
|       date       |              （必填）创建日期              |
|       type       | （必填）标签、分类、友情链三个页面需要配置 |
|     updated      |            （选填）页面更新日期            |
|   description    |              （选填）页面描述              |
|     keywords     |               （选填）关键字               |
|     comments     |              （选填）页面评论              |
|     top_img      |            （选填）页面顶部图片            |
|     mathjax      |      （选填）mathjax开关（默认false）      |
|      katex       |       （选填）katex开关（默认false）       |
|      aside       |               （选填）侧边栏               |
| highlight_shrink |           （选填）代码块是否展开           |
|      random      | （选填）配置友情链接是否排序（默认false）  |

### Post Front-matter

```markdown
---
title:
date:
updated:
tags:
categories:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside:
abcjs:
---
```



|         标签          |              功能              |
| :-------------------: | :----------------------------: |
|         title         |        （必填）文章标题        |
|         date          |        （必填）创建日期        |
|        updated        |      （选填）文章更新日期      |
|         tags          |        （选填）文章标签        |
|      categories       |          （选填）分类          |
|       keywords        |         （选填）关键字         |
|      description      |          （选填）描述          |
|        top_img        |        （选填）顶部图片        |
|         cover         |         （选填）缩略图         |
|       comments        | （选填）文章评论模块，默认true |
|          toc          |      （选填）显示文章toc       |
|      toc_number       |     （选填）显示toc_number     |
|   toc_style_simple    |    （选填）显示toc简洁模式     |
|       copyright       |        （选填）复制权限        |
|   copyright_author    |          （选填）作者          |
| copyright_author_href |        （选填）作者链接        |
|     copyright_url     |        （选填）文章链接        |
|    copyright_info     |        （选填）版权声明        |
|        mathjax        | （选填）启用mathjax，默认false |
|         katex         |  （选填）启用katex，默认false  |
|        aplayer        |            （选填）            |
|   highlight_shrink    |     （选填）代码框是否展开     |
|         aside         | （选填）显示侧边栏（默认true） |
|         abcjs         |            （选填）            |

## 创建Page

### 创建标签页

```shell
# 切换到根目录
hexo new page tags
```

修改`source/tags/index.md`文件

```yaml
---
title: 標籤
date: 2018-01-05 00:00:00
type: "tags"
orderby: random
order: 1
---
```

### 创建友链页

```shell
# 切换到根目录
hexo new page link
```

修改`source/link/index.md`文件

```yaml
---
title: 友情鏈接
date: 2018-06-07 22:17:49
type: "link"
---
```

在Hexo博客目錄中的 source/_data（如果沒有 _data 文件夾，請自行創建），創建一個文件 link.yml

```yaml
- class_name: 友情鏈接
  class_desc: 那些人，那些事
  link_list:
    - name: Hexo
      link: https://hexo.io/zh-tw/
      avatar: https://d33wubrfki0l68.cloudfront.net/6657ba50e702d84afb32fe846bed54fba1a77add/827ae/logo.svg
      descr: 快速、簡單且強大的網誌框架

- class_name: 網站
  class_desc: 值得推薦的網站
  link_list:
    - name: Youtube
      link: https://www.youtube.com/
      avatar: https://i.loli.net/2020/05/14/9ZkGg8v3azHJfM1.png
      descr: 視頻網站
    - name: Weibo
      link: https://www.weibo.com/
      avatar: https://i.loli.net/2020/05/14/TLJBum386vcnI1P.png
      descr: 中國最大社交分享平台
    - name: Twitter
      link: https://twitter.com/
      avatar: https://i.loli.net/2020/05/14/5VyHPQqR6LWF39a.png
      descr: 社交分享平台
```

## 搭建图库

