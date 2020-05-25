# 主题引用说明

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

## hexo-theme-fluid