---
title: Creating a GitHub Pages Site with Jekyll
subtitle: A page about how this page was made (don't think too hard about it)
published: true
---

Jekyll is a static site generator that is integrated with GitHub Pages. That makes things pretty easy on us. It is a nice middle ground between being easy to build out of the box but fully customizable with HTML, CSS, etc.

[Here's a link](https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB) to the video tutorials I used to get started

### Getting Things Set Up

I started by downloading the [RubyInstaller](https://github.com/oneclick/rubyinstaller2/releases) to install the Ruby programming language on my local machine. I let it do its magic and then 

### Making the GitHub Page
This is pretty simple - just make a new repository on github with the name \<username>.github.io. I cloned the repository to my computer

### Skeleton Site
I navigated to the directory holding the cloned repository and ran ```jekyll new milesok.github.io```, which creates the skeleton Jekyll site in the folder. I then pushed it all up to the repository using Git and now I had a website.

## The Basics of Jekyll
### Frontmatter
Basically metadata: written in YAML or JSON
```---
layout: post
title: "Post Title!"
date: 2020-07-17 8:55:00 -0600
categories: projects
---
It's useful because this data can be processed into how the website displays things, like the 'title' variable could be used by the home page to link to our post.