---
layout: post
title: Git简单应用
date: 2014-04-01 16:34
author: admin
comments: true
categories: [Git]
tags: [Git]
---
<h2>First time using Git（首次用git）</h2>
<div>
<pre>cd myproject
git init
# add all your files.  Use can use specific filenames or directories instead of '.'
git add .
git commit -a -m 'Initial commit'
git remote add origin ssh://waynelau521@git.code.sf.net/p/doufe-com/code
git push origin master
git branch --set-upstream master origin/master  # so 'git pull' will work later</pre>
</div>
<h2>Existing repository using Git(已经存在库)</h2>
<div>
<pre>cd myproject
git remote add origin ssh://waynelau521@git.code.sf.net/p/doufe-com/code
git push origin master
git branch --set-upstream master origin/master  # so 'git pull' will work later</pre>
</div>
