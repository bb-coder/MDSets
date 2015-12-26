title: "mac上搭建github＋hexo的问题"
date: 2013-03-17 16:12:10
categories: hexo 
description: 经过半天的努力终于搭建好了属于自己的基于github＋hexo的个人博客。
tags: hexo 
---

经过半天的努力终于搭建好了属于自己的基于github＋hexo的个人博客，拥有了自己的博客心情还是相当激动的。我的搭建过程是参照了[这篇博客][1]。

<!-- more -->

在搭建的过程当中遇到了一个非常蛋疼的问题，让我纠结了好久，最后发现这个问题其实就是一个格式的问题。

>问题描述：hexo deploy无效，无任何反应。

解决方法：

1. 记得_config.yml里面的标签后的“：”后面都有一个空格
2. deploy的type改成git
3. 然后运行下npm install hexo-deployer-git --save
4. hexo g
5. hexo d

问题得到了完美的解决，赶紧搭建自己的博客吧，骚年们～
good luck！

[1]: http://www.cnblogs.com/mjiayou/p/3791373.html?utm_source=tuicool


---