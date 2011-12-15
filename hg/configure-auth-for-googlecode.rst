===============================================
为googlecode的hg仓库配置权限验证
===============================================

:Author: jyf
:Date: 2011-12-15

.. contents:: 目录
.. sectnum::

缘起
=========

    googlecode 终于支持 git 和 hg 了， git的挺麻烦， 所以我就主打用hg了

另外公司也用hg, 知识可以复用。

    但 googlecode 不支持 sshkey 验证， 只能走 http验证， 所以得配置用户名

与密码，当然你不配置也行， 只是每次都需要输入密码。

解决
=========

    假设在 googlecode 上开了一个项目 `my-project`

    并且你的用户名为 `xxx`
    
    则可以用::

    hg clone https://xxx@code.google.com/p/my-project/

来初始化， 这时候你需要做的是
    
    #. 打开 项目目录下的 .hg/hgrc
    
    #. 增加一个 section::
    [auth]
    googlecode.prefix = code.google.com/p/my-project/
    googlecode.username = xxx
    googlecode.password = 你的googlecode密码
    googlecode.schemes = https


考虑到 defaults 可能被自动设置为 https://xxx@code.google.com/p/my-project/ 你需要
手动修改成 https://code.google.com/p/my-project/

这样设置了以后，就可以hg push了


