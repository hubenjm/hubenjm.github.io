---
layout: post
title: Site Setup
tags: ["Jekyll", "Blog", "Web Design", "css"]
year: 2014
month: 12
day: 21
---

I thought it would maybe be useful to talk a bit on how I set up this webpage. I may decide to continually add to this post in the future. But in short, I built it using github.io as a hosting platform and I combined a few different Jekyll blog templates to make it work.

1. [Jekyll-Now](https://github.com/barryclark/jekyll-now)
2. [drvinceknight](https://github.com/drvinceknight/drvinceknight.github.com/blob/master/css/main.css#L207) - copied some formatting `.css` code for dealing with inline code formatting and such
3. [Standard .highlight template](https://github.com/mojombo/tpw/blob/master/css/syntax.css#L5) - replaced the Jekyll-Now `_highlight.css` file with this

Overall, I wasn't happy with the black background for embedded code that is standard with the Jekyll-Now template, which is the original reason I started looking around for other Jekyll based sites for inspiration. I really do like the general workflow of doing a site this way as opposed to the purely `.html` sites (usually without any `.css`) I have done in the past. Never again will I have to go back and change a menu and its associated links on every page in my directory manually. All key style and navigational structure is determined in the `.css` and `_config.yml` files - not in the content itself.

The posts themselves use the markdown `.md` language, which is extremely readable even in a standard text editor. Furthermore, `.md` files can be compiled into `.pdf` files or other formats besides web-friendly html, if so desired.

When working on posts, I have the site running locally by going to my main local site directory and typing in the command line:
{% highlight bash %}
> jekyll serve --baseurl ''
{% endhighlight %}

Then I just navigate to `http://localhost:4000/` to see the changes updated in real-time as I edit away.
