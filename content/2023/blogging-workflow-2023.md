---
title: "My blogging workflow as of late 2023"
date: 2023-11-29T20:00:00+01:00
author: "soeren"
tags: ["blog", "hugo", "uberspace", "git"]
post: true
draft: false
---

In 2019 I wrote an article about the [technical background](/2019/hugo-mit-git-auf-uberspace-benutzen/) of this blog, and in 2020 there was a [post about how I blogged using iPad](/2020/hugo-blogging-ipad/). Neither of these articles mentioned the complete workflow, starting by how I capture ideas and how they slowly form into larger articles.

The idea of this post is to give this comprehensive overview. Not for you to replicate it, because it is so cool & smooth, but for me to see how things change over time. I feel ok with the current setup. If you have suggestions on how to improve it, please email me (using the button on the bottom of this page).

### Collecting ideas

First step is idea collection. Without an idea, there won’t be a blog post. In the past, I used [Pocket](https://getpocket.com/de/) as a read-later app and pasted interesting articles or podcasts in there. It took some until I realised that links will remain there. I never found the time and commitment to go through the list again. Something simpler was needed. That’s when I switched to „[Apple Notes](https://www.icloud.com/notes)“. The app is available on all my devices, it is fast, easy to use, and has the ability to paste almost anything in it. From the workflow perspective this means that I create a new note for every interesting topic I want to write about, paste links, pictures, and my thoughts into it and then leave it. I don’t care about formatting or well written text at this moment. The only purpose is to collect the ideas I have, when I have them, in an as easy as possible way.

### Formulating ideas

Next step is the draft creation. Within my Notes app, I start formulating the raw notes into a text draft. Usually, I re-do this step a couple of times, slightly changing the text with every iteration. Formatting is still postponed. 

### Transfer to markdown

Once the text is in a good enough state, I copy it over to a markdown file, add the front matter (proper title, tags, etc.) and links. Using the markdown syntax, the text is formatted for the first time. This goes hand-in-hand with another round of rephrasing sentences or even paragraphs.

### Publish

The final step is publishing. This blog is maintained in a [git](/tags/git) repository, so in order to publish content, the markdown file needs to be added to the repository. If I blog from the MacBook, I just paste the file in the respective directory, run `git add .` and `git commit -m …`. If I publish from a mobile device, I add the file to the repository using [Working Copy](https://apps.apple.com/de/app/working-copy-git-client/id896694807). 
 
The git repository is hosted on [uberspace](/tags/uberspace), which allows some magic (aka a git hook), when something is pushed to it. Each time I push changes to the remote server, a small script runs that builds the static site and makes it available at my domain. No need to build the site manually each time - a big time and frustration safer 🙂

### Refinement

Once an article is published, I read it in the browser … and will definitely find some typos, broken links or formulations I don’t like. Ergo, switching apps, fixing it, and then re-publish the changes. 

### Ideas for the future

From the [default-apps](/2023/default-apps-2023/) event, I learned that many people use [obsidian](https://obsidian.md/) for note taking. I will give it a try and see if it can replace my Notes and Markdown system break. 