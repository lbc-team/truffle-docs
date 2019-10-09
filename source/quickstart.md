# 快速入门 Truffle

本文主要入门介绍如何创建 Truffle 项目以及将智能合约部署到区块链。

 ```note::
  在开始之前，最好对以太坊有基础的了解，推荐阅读 `以太坊是什么 - 以太坊开发入门指南 <https://learnblockchain.cn/2017/11/20/whatiseth/>`_
  或阅读 `以太坊概述（英文） <https://truffleframework.com/tutorials/ethereum-overview>`_ 。
 ```

```eval_rst
.. _creating-a-project:
```
## 创建项目工程


Truffle 大多数命令都是在 Truffle 项目目录下运行的。 所以第一步是创建一个 Truffle 项目。 可以创建一个空项目模板，不过对于刚接触Truffle的同学，推荐使用[Truffle Boxes](https://truffleframework.com/boxes)，它提供了示例应用代码和项目模板。 我们将使用[MetaCoin box](https://truffleframework.com/boxes/metacoin)作为案例，它创建一个可以在帐户之间转移的Token（代币）。

1. 为 Truffle 项目创建新目录：

   ```shell
   mkdir MetaCoin
   cd MetaCoin
   ```

1. 下载 ("unbox") MetaCoin box:

   ```shell
   truffle unbox metacoin
   ```

 ```note::
  也可以使用 `truffle unbox <box-name>` 命令下载其他的 Box
 ```

 ```note::
   如果要创建没有合约的空工程，可以使用 `truffle init`.
 ```

 在操作完成之后，就有这样的一个项目结构：

* `contracts/`:  [Solidity合约目录](getting-started/interacting-with-your-contracts.md)
* `migrations/`: [部署脚本文件](getting-started/running-migrations.html#id2)目录
* `test/`:    测试脚本目录，参考 [如何测试应用？](testing/testing-your-contracts)
* `truffle.js`: Truffle [配置文件](reference/configuration)

```eval_rst
.. _Exploring the project:
```
## 项目结构

 ```note::
  这仅仅是一个入门。后面的文章我们可以学习到更多。
 ```

1. `contracts/MetaCoin.sol`： 这是一个用 [Solidity](https://learnblockchain.cn/docs/solidity/) 编写的 MetaCoin 代币 智能合约。注意他还引用了目录下的另外一个合约文件 `contracts/ConvertLib.sol` 。

1. `contracts/Migrations.sol`： 这是一个单独的 Solidity 文件，用来[管理和升级智能合约](getting-started/running-migrations). 每一个工程都有这样的一个文件，并且通常不需要编辑它。

1. `migrations/1_initial_migration.js`： 这是一个部署脚本，用来部署 `Migrations` 合约，对应 `Migrations.sol` 文件。

1. `migrations/2_deploy_contracts.js`： 这是一个部署脚本，用来部署 `MetaCoin` 合约. (部署脚本的运行是有顺序的，以2开头的脚本通常在以1开头的脚本之后运行)

1. `test/TestMetacoin.sol`： 这是一个[用Solidity编写的测试用例](testing/writing-tests-in-solidity)文件，用来检查合约是否像预期一样工作。

1. `test/metacoin.js` ： 这是一个用[JavaScript编写的测试用例脚本](testing/writing-tests-in-javascript)，用途和上面一样。

1. `truffle-config.js` （之前是 `truffle.js`）： Truffle [配置文件](reference/configuration), 用来设置网络信息，和其他项目相关的设置。当我们使用内建的默认的Truffle命令时，这个文件留空也是可以的。

```eval_rst
.. _Testing:
```
## 使用测试

1. 打开控制台终端，运行 Solidity 测试用例:

   ```shell
   truffle test ./test/TestMetacoin.sol
   ```

   我们可以看到下面的输出：

    ```
     TestMetacoin
       √ testInitialBalanceUsingDeployedContract (71ms)
       √ testInitialBalanceWithNewMetaCoin (59ms)

     2 passing (794ms)
   ```

```note::
 如果在windows平台运行，这个命令遇到问题，可以参考这个文档 `解决Windows命名冲突 <reference/configuration.html#resolving-naming-conflicts-on-windows>`_ 。
```

  运行测试用例的时候。期望的行为，会输出在控制台

1. 运行 JavaScript 测试用例

   ```shell
   truffle test ./test/metacoin.js
   ```

   You will see the following output

   ```
     Contract: MetaCoin
       √ should put 10000 MetaCoin in the first account
       √ should call a function that depends on a linked library (40ms)
       √ should send coin correctly (129ms)

     3 passing (255ms)
   ```

```eval_rst
.. _Compiling:
```

## 编译合约

1. 编译智能合约：

   ```shell
   truffle compile
   ```

   我们可以看到下面的输出:

   ```
   Compiling .\contracts\ConvertLib.sol...
   Compiling .\contracts\MetaCoin.sol...
   Compiling .\contracts\Migrations.sol...

   Writing artifacts to .\build\contracts
   ```

```eval_rst
.. _Migrating with Truffle Develop:
```

## 使用 Truffle Develop 部署合约


 ``` note::
  如果使用 `Ganache <https://truffleframework.com/ganache>`_ , 可以直接跳到下一部分。
 ```

为了部署我们的合约，我们需要连接到区块链网络。Truffle 提供了一个内置的个人模拟区块链，它可以帮助我们用来测试。注意，这个区块链是内地在我们本地的系统里面，他不和以太坊的组网进行连接。

我们可以使用[Truffle Develop](getting-started/using-truffle-develop-and-the-console#truffle-develop)来创建区块链，并与之交互。


1. 运行 Truffle Develop:

   ```shell
   truffle develop
   ```

   我们可以看到下面的信息：

   ```
   Truffle Develop started at http://127.0.0.1:9545/

   Accounts:
   (0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
   (1) 0xf17f52151ebef6c7334fad080c5704d77216b732
   (2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
   (3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
   (4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
   (5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
   (6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
   (7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
   (8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
   (9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

   Private Keys:
   (0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
   (1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
   (2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
   (3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
   (4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
   (5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
   (6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
   (7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
   (8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
   (9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

   Mnemonic: candy maple cake sugar pudding cream honey tiny smooth crumble sweet treat

   ⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
   Ensure you do not use it on production blockchains, or else you risk losing funds.

   truffle(development)>
   ```

   这也显示了10个账号，和他们对你的私钥，这些账号可以用来和区块链进行交互。

1. 在 truffle(develop)> 提示符（因为提供了一个交互式控制台）下， Truffle 的命令可以不带前缀 `truffle` 执行。比如，可以直接输入`compile` 来执行`truffle compile`，以及直接输入 `migrate` 来部署编译的智能合约到区块链（相当于`truffle migrate`）：

   ```shell
   migrate
   ```

  我们可以看到下面的信息：

   ```
   Starting migrations...
   ======================
   > Network name:    'develop'
   > Network id:      4447
   > Block gas limit: 6721975

   1_initial_migration.js
   ======================

      Deploying 'Migrations'
      ----------------------
      > transaction hash:    0x3fd222279dad48583a3320decd0a2d12e82e728ba9a0f19bdaaff98c72a030a2
      > Blocks: 0            Seconds: 0
      > contract address:    0xa0AdaB6E829C818d50c75F17CFCc2e15bfd55a63
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.99445076
      > gas used:            277462
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.00554924 ETH

      > Saving migration to chain.
      > Saving artifacts
      -------------------------------------
      > Total cost:          0.00554924 ETH

   2_deploy_contracts.js
   =====================

      Deploying 'ConvertLib'
      ----------------------
      > transaction hash:    0x97e8168f1c05fc40dd8ffc529b9a2bf45cc7c55b07b6b9a5a22173235ee247b6
      > Blocks: 0            Seconds: 0
      > contract address:    0xfb39FeaeF3ac3fd46e2123768e559BCe6bD638d6
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.9914458
      > gas used:            108240
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.0021648 ETH

      Linking
      -------
      * Contract: MetaCoin <--> Library: ConvertLib (at address: 0xfb39FeaeF3ac3fd46e2123768e559BCe6bD638d6)

      Deploying 'MetaCoin'
      --------------------
      > transaction hash:    0xee4994097c10e7314cc83adf899d67f51f22e08b920e95b6d3f75c5eb498bde4
      > Blocks: 0            Seconds: 0
      > contract address:    0x6891Ac4E2EF3dA9bc88C96fEDbC9eA4d6D88F768
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.98449716
      > gas used:            347432
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.00694864 ETH

      > Saving migration to chain.
      > Saving artifacts
      -------------------------------------
      > Total cost:          0.00911344 ETH

   Summary
   =======
   > Total deployments:   3
   > Final cost:          0.01466268 ETH
   ```

   这里显示了交易的ID号，部署的合约地址。以及交易的花费和一些相关的状态。

 ``` note::
  当然我们自己部署的时候，交给哈希和一个地址和我们的账号，都是和上面不一样的。
 ```




```eval_rst
.. _Alternative-Migrating-with-Ganache:
```

## 可选: 通过 Ganache 部署

 ``` note::
  直接了解如何与合约进行交互，可以直接跳到下一个部分。
 ```

除了用上面的 Truffle Develop，还可以选择使用 [Ganache](https://truffleframework.com/ganache), 这是一个桌面应用，他同样会创建一个个人模拟的区块链。 对于刚接触以太坊的同学来说，`Ganache` 会更容易理解，因为他把所有的信息，都输在前端的界面。

不像 Truffle Develop 把链和控制台集成在一起，使用 Ganache 需要编辑配置文件，以便 Truffle 能链接 Ganache 实例。

1. 下载安装 [Ganache](https://truffleframework.com/ganache).

1. 编辑器打开 `truffle.js` ，使用下面的内容：

   ```javascript
   module.exports = {
     networks: {
       development: {
         host: "127.0.0.1",
         port: 7545,
         network_id: "*"
       }
     }
   };
   ```

   这是使用默认的连接参数去连接  Ganache（如果IP和端口有变化，需要同步修改上面的内容）。

1. 保存关闭配置文件。

1. 启动Ganache

   ![Ganache 界面](https://truffleframework.com/img/docs/ganache/quickstart/accounts.png)

1. 打开控制台，执行部署：

   ```shell
   truffle migrate
   ```

   我们可以看到类似下面的输出：

   ```
   Starting migrations...
   ======================
   > Network name:    'develop'
   > Network id:      4447
   > Block gas limit: 6721975

   1_initial_migration.js
   ======================

      Deploying 'Migrations'
      ----------------------
      > transaction hash:    0x3fd222279dad48583a3320decd0a2d12e82e728ba9a0f19bdaaff98c72a030a2
      > Blocks: 0            Seconds: 0
      > contract address:    0xa0AdaB6E829C818d50c75F17CFCc2e15bfd55a63
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.99445076
      > gas used:            277462
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.00554924 ETH

      > Saving migration to chain.
      > Saving artifacts
      -------------------------------------
      > Total cost:          0.00554924 ETH

   2_deploy_contracts.js
   =====================

      Deploying 'ConvertLib'
      ----------------------
      > transaction hash:    0x97e8168f1c05fc40dd8ffc529b9a2bf45cc7c55b07b6b9a5a22173235ee247b6
      > Blocks: 0            Seconds: 0
      > contract address:    0xfb39FeaeF3ac3fd4TinY66668exionge6bD638d6
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.9914458
      > gas used:            108240
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.0021648 ETH

      Linking
      -------
      * Contract: MetaCoin <--> Library: ConvertLib (at address: 0xfb39FeaeF3ac3fd46e2123768e559BCe6bD638d6)

      Deploying 'MetaCoin'
      --------------------
      > transaction hash:    0xee6574803810e7314cc83adf899d67f51f22e08b920e657480385c5eb498bde4
      > Blocks: 0            Seconds: 0
      > contract address:    0x6891ATinyEF3dA9bc88C96fXlbC9eA4d6D88F768
      > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
      > balance:             99.98449716
      > gas used:            347432
      > gas price:           20 gwei
      > value sent:          0 ETH
      > total cost:          0.00694864 ETH

      > Saving migration to chain.
      > Saving artifacts
      -------------------------------------
      > Total cost:          0.00911344 ETH

   Summary
   =======
   > Total deployments:   3
   > Final cost:          0.01466268 ETH
   ```

   这里同样显示了交易的ID号（hash），部署的合约地址。以及交易的花费和一些相关实时状态。

 ``` note::
  当然我们自己部署的时候，交给哈希和一个地址和我们的账号，都是和上面不一样的。
 ```

1. 在 Ganache 里，点击 "Transactions" 可以看到交易详情。

1. 为了和合约进行交互，我们可以使用 Truffle 的控制台：`truffle console`， Truffle console 和 Truffle Develop 类似，仅仅是他们连接的链不一样而已，这里是连接 Ganache 。

   ```shell
   truffle console
   ```

   可以看到下面的提示:

   ```
   truffle(development)>
   ```

``` note::
  控制台提示符：truffle(development)> 括号里，指当前连接的网络。
 ```

```eval_rst
.. _Interacting with the contract:
```
## 合约交互

可以使用控制台 console 和合约进行交互：

``` note::
 我们在例子中使用 `web3.eth.getAccounts()` , 它返回一个 promise 从中可以获取到由助记词生成的所有账号。  如果指定 `(await web3.eth.getAccounts())[0]` 会返回 `0x627306090abab3a6e1400e9345bc60c78a8bef57`.
```

在 Truffle v5, 控制台支持 async/await 方法（同步方式）, 这样让跟合约交互更简单了，方法如下：

* 从获取部署合约实例 及获取账号列表 开始：

  ```shell
  truffle(development)> let instance = await MetaCoin.deployed()
  truffle(development)> let accounts = await web3.eth.getAccounts()
  ```

* 检查账号余额：

  ```shell
  truffle(development)> let balance = await instance.getBalance(accounts[0])
  truffle(development)> balance.toNumber()
  ```

* 查看以太价值（其实就是调用了一个合约方法：合约方法里定义了一个metacoin 价值 2 ether)：

  ```shell
  truffle(development)> let ether = await instance.getBalanceInEth(accounts[0])
  truffle(development)> ether.toNumber()
  ```

* 发送一些 metacoin 到其他的账号 :

  ```shell
  truffle(development)> instance.sendCoin(accounts[1], 500)
  ```

* 检查刚刚收款人的余额：

  ```shell
  truffle(development)> let received = await instance.getBalance(accounts[1])
  truffle(development)> received.toNumber()
  ```

* 检查刚刚发送方的余额：

  ```shell
  truffle(development)> let newBalance = await instance.getBalance(accounts[0])
  truffle(development)> newBalance.toNumber()
  ```

```eval_rst
.. _Continue learning:
```
## 进一步学习

本篇快速入门仅仅是了解 Truffle 工程的一个生命周期，还有很多内容需要学习，请学习文档其余部分。