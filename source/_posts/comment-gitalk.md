---
title: hexo使用gitalk
date: 2018-08-02 15:26:13
tags: gitalk
categories: 工具
---
## hexo评论插件gitalk

在博客上增加了一个评论插件，gitalk. 基于github的issue做的评论
原理就是github以app授权的方式让博客拿到repo的权限，页面在加载时，提示用户登录github，
对某个issue进行评论列表加载、评论回复、点赞等操作。


More info: [gitalk](https://gitalk.github.io/)

### 在github上申请应用授权 
[授权地址](https://github.com/settings/applications/new)

![授权页面](/blog/resource/img/auth.png)


Application name－－应用名称,填写自己可以随意增加issue的repo名字

Homepage URL－－如上图可以填本地地址进行测试,127.0.0.1:port/xx/xx

Application description－－应用描述,随意填

Authorization callback URL－－发布到线上的博客地址,github在验证登录后会回调返回登录授权的access_token，后续评论、点赞都是通过access_token去取github某个issue的数据

### 配置hexo的文件themes/next/layout/_third-party/comments/gitalk.swig

```
{% if page.comments && theme.gitalk.enable %}
  <link rel="stylesheet" href="/blog/css/gitalk.css">

  <script src="/blog/js/gitalk.min.js"></script>
   <script type="text/javascript">
        var gitalk = new Gitalk({
          clientID: '{{ theme.gitalk.ClientID }}',
          clientSecret: '{{ theme.gitalk.ClientSecret }}',
          repo: '{{ theme.gitalk.repo }}',
          owner: '{{ theme.gitalk.githubID }}',
          admin: ['{{ theme.gitalk.adminUser }}'],
          id: location.pathname,
          distractionFreeMode: '{{ theme.gitalk.distractionFreeMode }}'
        })
        gitalk.render('gitalk-container')
       </script>
{% endif %}

```

实际就是调用gitalk封装的js初始化github的issue

### 配置themes/next/_config.yml 

最后一行后添加ClientID 和ClientSecret的配置

```
gitalk:
  enable: true
  githubID: nameNotNull
  repo: blog
  owner: nameNotNull
  admin: nameNotNull
  ClientID: xx
  ClientSecret: xxx
  adminUser: nameNotNull
  distractionFreeMode: true
```
### 配置themes/next/layout/_partials/comments.swig 

最后一个endif前添加评论页面样式

```
  
  {% elseif theme.valine.appid and theme.valine.appkey %}
    <div class="comments" id="comments">
    </div>

  {% elseif theme.gitalk.enable %}
       <div id="gitalk-container"></div>

  {% endif %}



{% endif %}
```

### 配置themes/next/source/css/_common/components/third-party/third-party.styl

引入gitalk的配置，最后一行添加

```
@import "gitalk";

```

至此，则将gitalk的js加载到页面，用户点击文章详情页，默认唤起github的授权登录，js跳转授权页面，成功登录后即可发布评论（实际是发布issue :smile:）
