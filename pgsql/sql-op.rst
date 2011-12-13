============================================
SQL操作tip
============================================

:Author: jyf<jyf1987@gmail.com>
:Date: 2011-12-03

.. contents:: 目录
.. sectnum::


What
===========

    这个文档用来记录各种管理操作的对应 sql命令, 用来替代对应的各种命令工具 比如创建用户 创建数据库神马的 

Why
=========

#. 对工具的依赖最小，只需要一个 psql就ok

#. 能够更深入的了解 pgsql 系统

#. 够耍酷

HowTo
=========

创建用户
-------------

创建一个密码为 dddd 的用户 test, 并赋予登录权限::

    create user test password 'dddd';

创建超级用户 test::

    create user test password 'dddd' superuser;

或者给已经存在的用户赋予 超级用户权限::

    alter role dbu superuser;





