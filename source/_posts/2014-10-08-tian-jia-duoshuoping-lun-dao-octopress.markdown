---
layout: post
title: "添加duoshuo评论到octopress"
date: 2014-10-08 10:25:36 +0800
comments: true
categories: github
---

octopress本身已经有disqus评论,但是在国内加载速度太慢，所以还是以国内的duoshuo评论替代之。

### 主要涉及的文件
下面根目录“/”为octopress/目录  

    /_config.ymlz ：添加多说相关设置变量  
    /source/_layouts/post.html ：为博文页添加duoshuo评论  
    /source/_include/post/duoshuo.html ：duoshuo评论框  
    /source/_include/article.html ：文章上面加一个评论链接  
    /source/_include/asides/duoshuo.html ：duoshuo最新评论显示  
    /source/index.html ：  
    /source/_layouts/page.html ：  
    
  `多说评论框把评论提交到多说服务器，多说最新评论显示从服务器拉取评论以显示在我们的页面上。  
下面一个文件一个文件地添加代码：`
### /_config.ymlz
添加如下几行：  

    duoshuo_short_name: yourname #用你自己duoshuo名（需要去多说网站获取）  
    duoshuo_asides_num: 10      # 侧边栏评论显示条目数  
    duoshuo_asides_avatars: 1   # 侧边栏评论是否显示头像  
    duoshuo_asides_time: 1      # 侧边栏评论是否显示时间  
    duoshuo_asides_title: 1     # 侧边栏评论是否显示标题  
    duoshuo_asides_admin: 1     # 侧边栏评论是否显示作者评论  
    duoshuo_asides_length: 18   # 侧边栏评论截取的长度  
    
### /source/_layouts/post.html
在disqus代码：  

        {% if site.disqus_short_name and page.comments == true %}
        <section>
            <h1>Comments</h1>
            <div id="disqus_thread" aria-live="polite">{% include post/disqus_thread.html %}</div>
        </section>
        {% endif %}

下方添加多说评论框（在文件/source/_include/post/duoshuo.html中实现）：  

    {% if site.duoshuo_short_name and page.comments != false %}
      <section>
        <h3>多说评论：</h3>
        <div id="comments" aria-live="polite">{% include post/duoshuo.html %}</div>
      </section>
   {% endif %}  
   
`上面是为了使rake new_post["..."]产生的文章页面下包含评论框；若要使用rake new_page["..."]产生的页面下也包含评论框，可以在/source/_layouts/page.html做同样添加。`

### /source/_include/post/duoshuo.html
这个文件需要你新建，然后复制粘贴以下代码：  

    <section>
    	<!-- 在octopress/目录的_config.yml中已经定义了duoshuo_short_name -->
    	{% if site.duoshuo_short_name != false %}
    	<!-- 多说评论框 start -->
    	<div class="ds-thread"  data-title="{{ page.title }}" ></div>
    		<!-- 多说评论框 end -->
    		<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    		<script type="text/javascript">
    		var duoshuoQuery = {short_name:"{{ site.duoshuo_short_name }}"};
    			(function() {
    				var ds = document.createElement('script');
    				ds.type = 'text/javascript';ds.async = true;
    				ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
    				ds.charset = 'UTF-8';
    				(document.getElementsByTagName('head')[0] 
    				 || document.getElementsByTagName('body')[0]).appendChild(ds);
    			})();
    		</script>
    		<!-- 多说公共JS代码 end -->
    	{% endif %}
    <section>
    
`上面就是duoshuo评论框模块了。`

### /source/_include/article.html  
在disqus代码：  

        {% if site.disqus_short_name and page.comments != false and post.comments != false and site.disqus_show_comment_count == true %}
         | <a href="{% if index %}{{ root_url }}{{ post.url }}{% endif %}#disqus_thread">Comments</a>
        {% endif %}

下方添加duoshuo代码：  

         {% if site.duoshuo_short_name and page.comments != false %}
          | <a href="{% if index %}{{ root_url }}{{ post.url }}{% endif %}#comments">Comments</a>
         {% endif %}

`这样就在文章上面加上了一个评论链接。`

### /source/_include/asides/duoshuo.html
这个文件需要你新建，然后复制粘贴以下代码：  

    <section>
    <h1>最新评论</h1>
    <ul class="ds-recent-comments" data-num-items="{{ site.duoshuo_asides_num }}" data-show-avatars="{{ site.duoshuo_asides_avatars }}" data-show-time="{{ site.duoshuo_asides_time }}" data-show-title="{{ site.duoshuo_asides_title }}" data-show-admin="{{ site.duoshuo_asides_admin }}" data-excerpt-length="{{ site.duoshuo_asides_length }}"></ul>
    {% if index %}
    <!--多说js加载开始，一个页面只需要加载一次 -->
    <script type="text/javascript">
      var duoshuoQuery = {short_name:"{{ site.duoshuo_short_name }}"};
      (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = 'http://static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
      })();
    </script>
    <!--多说js加载结束，一个页面只需要加载一次 -->
    {% endif %}
    </section>
    
    `上面的代码是为了在侧边栏里显示最新评论，为了把它添加到侧边栏，还需在_config.yml 文件中的 blog_index_asides 行或 page_asides 行或 post_asides 行中添加：`  
    
    asides/duoshuo.html
    
### /source/index.html ：  

### /source/_layouts/page.html ：  
