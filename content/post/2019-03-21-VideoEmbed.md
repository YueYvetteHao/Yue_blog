---
title: "How to embed videos on website"
date: 2019-03-21
categories:
- website
tags:
- website
- markdown
- html
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/1rkZsrr.png
comments: true
showSocial: true
---

This blog post is about how to embed video players on webpages hosted by [Hugo](https://gohugo.io/content-management/shortcodes/).
<!--more-->

<!-- toc -->

# YouTube Video Player

YouTube (and vimeo) video embedding is already supported by Hugo's built-in [shortcodes](https://gohugo.io/content-management/shortcodes/#youtube).<br>
Thus, to integrate a YouTube video player on your website is easy, simply add the following line of code in the markdown file:
![](https://i.imgur.com/YqYzj8j.jpg)

The id is copied from the URL of the original YouTube [video](https://www.youtube.com/watch?v=1PQ-qpPVIJo):
```
https://www.youtube.com/watch?v={{< hl-text red >}}1PQ-qpPVIJo{{< /hl-text >}}
```
When calling the shortcode "youtube", the following html codes will be automatically generated for the website:
{{< codeblock "" "markdown" >}}
{{< youtube id="1PQ-qpPVIJo" autoplay="false" >}}
{{< /codeblock >}}
And the embeded video will look like the following:
{{< youtube id="1PQ-qpPVIJo" autoplay="false" >}}


# 优酷视频 Youku

In order to embed video players from a different source, we will need to customize a shortcode and upload it in the {{< hl-text orange >}}/layouts/shortcodes{{< /hl-text >}} folder.<br>
For example, the following html file will enable us to embed Youku video players on the website:
{{< codeblock "youku.html" "xml" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/shortcodes/youku.html">}}
<div class="embed-container">
	<iframe width="560" height="315" src="//player.youku.com/embed/{{ index .Params "id" }}" frameborder="0" allowfullscreen></iframe>
</div>
{{< /codeblock >}}
After uploading the customized shortcode, integrating videos from Youku is similar to adding YouTube videos. We will call the shortcode named "youku" by including the following line of code in the markdown file:
![](https://i.imgur.com/jFPUIcu.jpg)

Again, the video id is copied from the original [URL](https://v.youku.com/v_show/id_XMzY5MTQxNjIwMA==.html):
```
https://v.youku.com/v_show/id_{{< hl-text red >}}XMzY5MTQxNjIwMA=={{< /hl-text >}}.html
```
Then, a video player for Youku is embeded:
{{< youku id="XMzY5MTQxNjIwMA==" autoplay="false" >}}


# 腾讯视频 Tencent

Similarly, we will customize another shortcode for Tencent videos, and upload it to the {{< hl-text orange >}}/layouts/shortcodes{{< /hl-text >}} folder:
{{< codeblock "tencent.html" "xml" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/shortcodes/tencent.html">}}
<div class="embed-container">
	<iframe width="560" height="315" src="https://v.qq.com/iframe/player.html?vid={{ index .Params "id" }}" frameborder="0" allowfullscreen="true"></iframe>
</div>
{{< /codeblock >}}
Then we can embed a Tencent [video](https://v.qq.com/x/page/b0846uzr9sq.html) (`https://v.qq.com/x/page/{{< hl-text red >}}b0846uzr9sq{{< /hl-text >}}.html`) with the following code:
![](https://i.imgur.com/2XQ4sm3.jpg)

Tencent video:
{{< tencent id="b0846uzr9sq" autoplay="false">}}



