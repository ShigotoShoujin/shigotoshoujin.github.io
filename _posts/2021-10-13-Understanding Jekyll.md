---
layout: post
title:  "Understanding Jekyll and Themes"
date:   2021-10-13
categories: jekyll update
---

### Understanding Jekyll
The first step in fixing the misunderstanding which prevent the blog from working is to start previewing the pages locally. See [Testing your GitHub Pages site locally with Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)

Starting with the working demo blog and isolating the issue, the following observation can be made
- The correct file name format is "yyyy-mm-dd-title.md". By reflex a ".title" was used instead of "-title"
- The category in the header does matter, it has to be "jekyll update". More research can be made on how to use categories.
- The date in the header is used to determine where the blog file will be in the site folders, and not the date from the file name.
- Posts can be added with a future date, and will only be visible later.
- The file extension does not matter between .markdown and .md

### Understanding Themes
The desire to use a theme lead to the discovery of this [Step by Step Tutorial](https://jekyllrb.com/docs/step-by-step/01-setup/).
What better way to understand something than to learn from scratch!

With this new knowledge, the github page is now in an acceptable state for the time being.
[https://shigotoshoujin.github.io](https://shigotoshoujin.github.io)
