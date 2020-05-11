---
title: "How to add a calendar to Hugo website"
date: 2020-05-07T17:15:22.000Z
archive: ["2020","2020-05","2020-05-07"]
categories:
- blog
tags:
- website
- markdown
- html
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/VXhGlJM.png
comments: true
showSocial: true
---

Today I added a calendar to my website, and I was inspired by these two blog posts: [Activity Calendar Partial](https://gohugohq.com/partials/activity-calendar-posts/) and [Generate Yearly and Monthly Archive Pages with Hugo Sections](https://blog.atj.me/2017/10/generate-yearly-and-monthly-archive-pages-with-hugo-sections/).

<!--more-->

<!-- toc -->

# Partials file structure

Here are the partial files I created in the {{< hl-text orange >}}layouts/partials/{{< /hl-text >}} folder and they were organized in the following structure.

```
|-- layouts
|  |-- partials
|  |  |-- archy.html
|  |  |-- archm.html
|  |  |-- archd.html
|  |  |-- calendar.html
|  |  |-- calendar
|  |  |  |-- year.html
|  |  |  |-- month.html
|  |  |  |-- day.html
|  |  |-- post
|  |  |  |-- archive.html
```
{{< hl-text primary >}}calendar.html{{< /hl-text >}}, {{< hl-text primary >}}year.html{{< /hl-text >}}, {{< hl-text primary >}}month.html{{< /hl-text >}}, {{< hl-text primary >}}day.html{{< /hl-text >}} files were downloaded from the [Activity Calendar](https://gohugohq.com/partials/activity-calendar-posts/) blog post. However, I modified the  {{< hl-text primary >}}calendar.html{{< /hl-text >}} file slightly, changing `.GroupByPublishDate` to `.GroupByDate`:
```
{{ range ($pages.GroupByPublishDate "2006") }}
{{ range ($pages.GroupByPublishDate "2006-01") }}
{{ range ($pages.GroupByPublishDate "2006-01-02") }}
# Change to .GroupByDate
```

The code for three types of listing (by year, month or day) were partly adapted from templates provided in [Generate Yearly and Monthly Archive Pages with Hugo Sections](https://blog.atj.me/2017/10/generate-yearly-and-monthly-archive-pages-with-hugo-sections/).
{{< codeblock "archy.html "  "html" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/partials/archy.html">}}
{{ $year := .Date.Format "2006" }}
<h2>&emsp;Archive for {{ $year }}</h2>
{{ range where .Site.Pages "Type" "post" }}
    {{ if eq (.Date.Format "2006") $year }}
        {{ partial "post/archive" . }}
    {{ end }}
{{ end }}
{{< /codeblock >}}
{{< codeblock "archm.html "  "html" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/partials/archm.html">}}
{{ $year := .Date.Format "2006" }}
{{ $month := .Date.Format "January" }}
<h2>&emsp;Archive for {{ $month }} {{ $year }}</h2>
{{ range where .Site.Pages "Type" "post" }}
	{{ if and (eq (.Date.Format "2006") $year) (eq (.Date.Format "January") $month) }}
		{{ partial "post/archive" . }}
	{{ end }}
{{ end }}
{{< /codeblock >}}
{{< codeblock "archd.html "  "html" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/partials/archd.html">}}
{{ $year := .Date.Format "2006" }}
{{ $month := .Date.Format "January" }}
{{ $day := .Date.Format "02" }}
<h2>&emsp;Archive for {{ $month }} {{ $day }} {{ $year }}</h2>
{{ range where .Site.Pages "Type" "post" }}
  	{{ if and (eq (.Date.Format "2006") $year) (eq (.Date.Format "January") $month) (eq (.Date.Format "02") $day)}}
    	{{ partial "post/archive" . }}
  	{{ end }}
{{ end }}
{{< /codeblock >}}
{{< codeblock "/layouts/partials/post/archive.html "  "html" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/partials/post/archive.html">}}
{{ $year := .Date.Format "2006" }}
{{ $month := .Date.Format "January" }}
<section class="boxes">
	<div class="archive archive-year box" data-date="{{ $year }}">
		<ul class="archive-posts archive-month" data-date="{{ $year }}{{ $month }}">
			<li class="archive-post archive-day">
     			<a href="{{ .Permalink }}">{{ .Title }}</a>
     			<span class="post-date">{{ .Date.Format "02 Jan 2006" }}</span>
			</li>
		</ul>
	</div>
</section>
{{< /codeblock >}}

# Taxonomy structure

The idea is to archive blog posts by date and users can click on the date on the calendar to go to a specific post. In order for this to work, I added a new way of grouping the posts for this website (aka "[Taxonomy](https://gohugo.io/templates/taxonomy-templates/)"). <br>

In the configuration file {{< hl-text primary >}}config.toml{{< /hl-text >}}, add:
```
# prevent hugo from creating folders/indexes for each section
disableKinds = ["section"]

# adding taxonomy term 'archive'
[taxonomies]
    archive = "archive"
```

Then I created a layout file {{< hl-text primary >}}archive.html{{< /hl-text >}} to {{< hl-text orange >}}layouts/taxonomy/{{< /hl-text >}} folder.
{{< codeblock "/layouts/taxonomy/archive.html "  "html" "https://github.com/YueYvetteHao/Yue_blog/blob/master/layouts/taxonomy/archive.html">}}
{{ partial "head.html" . }}
  <body>
    <div id="blog">
      {{ partial "header.html" . }}
      {{ partial "sidebar.html" . }}
      <div id="main" data-behavior="{{ .Scratch.Get "sidebarBehavior" }}"
        class="{{ with .Params.coverImage }}hasCover{{ end }}
               {{ if eq .Params.coverMeta "out" }}hasCoverMetaOut{{ else }}hasCoverMetaIn{{ end }}
               {{ with .Params.coverCaption }}hasCoverCaption{{ end }}">

        {{ $year := .Date.Format "2006" }}
        {{ $currentTaxonomy := index (last 1 (split (delimit (split .URL "/") "," "") ",")) 0 }}
        {{ $regex := index (findRE "-" $currentTaxonomy 2) 1 }}
    
        {{ if eq $currentTaxonomy $year }} <!-- archive by year -->
            {{ partial "archy.html" . }}
        {{ else if eq $regex "-" }} <!-- archive by day -->
            {{ partial "archd.html" . }}
        {{ else }} <!-- archive by month -->
            {{ partial "archm.html" . }}
        {{ end }}
	
      </div>
    </div>
  </body>
{{ partial "foot.html" . }}
{{< /codeblock >}}

Specific date format and the archive variable is required in the front matter of markdown files:
```
# examplepost.md
---
date: 2020-05-07T17:15:22.000Z
archive: ["2020","2020-05","2020-05-07"]
---
```


# Render calendar style

I compiled the [styling.scss](https://gohugohq.com/partials/activity-calendar-posts/) file to css format at [sassmeister.com](https://www.sassmeister.com/), then added the css file to the {{< hl-text orange >}}statics/css{{< /hl-text >}} folder. The calendar style (font size, color, etc.) can be altered by editing the css file {{< hl-text primary >}}/static/css/style.css{{< /hl-text >}}. <br>
For hugo to reconize customized css files, toggle the customCSS option in {{< hl-text primary >}}config.toml{{< /hl-text >}}:
```
 # Custom CSS. Put here your custom CSS files. They are loaded after the theme CSS;
 # they have to be referred from static root. Example
   [[params.customCSS]]
     href = "css/style.css"
```
{{< alert info >}} If your website is already using a custom css, simple copy and paste the code for calendar styling into the custom css file. {{< /alert >}} 

# Add the calendar to the frontpage

Call the calendar partial in the {{< hl-text orange >}}layouts/index.html{{< /hl-text >}} file, and specify start month and end month:

```
{{ partial "calendar" (dict "context" . "from" 2020 "fromMonth" 1 "to" (now.Format "2006") "toMonth" (now.Format "1") "pages" .Site.Pages) }}
```

Now the calendar should be visable on the website! Pretty awesome.

# Issues and solution

{{< alert warning >}} Issue 1: The background circles for 'calendar days with articles' and 'today' are now showing. {{< /alert >}}
Hugo websites with themes such as [Tranquilpeak](https://themes.gohugo.io/hugo-tranquilpeak-theme/) and [XMag](https://themes.gohugo.io/hugo-xmag/) display this issue when rendering the calendar.
{{< alert success >}} Solution: {{< /alert >}}
In the {{< hl-text primary >}}/static/css/style.css{{< /hl-text >}} file, add `opacity: 0.9` under `.calendar-day.calendar-day-has-articles` and `.calendar-day.calendar-day-is-today`. 
```
.calendar-day.calendar-day-has-articles {
  color: #ffffff;
  position: relative;
  opacity: 0.9;
}
.calendar-day.calendar-day-is-today {
  position: relative;
  font-weight: 800;
  color: #ff6600;
  opacity: 0.9;
}
```
Also move `.calendar-day.calendar-day-future { opacity: 0.4; } ` to the bottom of the file so that the opacity for future dates renders the last.

{{< alert warning >}} Issue 2: Links to categories and tags are not working after adding the calendar to the website. {{< /alert >}}
Hugo websites with theme [XMag](https://themes.gohugo.io/hugo-xmag/) may have this problem.
{{< alert success >}} Solution: {{< /alert >}}
Make sure that the desired taxonomy terms are all included in the {{< hl-text primary >}}config.toml{{< /hl-text >}} file.
```
[taxonomies]
  archive = "archive"
  tag = "tags"
  category = "categories"
```

{{< alert warning >}} Issue 3: The paginators (both `Prev` and `Next`) were broken after adding calendar. {{< /alert >}}
Hugo websites with theme [Tranquilpeak](https://themes.gohugo.io/hugo-tranquilpeak-theme/) may have this problem.
{{< alert info >}} Solution coming soon. {{< /alert >}}