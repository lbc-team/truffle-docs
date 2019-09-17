Truffle 翻译说明及概述
================================

本文档是基于 `Truffle 官方最新文档 <https://truffleframework.com/docs>`_ 由 `深入浅出区块链  <https://learnblockchain.cn/>`_ 社区成员整理、翻译及校队，我们虽力求准确，但如您发现纰漏，欢迎到 `GitHub 提Issues  <https://github.com/lbc-team/truffle-docs>`_ 指正。


.. warning::
   最新文档同步时间为2019/05/21。


尊重汗水，如需转载请联系微信：xlbxiong 获取授权。

Truffle 概述
==================

.. image:: https://truffleframework.com/img/truffle-logo-dark.svg
    :width: 160px
    :alt: Truffle logo
    :align: center


Truffle 是一个在以太坊进行 DApp 开发的世界级开发环境、测试框架。它在使开发人员更轻松。这里有几篇博客让我们了解如何开发DApp：

* `开发、部署第一个去中心化应用 (Dapp) - 宠物商店 <https://learnblockchain.cn/2018/01/12/first-dapp/>`_
* `使用 Truffle 开发以太坊投票 DAPP <https://learnblockchain.cn/2019/04/10/election-dapp/>`_
* `DApp 教程：用 Truffle 开发一个链上记事本 <https://learnblockchain.cn/2019/03/30/dapp_noteOnChain/>`_

使用 Truffle 开发有一以下优点：

* 内置智能合约编译，链接，部署和二进制（文件）管理。
* 可快速开发自动化智能合约测试框架。
* 可脚本化、可扩展的部署和迁移框架。
* 可管理多个不同的以太坊网络，可部署到任意数量的公共主网和私有网络。
* 使用 `ERC190 <https://github.com/ethereum/EIPs/issues/190>`_ 标准，使用 EthPM 和 NPM 进行包装管理。
* 支持通过命令控制台直接与智能合约进行交互。
* 可配置的构建管道，支持紧密集成。
* 支持在Truffle环境中使用外部脚本运行器执行脚本。

Truffle 文档内容
==================

Truffle 文档包含5个部分: 快速入门 ，基本功能， 编写测试用例， 高级用法，参考引用，以下是大纲：

.. toctree::
   :maxdepth: 2

   quickstart.md

.. toctree::
   :maxdepth: 2
   :caption: 基本功能

   getting-started/installation.md
   getting-started/creating-a-project.md 
   getting-started/compiling-contracts.md
   getting-started/running-migrations.md
   getting-started/interacting-with-your-contracts.md
   getting-started/truffle-with-metamask.md
   getting-started/package-management-via-ethpm.md
   getting-started/package-management-via-npm.md
   getting-started/debugging-your-contracts.md
   getting-started/using-truffle-develop-and-the-console.md
   getting-started/writing-external-scripts.md
   getting-started/working-with-quorum.md
   
.. toctree::
   :maxdepth: 2
   :caption: 编写测试用例

   testing/testing-your-contracts.md
   testing/writing-tests-in-javascript.md
   testing/writing-tests-in-solidity.md

.. toctree::
   :maxdepth: 2
   :caption: 高级用法

   advanced/networks-and-app-deployment.md
   advanced/build-processes.md
   advanced/creating-a-truffle-box.md


.. toctree::
   :maxdepth: 2
   :caption: 参考引用

   reference/choosing-an-ethereum-client.md
   reference/configuration.md
   reference/contract-abstractions.md
   reference/truffle-commands.md
   reference/contact-the-developers.md
