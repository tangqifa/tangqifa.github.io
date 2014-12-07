---
layout: post
title: "Build Octopress Blog Record"
date: 2014-12-07 13:11:05 +0800
comments: true
categories:Octopress
description:"博客配置过程"
keywords:Octopress 
---
Build Octopress Blog on Github 
===
本篇教程在OSX Yosemite 10.10.1下完成

首先查看本机的git和ruby版本  
~$ git --version   
git version 1.9.3 (Apple Git-50)

~$ ruby --version   
ruby 2.0.0p481 (2014-05-08 revision 45883) [universal.x86_64-darwin14]

以上，默认ruby版本是2.0.0，需要升级到最新的2.1版本以上才能顺利进行后面的步骤。

安装brew，下面的步骤有依赖brew   
~$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

安装rvm，并自动更新ruby到最新版本   
~$ \curl -L https://get.rvm.io | bash -s stable --ruby

切换到最新的版本   
~$ rvm use current
Using /Users/tangqifa/.rvm/gems/ruby-2.1.5   

查看是否为最新版本   
~$ ruby --version   
ruby 2.1.5p273 (2014-11-13 revision 48405) [x86_64-darwin14.0]   

安装rbenv，并添加到环境变量中   
git clone git://github.com/sstephenson/rbenv.git .rbenv    

在.bashrc文件加入   
export PATH="$PATH:$HOME/.rbenv/bin" # Add rbenv to PATH for scripting

接着执行以下安装命令   
git clone git://github.com/imathis/octopress.git octopress  
cd octopress  
gem install bundler  
rbenv rehash  
bundle install




