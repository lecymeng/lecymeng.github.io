# 主题引用说明

## Official 文章 Front-matter
参考 https://hexo.io/zh-cn/docs/front-matter

|              |                                                      |              |
| :----------- | :--------------------------------------------------- | :----------- |
| 参数         | 描述                                                 | 默认值       |
| `layout`     | 布局                                                 |              |
| `title`      | 标题                                                 | 文章的文件名 |
| `date`       | 建立日期                                             | 文件建立日期 |
| `updated`    | 更新日期                                             | 文件更新日期 |
| `comments`   | 开启文章的评论功能                                   | true         |
| `tags`       | 标签（不适用于分页）                                 |              |
| `categories` | 分类（不适用于分页）                                 |              |
| `permalink`  | 覆盖文章网址                                         |              |
| `keywords`   | 仅用于 meta 标签和 Open Graph 的关键词（不推荐使用） |              |

### Sample
```
---
title: Hello World
date: 2013/7/13 20:46:25
---
```

## hexo-theme-matery
### custom theme
replace res
```
themes/hexo-theme-matery/source/medias/banner
themes/hexo-theme-matery/source/medias/featureimages
themes/hexo-theme-matery/source/medias/reward
themes/hexo-theme-matery/source/medias/avatar.png
themes/hexo-theme-matery/source/medias/cover.jpg
themes/hexo-theme-matery/source/medias/logo.png
themes/hexo-theme-matery/source/favicon.png
```

config
```yaml
time:
  enable: true
  year: 2015 # 年份
  month: 11 # 月份
  date: 24 # 日期

indexbtn:
  enable: true
  name: Github
  icon: fab fa-github-alt
  url: https://github.com/lecymeng

socialLink:
  github: https://github.com/lecymeng
  email: lecymeng@outlook.com
  facebook: # https://www.facebook.com/xxx
  twitter: # https://twitter.com/xxx
  qq: 1570682285

reward:
  enable: true
  title: 你的赏识是我前进的动力
  wechat: /medias/reward/wechat.png
  alipay: /medias/reward/alipay.png

copyright:
  enable: false
  minCharNumber: 240 # 至少复制多少个字符就追加版权信息.
  description: 本文章著作权归作者所有，任何形式的转载都请注明出处。

postInfo:
  date: true # 发布日期
  update: false # 更新日期
  wordCount: true # 文章字数统计
  totalCount: false # 站点总文章字数
  min2read: true # 文章阅读时长
  readCount: true # 文章阅读次数

profile:
  avatar: /medias/avatar.png
  career: Software Engineer
  introduction: If you wish to succeed, you should use persistence as your good friend, experience as your reference, prudence as your brother and hope as your sentry.

myProjects:
  enable: false

mySkills:
  enable: true
  data:
    Java:
      background: 'linear-gradient(to right, #FF0066 0%, #FF00CC 100%)'
      percent: 85%
    Android:
      background: 'linear-gradient(to right, #9900FF 0%, #CC66FF 100%)'
      percent: 80%
    Python:

myGallery:
  enable: true
  data:

valine:
  enable: true
  appId: orsSeN9tLirrMORe4CklaXEN-gzGzoHsz
  appKey: wOCnGVsLjb2wsCTDhhMvAPCr

githubLink:
  enable: false
  url: https://github.com/lecymeng
  title: Fork Me

verifyPassword:
  enable: true

googleAnalytics:
  enable: true
  id: UA-85513822-1

# 无文章特色图片时需要显示的文章特色图片.
featureImages:
- ...
```

### root _config.yml
```yaml
# hexo-theme-matery enable: true --> false
highlight:
  enable: false

# hexo-theme-matery per_page: 10 --> 12
index_generator:
  path: ''
  per_page: 12
  order_by: -date

# hexo-theme-matery per_page: 10 --> 12
## Set per_page to 0 to disable pagination
per_page: 12

# hexo-theme-matery config start
# hexo-theme-matery add search
search:
  path: search.xml
  field: post

# hexo-theme-matery add permalink_pinyin
permalink_pinyin:
  enable: true
  separator: '-' # default: '-'

# hexo-theme-matery add prism_plugin
prism_plugin:
  mode: 'preprocess'    # realtime/preprocess
  theme: 'tomorrow'
  line_number: false    # default false
  custom_css:

# hexo-theme-matery add feed
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date

# hexo-theme-matery add githubEmojis
githubEmojis:
  enable: true
  className: github-emoji
  inject: true
  styles:
  customEmojis:
# hexo-theme-matery config end
```

### dependence source
```
source/_data/friends.json
source/about/index.md
source/categories/index.md
source/contact/index.md
source/friends/index.md
source/tags/index.md
```

### 文章 Front-matter
```
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
---
```

```
---
title: typora-vue-theme主题介绍
date: 2018-09-07 09:25:00
author: 赵奇
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: false
mathjax: false
summary: 这是你自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
categories: Markdown
tags:
  - Typora
  - Markdown
---
```


## hexo-theme-fluid
create source/_data/fluid_config.yml
copy themes/hexo-theme-fluid/_config.yml --> source/_data/fluid_config.yml

### custom theme
```
web_analytics:  # 网页访问统计
  enable: false
  baidu:  # 百度统计的Key，参见 https://tongji.baidu.com/sc-web/10000033910/home/site/getjs?siteId=13751376 代码获取中 hm.js? 后边的字符串
  google: UA-85513822-1
```

### 文章 Front-matter
参考 Official 文章 Front-matter