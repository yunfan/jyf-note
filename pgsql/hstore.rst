=========================================
hstore 数据类型使用札记
=========================================

:Author: jyf<jyf1987@gmail.com>
:Date: 2011-12-03

.. contents:: 目录
.. sectnum::

缘起
==============

需求
-----------

    公司业务上有一些抽象数据类型各有不同的附加数据，偶有查询的可能(统计上)，但是不常见

备选方案
----------------

MongoDB
~~~~~~~~~~~~

有两点原因导致没有使用MongoDB

#. 原有系统全是基于 postgresql 的, 要迁移到 MongoDB 上工作量很大，而此需求并不值得如此庞大工作量

#. MongoDB 不支持 collection之间的并联， 也不支持一些行内引用，导致有不少业务需要重写，结构可能都要变。

Pgsql的关系表
~~~~~~~~~~~~~~~~

这里也有两个原因导致不采用此种方案

#. 数据量大(一个对应关系一条记录)，每次修改会涉及到不少记录的写操作。

#. 增加了复杂性，会把一些两个表的联表查询变成了双层联表查询。


所以最后找到了 hstore 这个pgsql的数据类型


介绍与安装
====================

介绍
-----------

    hstore 是一个扩展的数据类型， 提供 hash键值对的支持， 既可以在某一个字段里存储哈希键值对。

    hstore 是在 pgsql 8.3 引入的 ， 作者是两个俄罗斯人(从邮箱上判断的)。但是在 8.x 系列上功能都很有限

而从 pgsql 9.x 开始， 支持的功能就很丰富了，可以直接创建一个表指定某个字段使用 hstore类型存储。(8.x上只能

寄生在其他字段上 比如 text)

    更多相关介绍看这里 http://www.postgresql.org/docs/9.1/interactive/hstore.html

安装
---------------

环境
~~~~~~~~~~~

=============   ==========================================
Field               Value
=============   ==========================================
CPU                 intel i7 2600
RAM                 4G x 4 ddr3 1600
DISK                1T 7200r
OS                  ubuntu lucid (10.04)
=============   ==========================================

安装操作
~~~~~~~~~~~~

预编译包可以在这里下载:     http://www.postgresql.org/download/linux/     请根据自己的发行版选择

源代码编译在这里下载代码:       http://ftp.postgresql.org/pub/source/v9.1.1/postgresql-9.1.1.tar.bz2        **推荐自己编译，这样更装逼 :-]**

下面的操作是基于更装逼的方法的::

    tar -xvf postgresql-9.1.1.tar.bz2
    cd postgresql-9.1.1

    export w=/tmp/pgsql
    mkdir -p $w/data

    ./configure --prefix=$w
    make -j15
    make install

    cd contrib
    make -j15
    make install

    cd $w
    sudo chown postgres:postgres data
    sudo adduser postgres
    sudo su postgres

    ./bin/initdb -D ./data
    ./bin/postgres -D ./data >> pgsql.log 2>&1 &
    ./bin/psql -c 'create extension hstore'

这里有两点需要注意:

#. 需要在你指定的数据库上用超级用户执行 create extension hstore; 默认的是在 postgres这个上面执行 可能会出错 

#. 需要停止一切占用 5432 端口的程序， 因为pgsql自己要用

