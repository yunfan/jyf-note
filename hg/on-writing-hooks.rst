=====================================
为hg写钩子
=====================================

概述
========

钩子定义在 hgrc里的 [hooks]下

钩子定义有两种，一种是外部脚本，另一种是进程内的python脚本，官方推荐前者，并保证api的稳定

我推荐后者，因为省事

外部脚本
----------

外部脚本可以是任意可执行文件

例如::

    [hooks]
    commit.log = /tmp/logit.sh

进程内脚本
------------

进程内脚本有两种形式: 一种是模块的调用，一种是文件的调用，但两者最后的参数都是函数

模块的调用
-----------------

可以写作::

    [hooks]
    commit.log = python:module.xxx.func

其中 module.xxx 是为一个模块名 最后那个 func是函数名

文件的调用
-----------------

可以写作::

    [hooks]
    commit.log = python:/tmp/test.py:func

其中 /tmp/test.py 是为一个文件名 最后那个func是函数名， 注意前面是冒号

对于本地的钩子的认识
==========================

事前拦截
--------------

凡是带 `pre` 前缀的基本都是, 而 `pretxn` 前缀的则是最终完成前的那一步，比如

`pretxncommit` 是指最终将要提交前的那一步

事后处理
--------------

一般的没前缀的多半是事后处理，所以要拦截提交，用 `commit` 就晚了

共性
--------------

无论什么钩子，一般来说 return True就停止处理， return False则继续

所以我猜测调用钩子的函数的形式是::

    for hook in hooks:
        if hook(**kargs):
            break

对于远程钩子的认识
===========================

远程钩子主要是拦截 hg push

这里的问题是 钩子的名字不叫 commit, 而应该是 incoming 与 changegroup

如果想要做事前拦截，推荐用 pretxnchangegroup 这个钩子名。

在远程钩子中如果需要中断处理，return 是没用的，所以需要::

    raise mercurial.util.Abort('错误信息')

远程钩子的其他问题
---------------------

需要注意，我们经常用 hg push hg@example.com 则应该登录到 example.com去

查看 /etc/passwd 里hg用户的home目录在哪里

使用 `mercurial-server` 的话，默认的home目录在 /var/lib/mercurial-server下

该目录下放一个 .mercurial-server 文件，可以查看获知相应的仓库放在哪里

默认的全局权限很弱，一般都用 hgadmin 所以注意看里面的权限配置
