Truffle 框架文档翻译说明 及 概述
================================

本文档是基于 `Truffle 官方最新文档 <https://truffleframework.com/docs>`_ 由 `深入浅出区块链  <https://learnblockchain.cn/>`_ 社区成员整理、翻译及校队，我们虽力求准确，但如您发现纰漏，欢迎到 `GitHub 提Issues  <https://github.com/lbc-team/truffle-docs>`_ 指正。

尊重汗水，需转载请联系微信：xlbxiong 获取授权。

Truffle 概述
==================

![Truffle Logo](https://truffleframework.com/img/truffle-logo-dark.svg)
<img style="max-width: 160px;" src="" alt="" />

Truffle 是一个在以太坊虚拟机（EVM）上进行开发的世界级开发环境、测试框架。旨在使开发人员更轻松。 使用 Truffle 开发有一下优点：

* Built-in smart contract compilation, linking, deployment and binary management.
* Automated contract testing for rapid development.
* Scriptable, extensible deployment & migrations framework.
* Network management for deploying to any number of public & private networks.
* Package management with EthPM & NPM, using the [ERC190](https://github.com/ethereum/EIPs/issues/190) standard.
* Interactive console for direct contract communication.
* Configurable build pipeline with support for tight integration.
* External script runner that executes scripts within a Truffle environment.

.. toctree::
   :maxdepth: 2
   :caption: Truffle 使用手册

   quickstart.md

.. toctree::
   :maxdepth: 2
   :caption: 快速开始使用

   quickstart/installation.md
   quickstart/creating-a-project.md 
   quickstart/compiling-contracts.md
   quickstart/running-migrations.md
   quickstart/interacting-with-your-contracts.md
   quickstart/truffle-with-metamask.md
   quickstart/package-management-via-ethpm.md
   quickstart/package-management-via-npm.md
   quickstart/debugging-your-contracts.md
   quickstart/using-truffle-develop-and-the-console.md
   quickstart/writing-external-scripts.md
   quickstart/working-with-quorum.md
   
.. toctree::
   :maxdepth: 2
   :caption: 编写测试

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
   :caption: 参考文章


   reference/choosing-an-ethereum-client.md
   reference/configuration.md
   reference/contract-abstractions.md
   reference/truffle-commands.md
   reference/contact-the-developers.md
