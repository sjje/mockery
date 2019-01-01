.. index::
    single: Installation

安装
============

Mockery 支持使用 Composer 安装或者使用 Git Hub Clone 安装.

Composer
--------

使用 Composer 安装 Mockery 你可以在 `getcomposer.org <https://getcomposer.org>`_ 阅读更多的相关信息.
首先在你的项目中安装 Composer，更多关于 Composer 介绍请查看这里 `Composer download page <https://getcomposer.org/download/>`_.
然后你可以在你的 composer.json 文件中定义 开发依赖。 如果你认为作者对 Master 分支的稳定性,
你可以使用 stable 分支安装一个 stable 版本。 (使用
``@stable`` tag).

.. code-block:: json

    {
        "require-dev": {
            "mockery/mockery": "dev-master"
        }
    }

To install, you then may call:

.. code-block:: bash

    php composer.phar update

这样安装 Mockery 将被作为开发依赖, 意味着它不会被安装在生成环境，当你使用这个命令安装生成依赖的时候 ``php composer.phar update --no-dev``.

Git
---

Git repository 在 master 分支上托管开发分支. 你可以在使用 composer 安装的时候在  ``composer.json`` file 中加上``dev-master`` 来使用最新的版本。
