---
title: Creating a GitHub Pages Site with Jekyll
subtitle: A page about how this page was made
published: false
categories: projects
---

Jekyll is a static site generator that is integrated with GitHub Pages. That makes things pretty easy on us. It is a nice middle ground between being easy to build out of the box but fully customizable with HTML, CSS, etc.

[Here's a link](https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB) to the video tutorials I used to get started and to the [jekyll documentation tutorial](https://jekyllrb.com/docs/step-by-step).

### Getting Things Set Up

I started by downloading the [RubyInstaller](https://github.com/oneclick/rubyinstaller2/releases) to install the Ruby programming language on my local machine. I let it do its magic and then 

### Making the GitHub Page
This is pretty simple - just make a new repository on github with the name \<username>.github.io. I cloned the repository to my computer

### Skeleton Site
I navigated to the directory holding the cloned repository and ran ```jekyll new milesok.github.io```, which creates the skeleton Jekyll site in the folder. I then pushed it all up to the repository using Git and now I have a website. We can create a local development server with ```jekyll serve``` but GitHub will automatically build the page with any updated commits you push.

## The Basics of Jekyll
### Frontmatter
Basically metadata: written in YAML or JSON
```
---
layout: post
title: "Post Title!"
date: 2020-07-17 8:55:00 -0600
categories: projects
---
```
It's useful because this data can be processed into how the website displays things, like the 'title' variable could be used by the home page to link to our post. We can [create defaults](https://jekyllrb.com/docs/configuration/front-matter-defaults/) in the \_config.yml file

### Posts!
They can be written in Markdown which makes things easy
1. Create file in \_post folder with the file name ```yyyy-mm-dd-post-title.md```
2. Always start with frontmatter: layout, title, date, etc - you can set defaults to make things easier
3. Within \_posts you can make separate directories and it doesn't mess with how Jekyll processes everything
4. We can use a \_drafts folder for unfinished stuff - ```jekyll serve --draft``` will show what the page looks like with drafts

Side note I'm using [prose.io](prose.io) to edit markdown and easily save changes/preview/etc. within the browser.

### Pages!
Basically anything that's not a blog. The frontmatter 'permalink' variable allows us to override the link path. We can give it a string to define the path, or a pattern (e.g. /:categories/:year/:month/:day/:title). I made a ['/now' page](https://nownownow.com/about) to test this out which is now [here](https://milesok.github.io/now).

That's about 