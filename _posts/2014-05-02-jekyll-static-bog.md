---
layout: post
title: Jekyll安装及写静态博客
date: 2014-05-02 16:46
author: admin
comments: true
categories: [Jekyll]
tags: [Ruby,Github,Jekyll]
---

## 下载、安装  ruby 和 DEVELOPMENT KIT

地址<http://rubyinstaller.org/downloads/>

其中 Ruby 与 DEVELOPMENT KIT 的版本要对应来安装

* Ruby 1.8.7 and 1.9.3: DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe
* Ruby 2.0 and 2.1 (32bits version only): DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe
* Ruby 2.0 and 2.1 (x64 - 64bits only): DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe

ruby安装到C:\Ruby193;

DevKit安装到C:\rubydevkit

	cd C:\rubydevkit
	ruby dk.rb init
	ruby dk.rb install

如果是ruby2.0 64位的还需在C:\rubydevkit\config.yml 
中添加"- C:\Ruby200-x64"

<!-- more -->

## 更改gem镜像到 taobao网，可以改善国内Ruby安装的速度

	gem sources --remove https://rubygems.org/
	gem sources -a https://ruby.taobao.org/
	gem sources -l         #查看是否只有taobao镜像
	gem update --system    #更新RubyGems软件

## 安装jekyll

    gem install jekyll

## 安装rdiscount，这个是用来解析Markdown标记的解析包。

	gem install rdiscount

## 写markdown

一定要确保你的文章要保存为UTF-8 无 BOM 格式才行。 
文件名称不能是中文

## 编译md文件，启动博客

	jekyll serve

## 相关错误处理
### 错误1
   
	  Generating... c:/Ruby193/lib/ruby/gems/1.9.1/gems/posix-spawn-0.3.8/lib/po	
	six/spawn.rb:162: warning: cannot close fd before spawn
	'which' 不是内部或外部命令，也不是可运行的程序

需要安装[Python](https://www.python.org/downloads/),修改pygments.rb版本，不要0.5.2的版本

	gem install pygments.rb --version "=0.5.0"
	gem uninstall pygments.rb --version "=0.5.2"

### 错误2：中文乱码

	error: invalid byte sequence in GBK. Use --trace to view backtrace

jekyll 1.3.0版本以后的，修改如下：
打开路径 C:\Ruby193\lib\ruby\gems\1.9.1\gems\jekyll-1.5.1\lib\jekyll，打开 convertible.rb

	self.content = File.read_with_options(File.join(base, name),
	merged_file_read_opts(opts))
改成

	self.content = File.read_with_options(File.join(base, name),:encoding=>"utf-8")
	
	
打开路径 C:\Ruby193\lib\ruby\gems\1.9.1\gems\jekyll-1.5.1\lib\jekyll\tags，打开include.rb 

	File.read_with_options(file, file_read_opts(context))
改成

	File.read_with_options(file, :encoding=>"utf-8")

## wordpress转md

参考:<http://johnnycode.com/2012/07/10/how-to-migrate-from-wordpress-to-jekyll-running-on-github/> 和
<https://github.com/theaob/wpXml2Jekyll>