# 用 NPM 进行包管理

Truffle 集成 `npm` ，并且知道项目中的  `node_modules`  目录（如果存在）。 这意味着我们可以通过  `npm`  来使用和分发合约、dapps、以太坊的合约库，使我们的代码可供其他人使用，也可以使用其他代码。

## 包文件布局

使用 Truffle 创建的项目默认具有特定的目录结构，这使得它们可以作为包来使用。 虽然这种目录结构不是必需的，但如果作为通用约定（或“事实上的标准”），那么通过 NPM 分发合约和 dapp 会更加容易。

Truffle 包中最重要的目录如下：


* `/contracts` 
* `/build` (包含通过Truffle创建的  `/build/contracts`)

第一个目录是我们的合约目录，其中包含Solidity合约源文件，合约文件中允许导入其他的合约，也运行其他人导入这些合约。
第二个目录是构建目录，更具体地说是 `/build/contracts` ，它以 `.json` 文件的形式保存构建工件（artifacts），构建的 `.json` 允许其他人使用 JavaScript 无缝地与我们的合约交互（可以dapps、脚本和迁移中使用）。


## 使用包

在项目中使用包时，在使用其他合约代码时有两个地方需要注意：合约代码和 Javascript 代码（迁移文件和测试文件）。 后面为每种情况提供了示例，并讨论了利用其他合约和构建工件(artifacts)的技术。

### 安装


在本例中，我们将使用[Truffle 示例库](https://github.com/ConsenSys/example-truffle-library)，它提供了一个部署到 Morden 测试网络的简单名称注册表。 为了将它作为依赖，我们必须首先通过 `npm` 在项目中安装它：


```
$ cd my_project
$ npm install example-truffle-library
```

请注意，上面的第二个命令下载了包并将其放在 `my_project/node_modules`  目录中，这对于下面的示例很重要。使用  `npm` 进行可以参考 [npm 文档](https://docs.npmjs.com/) 。

### 在合约代码中使用包

要在合约中使用其他包的合约，可Solidity的[import声明](http://solidity.readthedocs.io/en/develop/layout-of-source-files.html?#importing-other-source-files)一样简单。 我们的导入路径不是[相对或绝对的](https://learnblockchain.cn/docs/truffle/getting_started/compiling-contracts.html#dependencies)，它表示正在寻找来自特定命名包的文件。 使用上面提到的示例库的示例为：

```
import "example-truffle-library/contracts/SimpleNameRegistry.sol";
```

由于路径不是以 `./` 开头，因此 Truffle 知道在项目的 `node_modules` 目录中查找` example-truffle-library` 文件夹。它将为我们所需合约提供路径。


### 在JavaScript中使用包

要在 JavaScript 代码中与包的合约进行交互，我们只需要 `require` 包内的 `.json` 文件，然后使用[truffle-contract](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract)模块将这些转换为可用的抽象：


```
var contract = require("truffle-contract");
var data = require("example-truffle-library/build/contracts/SimpleNameRegistry.json");
var SimpleNameRegistry = contract(data);
```

要使用这些抽象，请参阅[与合约交互](https://learnblockchain.cn/docs/truffle/getting_started/interacting-with-your-contracts.html)部分以获取更多详细信息。


### 包的部署地址

有时我们希望合约与包内先前部署的合约进行交互。 由于部署的地址存在于包的 `.json` 文件中，因此必须执行额外的步骤才能将这些地址放入合约中。 为此，请使合约接受依赖合约的地址，然后使用迁移。 以下是项目中存在依赖合约示例以及迁移示例：


合约文件: `MyContract.sol`

```javascript
pragma solidity ^0.4.13;

import "example-truffle-library/contracts/SimpleNameRegistry.sol";

contract MyContract {
  SimpleNameRegistry registry;
  address public owner;

  function MyContract {
    owner = msg.sender;
  }

  //使用了 已经部署的 registry 包
  function getModule(bytes32 name) returns (address) {
    return registry.names(name);
  }

  // Set the registry if you're the owner.
  function setRegistry(address addr) {
    require(msg.sender == owner);

    registry = SimpleNameRegistry(addr);
  }
}

```

迁移文件: `3_hook_up_example_library.js` 

```javascript
// Note that artifacts.require takes care of creating an abstraction for us.
var SimpleNameRegistry = artifacts.require("example-truffle-library/SimpleNameRegistry");

module.exports = function(deployer) {
  // Deploy our contract, then set the address of the registry.
  deployer.deploy(MyContract).then(function() {
    return MyContract.deployed();
  }).then(function(deployed) {
    return deployed.setRegistry(SimpleNameRegistry.address);
  });
};
```

### 发布之前

当我们的配置有有不想发布的 [artifacts](https://learnblockchain.cn/docs/truffle/getting-started/compiling-contracts.html#artifacts), 可以在发布之前运行：


```
$ truffle networks --clean
```

这里可以参考更多 [命令](https://learnblockchain.cn/docs/truffle/reference/truffle-commands.html#networks) 使用。

