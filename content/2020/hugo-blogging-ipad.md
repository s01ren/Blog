---
title: Publish to Blog from iPad
date: 2020-12-28T12:00:00+01:00
author: soeren
tags: 
  - blogging
  - git
  - hugo
  - ios
post: true
draft: false
---

Unfortunately, ideas for new blog posts often fizzle out because I find it too much of an effort to boot up my laptop in the evening and organize my thoughts. I am therefore always on the lookout for ways to lower the inhibition threshold as much as possible.

A good approach would be to remove the laptop from the equation and blog from a device that I have in my hand all the time anyway: Smartphone or tablet, for example. But because the cell phone display is perhaps a bit small, I'm going to see if I can blog *well* from an iPad today. And **yes**, this article was written entirely on the iPad. 

I use [Hugo](/tags/hugo) as the foundation for my blog, which I run ([as described here](/2019/hugo-mit-git-auf-uberspace-benutzen)) on a [uberspace](/tags/uberspace). This has the advantage that I can outsource some of the magic to the server. Specifically, I have set up a `post-update` hook in the [Git](/tags/git) repository on the server. This automatically creates the static files after each commit. 

So I don't need to have Hugo installed on the i-Device, I just need a Git client and a text editor. I found both in [Working Copy](https://workingcopyapp.com/). 

The app costs a bit, but in my opinion it's worth every penny. Especially because it is perfectly integrated into [iOS](/tags/ios). For example, the repository can be accessed via the Files app - so I can edit the content with many different apps.

My workflow is now as follows:

1. within Working Copy, I duplicate an existing Markdown file as the basis for the new article and adapt the YAML header.
1. open the new file in the integrated text editor and write the article. For a little more syntax highlighting, you can also open the file in an external editor (e.g. Textastic).
1. if I want to include screenshots or other files, I save them directly to the `static` folder of the repository via the iOS Files app. 
1. when I'm done, I use Working Copy to commit the changes and then push them to the repository. The uberspace then automatically updates the blog at [gluecko.se](https://gluecko.se) as described.

It could hardly be easier.