# 用 EthPM 进行包管理


EthPM 是以太坊的新[包管理库](https://www.ethpm.com/)。 它遵循[ERC190规范](https://github.com/ethereum/EIPs/issues/190)，用于发布和使用智能合约包，并获得了许多不同的以太坊开发工具的广泛支持。 为了表示支持，我们也将以太坊包管理库（Package Registry) 直接集成到 Truffle 中。



## 安装软件包


从EthPM安装软件包几乎与通过NPM安装软件包一样简单。 我们只需运行以下命令：


```
$ truffle install <package name>
```

我们还可以在特定版本上安装软件包：

```
$ truffle install <package name>@<version>
```


与NPM一样，EthPM 版本遵循[semver](http://semver.org/)。 我们可以在[以太坊软件包管理库](http://explorer.ethpm.com/))中找到所有可用软件包的列表。



## 安装依赖

项目里可以定义一个 `ethpm.json` 文件，它可以指定项目依赖及对应的版本。 要安装 `ethpm.json` 文件中列出的所有依赖，可以运行：


```
$ truffle install
```

有关 `ethpm.json` 文件的更多详细信息，请参阅下面的[包配置](#package-configuration)。

## 使用安装的合约


已安装的软件包将放在项目文件夹中的 `installed_contracts` 目录中。 如果不存在`installed_contracts` 目录，则会为我们创建。 这个文件夹就像用 NPM 处理 `node_modules` 文件夹一样 - 也就是说，你不应该编辑里面的内容，除非确切的知道自己在做什么。:)

安装的软件包可以在 测试、迁移、合约文件中 通过 `import` 或 `require` 及合约的名称来引入。
例如，以下Solidity合约将从 `owned `包导入 `owned.sol` 文件：


```javascript
pragma solidity ^0.5.0;

import "owned/owned.sol";

contract LBCContract is owned {
  // ...
}
```


同样，以下迁移文件可以使用 `ens` 包中的 `ENS.sol` 合约：


File: `./migrations/2_deploy_contracts.js`

```javascript
var ENS = artifacts.require("ens/ENS");
var MyContract = artifacts.require("MyContract");

module.exports = function(deployer) {

// 如果ENS不存在时进行部署。
// 如果我们在测试网络上并且ENS不存在，将进行部署。

  deployer.deploy(ENS, {overwrite: false}).then(function() {
    return deployer.deploy(MyContract, ENS.address);
  });
};
```

请注意，在上面的迁移中，我们使用 `ens` 包并根据ENS是否已有地址来有条件地部署ENS合约。 这是[deployer](https://learnblockchain.cn/docs/truffle/getting-started/running-migrations.html#deployer-deploy-contract-args-options)为我们提供的一种奇特技巧，可以更轻松地编写依赖于网络存在的迁移。

在这种情况下，如果我们在Ropsten网络上运行迁移，则此迁移**不会**部署 `ENS` 合约，因为（在撰写本文时）Ropsten 网络已经部署了规范的 `ENS` 合约，我们不需要部署自己的。 但是，如果我们正在针对不同的网络或测试网络运行部署，那么我们希望部署 `ENS` 合约，以便我们可以使用依赖的合约。


## 发布自己的软件包

发布我们自己的软件包与安装一样简单，像 NPM 一样，需要更多配置。


### Ropsten, Ropsten, Ropsten

以太坊软件包管理库（Package Registry）目前存在于Ropsten测试网络中。 要发布自己的软件包，我们需要设置自己的Ropsten配置，因为需要用它签名交易。


在这个例子中，我们将使用 Infura 来发布包，需要使用 `truffle-hdwallet-provider` 模块以及Ropsten网络账号的助记符。 首先，在项目目录中通过NPM安装 `truffle-hdwallet-provider` ：


```
$ npm install truffle-hdwallet-provider --save
```

编辑配置文件 `truffle.js` ：
File: 

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

// 12-word mnemonic
var mnemonic = "opinion destroy betray ...";

module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*" // Match any network id
    },
    ropsten: {
      provider: () => 
          new HDWalletProvider(mnemonic, "https://ropsten.infura.io/v3/YOUR-PROJECT-ID"),
      network_id: 3 // official id of the ropsten network
    }
  }
};
```

```eval_rst
.. _Package configuration:
```

### 包配置

与NPM一样，EthPM的配置选项文件名为 `ethpm.json` 。 此文件和 Truffle 配置文件在一个目录下，它为Truffle提供发布包所需的所有信息。 我们可以在[配置](https://learnblockchain.cn/docs/truffle/reference/configuration.html)部分中查看所有的配置项。


文件: `ethpm.json`

```javascript
{
  "package_name": "adder",
  "version": "0.0.3",
  "description": "Simple contract to add two numbers",
  "authors": [
    "Tim Coulter <tim.coulter@consensys.net>"
  ],
  "keywords": [
    "ethereum",
    "addition"
  ],
  "dependencies": {
    "owned": "^0.0.1"
  },
  "license": "MIT"
}
```

### 命令

配置完成后，发布很简单：

```
$ truffle publish
```

我们将看到与下面类似的输出，并确认包已成功发布。


```
$ truffle publish
Gathering contracts...
Finding publishable artifacts...
Uploading sources and publishing to registry...我们
+ adder@0.0.3
```

### 在发布之前


当我们的配置有有不想发布的 [artifacts](https://learnblockchain.cn/docs/truffle/getting-started/compiling-contracts.html#artifacts), 可以在发布之前运行：


```
$ truffle networks --clean
```

这里可以参考更多 [命令](https://learnblockchain.cn/docs/truffle/reference/truffle-commands.html#networks) 使用。


