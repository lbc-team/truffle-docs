# Truffle 和 MetaMask 配合

在浏览器中与智能合约进行交互之前，请确保合约已经编译及部署，并且我们是通过客户端JavaScript中的`web3`与合约进行交互。 Truffle 建议使用[truffle-contract](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract)库，因为它使合约的交互更容易，更健壮。


 ```note::
 有关这些主题的更多信息，包括使用`truffle-contract`，请查看的 `Truffle教程：宠物商店 <https://learnblockchain.cn/2018/01/12/first-dapp/>`_ 或 `TutorialToken <https://truffleframework.com/tutorials/robust-smart-contracts-with-openzeppelin>`_ 教程。
 ```

准备好之后，我们就可以使用MetaMask了。

## MetaMask 是什么?

[MetaMask](https://metamask.io/)是在浏览器中与dapps进行交互的最简单方法。 它是Chrome或Firefox的扩展（插件），可以连接到以太坊网络而无需在浏览器的计算机上运行完整节点。 它可以连接到以太坊主网以及任何一个测试网(Ropsten，Kovan和Rinkeby) 或者本地如[Ganache](https://truffleframework.com/ganache)或`Truffle Develop`创建的区块链。

```eval_rst
.. image:: https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask.png
    :alt: MetaMask
    :align: center
```

使用Truffle进行开发，我们与dapp进行交互，就像用户在实时网络上与之交互一样。


## 安装 MetaMask

* 要安装Chrome版本MetaMask ，前往[Chrome网上应用店](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)，然后点击**添加到Chrome**按钮。

* 要安装FireFox版本MetaMask ，前往[Firefox附加组件页面](https://addons.mozilla.org/en-US/firefox/addon/ether-metamask/)并单击**添加到Firefox**按钮。

接下来就看看如何使用。


## MetaMask 和 Ganache 搭配使用

[Ganache](https://truffleframework.com/ganache)是一个运行着区块链的图形应用程序，即提供了一个可用于测试目的区块链。 它默认运行在`127.0.0.1:7545`上。

```note::
 译者注：Ganache在内存中模拟了一个区块链，因此每次Ganache关闭之后，区块链会丢失。
```

```note::
  建议使用`127.0.0.1`而不是`localhost`，因为该地址不需要网络，因此更适合开发。
```

### 探测 MetaMask 注入的 web3

在深一步探究之前，我们需要确保dapp先检查MetaMask的`web3`实例，并且它已经与Ganache的配置正确匹配好。

MetaMask在网页页面中注入了自己的`web3`实例，所以我们先做一个检查，通常这个检查在页面加载之后进行，检查方法如下：


```javascript
  // 检查新版MetaMask
  if (window.ethereum) {
    App.web3Provider = window.ethereum;
    try {
      // 请求用户账号授权
      await window.ethereum.enable();
    } catch (error) {
      // 用户拒绝了访问
      console.error("User denied account access")
    }
  }
  // 老版 MetaMask
  else if (window.web3) {
    App.web3Provider = window.web3.currentProvider;
  }
 // 如果没有注入的web3实例，回退到使用 Ganache
  else {
    App.web3Provider = new Web3.providers.HttpProvider('http://127.0.0.1:7545');
  }
  web3 = new Web3(App.web3Provider);
```

```note::
 译者注：上面这部分检查代码，在Truffle 官方文档还是使用的旧的方式，不过MetaMask 更新后，旧的代码不再推荐使用。
```

### MetaMask 设置

要将Ganache与MetaMask一起搭配使用，需要进行一些设置，方法如下：
单击浏览器中的MetaMask图标，如果第一次使用会出现以下界面：



```eval_rst
.. image:: https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask-create-password.png
    :alt: MetaMask初始界面
    :align: center
```

单击**Import with seed phrase**(使用助记词)导入。 在标有**Wallet Seed**(钱包种子)的输入框中，输入Ganache启动后显示的助记词。

```warning::
 不要在主以太坊主网(mainnet)上使用此助记词。 确保将网络设置为“私有网络”(使用“Custom RPC”进行设置)，下文会详细介绍。
```

输入密码后，点击**IMPORT** ，完成账号导入。

```eval_rst
.. image:: https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask-seed-phrase.png
    :alt: MetaMask 助记词导入
    :align: center
```

```note::
  译者注：如果已经使用过MetaMask 可以是有 MetaMask 导入账号功能，通过Ganache提供的账号私钥导入对应的账号。
```

现在我们需要将MetaMask连接到Ganache创建的区块链。 单击显示`Main Ethereum Network`的菜单（如下图），然后选择**CustomRPC**。

```eval_rst
.. image:: https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask-select-network.png
    :alt: MetaMask 网络菜单
    :align: center
```

在提示文字为“New RPC URL”(在“New Network”旁边)的输入框中输入`http://127.0.0.1:7545`并单击** Save **。


<!--Add image from pet shop tutorial when updated for Ganache -->

顶部的网络名称将切换为“Private Network”(或者显示”RPC URL“),现在MetaMask已经连接到Ganache。 由Ganache创建的每个帐户都有100个以太币。 第一个帐户应该少一点，因为智能合约部署时该帐户为消耗了Gas。

单击右上角的帐户图标以创建新帐户，其中前10个帐户将对应于启动Ganache时显示的10个帐户。

```eval_rst
.. image:: https://truffleframework.com/img/docs/truffle/truffle-with-metamask/metamask-account1.png
    :alt: MetaMask 账号
    :align: center
```

## 搭配 Truffle Develop 使用 MetaMask


Truffle Develop是一个交互式命令行程序，它也运行一个用于测试目的的临时区块链，默认运行在`127.0.0.1：9545`。


```note::
 同样建议使用`127.0.0.1`而不是`localhost`，因为该地址不需要网络连接，因此更适合开发。
```

搭配Truffle Develop使用MetaMask 和Ganache很类似。 唯一的区别是Truffle Develop默认运行在`127.0.0.1：9545`，因此上面的web3代码可以修改为：

Using MetaMask with Truffle Develop is very similar to that of Ganache. The only difference is that Truffle Develop runs by default on `127.0.0.1:9545`, so you'll want to edit the above web3 code to say:

```javascript
  // 检查新版MetaMask
  if (window.ethereum) {
    App.web3Provider = window.ethereum;
    try {
      // 请求用户账号授权
      await window.ethereum.enable();
    } catch (error) {
      // 用户拒绝了访问
      console.error("User denied account access")
    }
  }
  // 老版 MetaMask
  else if (window.web3) {
    App.web3Provider = window.web3.currentProvider;
  }
  // 如果没有注入的web3实例，回退到使用 Truffle Develop
  else {
    App.web3Provider = new Web3.providers.HttpProvider('http://127.0.0.1:9545');
  }
  web3 = new Web3(App.web3Provider);
```

在MetaMask中，输入“New RPC URL”时，输入`http：//127.0.0.1:9545`。

## 搭配 Ganache CLI 使用 MetaMask

Ganache CLI 使用MetaMask，同样非常类似于Ganache。 唯一的区别是Ganache CLI默认运行在`http：//127.0.0.1:8545`上，因此上面的web3代码可以修改为：

```note::
  Ganache CLI 是Ganache的命令行版本。
```

```javascript
  // 检查新版MetaMask
  if (window.ethereum) {
    App.web3Provider = window.ethereum;
    try {
      // 请求用户账号授权
      await window.ethereum.enable();
    } catch (error) {
      // 用户拒绝了访问
      console.error("User denied account access")
    }
  }
  // 老版 MetaMask
  else if (window.web3) {
    App.web3Provider = window.web3.currentProvider;
  }
  // 如果没有注入的web3实例，回退到使用 Ganache CLI
  else {
    App.web3Provider = new Web3.providers.HttpProvider('http://127.0.0.1:8545');
  }
  web3 = new Web3(App.web3Provider);
```

在MetaMask中，输入“New RPC URL”时，输入`http：//127.0.0.1:8545`。

