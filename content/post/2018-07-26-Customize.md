---
title: "How to host your personal website on Github using Hugo (2/2)"
date: 2018-07-26
categories:
- study_notes
tags:
- website
- github
- markdown
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/D65Isnt.jpg
comments: false
showSocial: false
---

Customize the static website with Hugo.
<!--more-->

In the previous [blog](https://yueyvettehao.github.io/2018/07/how-to-host-your-personal-website-on-github-using-hugo-1/2/), I shared how I built my website. Hugo makes everything easier, to edit the website, I can simply alter the configuration file {{< hl-text primary >}}config.toml{{< /hl-text >}}, and the markdown files in the {{< hl-text orange >}}content{{< /hl-text >}} folder. <br>
Here I will show a few examples of how to customize the website.
<!-- toc -->

# Posts vs pages

The difference between [posts](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md#writing-posts) and [pages](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md#writing-pages) is that blog posts are dated and indexed, while pages are not. To write a page, first create a {{< hl-text orange >}}page{{< /hl-text >}} directory in the {{< hl-text orange >}}content{{< /hl-text >}} folder, then start a new markdown file with the following [front-matter settings](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md#front-matter-settings):
```
---
title: "Title"
comments:       false
showMeta:       false
showActions:    false
---
```

# Customize sidebar

The [sidebar](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md#sidebar) allow users to navigate through the website easily. Edit settings under “Menu Configuration” in the {{< hl-text primary >}}config.toml{{< /hl-text >}} file to alter sidebar items and add [Font Awesome](https://fontawesome.com/) icons. <br>
Index page:
```
# Menu Configuration
[[menu.main]]
  weight = 1
  identifier = "home"
  name = "Home"
  pre = "<i class=\"sidebar-button-icon fa fa-lg fa-home\"></i>"
  url = "/"
```
*About* [page](https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md#writing-pages):
```
[[menu.main]]
  weight = 2
  identifier = "about"
  name = "About"
  pre = "<i class=\"sidebar-button-icon fa fa-lg fa-smile-o\"></i>"
  url = "/page/about"
```
Sort posts by category:
```
[[menu.main]]
  weight = 4
  identifier = "categories"
  name = "Categories"
  pre = "<i class=\"sidebar-button-icon fa fa-lg fa-bookmark\"></i>"
  url = "/categories"
```
Index all the blog posts in *Chinese*:
```
[[menu.main]]
  weight = 7
  identifier = "chinese"
  name = "中文"
  pre = "<i class=\"sidebar-button-icon fa fa-lg fa-language\"></i>"
  url = "/tags/Chinese/"
```
Add links to social media:
```
[[menu.links]]
  weight = 1
  identifier = "google"
  name = "Google Scholar"
  pre = "<i class=\"sidebar-button-icon fa fa-lg fa-graduation-cap\"></i>"
  url = "https://scholar.google.com/citations?user=UoI3bSQAAAAJ&hl=en"
```


# Customize cover image

First upload the image to the "cloud" (imgur, Google Photo, etc.) and obtain a URL.
Then edit the {{< hl-text primary >}}config.toml{{< /hl-text >}} file:
```
 coverImage = "https://i.imgur.com/CgrW7fU.jpg"
```

# "Pinned" post?

I like the style of the home page provided by the theme. However, I wanted to “pin” a welcome message at the top. I think the correct method is to edit the layout. But I haven't figured out how to do that... <br>
So I simply created a post that dates in the future. Since the blog previews are ordered by time, this post will always be on top. :) 
```
---
title: "Welcome! 欢迎"
date: 2028-04-23
comments:       false
showMeta:       false
showActions:    false
---
```
The disadvantage of doing this is the welcome post will be indexed and will show up in archives and categories.
