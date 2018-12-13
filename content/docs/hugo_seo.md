---
title: "Hugo SEO搜索引擎优化"
keywords: ["WeFox", "唯狐博客", "Hugo", "SEO", "搜索引擎优化"]
date: 2018-12-12T16:30:07+08:00
tags: ["Ubuntu", "Hugo"]
categories: ["Web"]
draft: false
---

## 前言
为了让网站能更好的被搜索引擎收录，就需要进行搜索引擎优化，这里只针对Google。

## 站点内容优化
修改hugo根目录下的```config.toml```文件：

    [params.info]
    subtitle = "XXXXXXXX"
    keywords = "XXX,XXX"
    description = "XXXXXXXXXXXXXXXXXXXXXXX"
    title404 = "XXXXXXXXXXXXX"

具体内容请自行替换

修改模板中的```head.html```文件如 themes/××××/layouts/partials/head/head.html，如果有```meta.html```则直接在此文件中加入

    <meta name='keywords' content="{{ delimit .Keywords ", " }}">

    <meta property="og:title" content="{{ .Title }} - {{ .Site.Title }}">
    <meta name="apple-mobile-web-app-title" content="{{ .Title }} - {{ .Site.Title }}">

    {{ $realDescription := or .Description .Site.Params.description}}
    <meta name="description" content="{{ $realDescription }}">
    <meta property="og:description" content="{{ $realDescription }}">

这三段分别是对```keyword```，```title```和```description```的优化。

## 添加页面信息
首先确保每个页面一定都要设置有```title```，```description```，最好还有```keywords```：

	keywords:
	- mysite
	- mysite keyword
	- Another useful keyword
	title: My Homepage
    description : mysite description

## Google搜索优化

#### sitemap.xml配置
先登录[Google Search Console](https://search.google.com/search-console/sitemaps?resource_id=https%3A%2F%2Fwefox.me%2F&hl=en)，上传sitemap.xml，一般该文件会自动在hugo的根目录下生成，如果没有的话，可以自己在```layouts```或者```theme/layouts/_default```下创建一个，并输入：

	<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
	xmlns:xhtml="http://www.w3.org/1999/xhtml">
	{{ range .Data.Pages }}
	<url>
		<loc>{{ .Permalink }}</loc>{{ if not .Lastmod.IsZero }}
		<lastmod>{{ safeHTML ( .Lastmod.Format "2006-01-02T15:04:05-07:00" ) }}</lastmod>{{ end }}{{ with .Sitemap.ChangeFreq }}
		<changefreq>{{ . }}</changefreq>{{ end }}{{ if ge .Sitemap.Priority 0.0 }}
		<priority>{{ .Sitemap.Priority }}</priority>{{ end }}{{ if .IsTranslated }}{{ range .Translations }}
		<xhtml:link
					rel="alternate"
					hreflang="{{ .Lang }}"
					href="{{ .Permalink }}"
					/>{{ end }}
		<xhtml:link
					rel="alternate"
					hreflang="{{ .Lang }}"
					href="{{ .Permalink }}"
					/>{{ end }}
	</url>
	{{ end }}
	</urlset>

然后在终端用```hugo```命令后并上传网站会在网站根目录下出现。

#### robots.txt配置
在旧版的[Google Search Console](https://www.google.com/webmasters/tools/robots-testing-tool?utm_source=support.google.com/webmasters/&utm_medium=referral&utm_campaign=%206062598)里可以测试```robots.txt```，在```robots.txt```的末尾添加```sitemap```

	User-agent: *
	Allow: /
	Allow: /docs/
	Allow: /gossip/
	Allow: /about/
	Disallow: /assets/

	Sitemap: https://wefox.me/sitemap.xml

一般hugo或者主题都会自动生成```robots.txt```，默认基本都是在根目录下。

#### Google Analytics
具体操作直接看这个[官方文档](https://support.google.com/analytics/answer/1008015?hl=en)吧，设置好之后可以在只需登录一下自己的博客，然后在```Real-Time```里看下是否有登录信息就可知道是否绑定成功。

## Reference
https://skyao.io/learning-hugo/installation/seo.html
https://jimmysong.io/hugo-handbook/steps/seo.html