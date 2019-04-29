# 合约部署（Migrations）

 ```note::
  译者注：Migrations 直译”迁移“，当作为一个名词时，有时指的是用来部署的脚本文件，称之为迁移文件，作为动词会翻译成部署，请读者了解。
 ```

迁移脚本（JavaScript文件）可帮助我们将合约部署到以太坊网络。 这些文件负责暂存我们的部署任务，并且假设我们的部署需求会随着时间的推移而发生变化。 随着项目的发展，我们将创建新的迁移脚本，以进一步推动区块链的发展。 先前运行的部署记录通过特殊的 `Migrations` 迁移合约记录在链上，详细信息如下。


## 部署命令

要运行部署，请运行以下命令：

```none
$ truffle migrate
```

这将部署在项目的 `migrations` 目录中的所有迁移文件。 最简单的迁移只是一组管理部署脚本。 如果我们的迁移先前已成功运行，则 `truffle migrate` 将从上次运行的迁移开始执行，仅运行新创建的迁移。 如果不存在新的迁移，`truffle migrate` 将不会执行任何操作。 我们可以使用 `--reset` 选项从头开始运行所有迁移。 对于本地测试，确保在执行 `migrate` 之前安装并运行了 [Ganache](https://truffleframework.com/ganache)等 测试区块链。


## 脚本文件

一个简单的迁移文件，如文件名：`4_example_migration.js` ： 

```javascript
var MyContract = artifacts.require("XlbContract");

module.exports = function(deployer) {
  // 部署步骤
  deployer.deploy(MyContract);
};
```

请注意，文件名以数字为前缀，后缀为描述。 编号前缀是必需的，以便记录迁移是否成功运行。 后缀纯粹是为了人类的可读性和理解力。

 ```note::
  译者注：编号 还有记录 运行迁移文件顺序的作用。
 ```


### artifacts.require()

在迁移开始时，我们通过 `artifacts.require（）`方法告诉 Truffle 我们想要与哪些合约进行交互。 这个方法类似于Node的 `require`，但在我们的例子中，它特别返回了一个 合约抽象 contract abstraction，我们可以在其余的部署脚本中使用它。 指定的名称应与该源文件中的**合约定义的名称**相匹配。 不传递源文件的文件名，因为文件可以包含多个合约。

考虑这个示例，其中在同一源文件中指定了两个合约：

文件名: `./contracts/Contracts.sol`

```javascript

contract ContractOne {
  // ...
}

contract ContractTwo {
  // ...
}
```

通过 `artifacts.require()` 引入 `ContractTwo` 的语句像下面这样:

```
var ContractTwo = artifacts.require("ContractTwo");
```

也可以引入两个合约，语句如下:

```
var ContractOne = artifacts.require("ContractOne");
var ContractTwo = artifacts.require("ContractTwo");
```

### module.exports

所有迁移都必须通过 `module.exports` 语法导出函数。 每次迁移导出的函数都应该接受 `deployer` 对象作为其第一个参数。 此对象通过为部署智能合约提供清晰的语法以及执行部署职责（例如保存已部署的 artifacts 供以后使用）。 `deployer` 对象是用于暂存部署任务最主要接口，其API在本页底部描述。

我们的迁移功能也可以接受其他参数。 请参阅以下示例。

## 初始化迁移功能

Truffle要求我们有迁移合约（Migrations 合约）才能使用迁移功能。 此合约必须包含特定的接口，但我们可以随意编辑此合约。 对于大多数项目，此合约最初将作为第一个迁移文件进行部署，不会再次更新。 在使用`truffle init`创建新项目时，我们也会默认收到此合约。


文件: `contracts/Migrations.sol`

```
pragma solidity >=0.4.8 <0.6.0;

contract Migrations {
  address public owner;

  // A function with the signature `last_completed_migration()`, returning a uint, is required.
  uint public last_completed_migration;

  modifier restricted() {
    if (msg.sender == owner) _;
  }

  function Migrations() {
    owner = msg.sender;
  }

  // A function with the signature `setCompleted(uint)` is required.
  function setCompleted(uint completed) restricted {
    last_completed_migration = completed;
  }

  function upgrade(address new_address) restricted {
    Migrations upgraded = Migrations(new_address);
    upgraded.setCompleted(last_completed_migration);
  }
}
```

我们必须在第一次迁移中部署此合约才能利用迁移功能。 为此，需要创建以下迁移脚本文件：


文件名: `migrations/1_initial_migration.js`

```javascript
var Migrations = artifacts.require("Migrations");

module.exports = function(deployer) {
  // 任务就是 部署迁移合约
  deployer.deploy(Migrations);
};
```

这里，我们可以使用增加的编号前缀创建新的迁移，以部署其他合约并执行更多的部署步骤。

## 部署程序 Deployer

我们的迁移文件将用于部署程序 deployer 来（分阶段）部署任务。 因此，我们可以同步编写部署任务，它们将以正确的顺序执行：


```javascript
// Stage deploying A before B
deployer.deploy(A);
deployer.deploy(B);
```

或者，部署程序上的每个函数可以使用 Promise，等待上一个任务执行的部署任务完成之后执行（进入一个部署队列）：

```javascript
// Deploy A, then deploy B, passing in A's newly deployed address
deployer.deploy(A).then(function() {
  return deployer.deploy(B, A.address);
});
```

如果你发现更清晰语法也可以为部署编写为单个 promise 链， 部署API将在本页底部讨论。

## 考虑网络

可以根据网络条件，条件性地运行部署。 这是一项高级功能，因此请在继续之前先参阅[网络](../advanced/networks-and-app-deployment.md) 部分。

要有条件地运行部署步骤，在编写迁移时，加入第二个参数 `network`， 例如：

```javascript
module.exports = function(deployer, network) {
  if (network == "live") {
    // Do something specific to the network named "live".
  } else {
    // Perform a different step otherwise.
  }
}
```

## 可用账号

迁移也会通过我们的以太坊客户端和 Web3 provider 提供给我们的帐户列表，供我们在部署期间使用。 下面和 从`web3.eth.getAccounts（）`返回的完全相同的帐户列表。

```javascript
module.exports = function(deployer, network, accounts) {
  // Use the accounts within your migrations.
}
```

## 部署程序接口 Deployer API

部署程序包含许多可用于简化迁移的功能。

### deployer.deploy(contract, args..., options)

部署合约可以通过使用指定`合约对象`和`可选的合约构造函数的参数`来进行合约部署。对于单个合约很有用，DApp只存在此合约的一个实例。 它将在部署之后设置合约地址（即`Contract.address` 将等于新部署的地址），并且它将覆盖任何先前存储的地址。

 ```note::
  译者注：上面的段落的第一句 有一点拗口， 其意思是：传给 deploy 函数的可选参数 会传递给智能合约的构造函数，看下面的例子就很容易理解。
 ```


我们也可以选择传递一组合约或一组数组，以加快多个合约的部署。另外，最后一个参数是一个可选对象，它可以包含名为`overwrite`的键以及其他交易参数如 `gas` 和`from`。如果`overwrite`设置为`false`，则部署程序如果发现之前已经部署了该合约，则不会再次部署该合约。这对于由外部依赖提供合约地址的某些情况下很有用。

注意，在调用`deploy`之前，我们需要首先部署和链接合约所依赖的库。有关详细信息，请参阅下面的`链接`功能。

有关详细信息，请参阅[truffle-contract](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract) 文档。


通过下面示例会更好理解 deploy 方法:

```javascript
// 部署没有构造函数的合约
deployer.deploy(A);

//  部署合约 并使用一些参数传递给合约的构造函数。
deployer.deploy(A, arg1, arg2, ...);

// 如果合约部署过，不会覆盖
deployer.deploy(A, {overwrite: false});

// 设置gasLimit 和部署合约的账号
deployer.deploy(A, {gas: 4612388, from: "0x...."});

// 部署多个合约，一些包含参数，另一些没有。
// 这比编写三个`deployer.deploy（）`语句更快，因为部署者可以作为单个批处理请求执行部署。
deployer.deploy([
  [A, arg1, arg2, ...],
  B,
  [C, arg1]
]);

// 外部依赖示例:
//对于此示例，我们的依赖在部署到线上网络时提供了一个地址，但是没有为测试和开发等任何其他网络提供地址。
//当我们部署到线上网络时，我们希望它使用该地址，但在测试和开发中，我们需要部署自己的版本。 我们可以简单地使用`overwrite`键来代替编写一堆条件。

deployer.deploy(SomeDependency, {overwrite: false});
```

### deployer.link(library, destinations)

将已部署的库链接到合约或多个合约。 参数 `destinations` 可以是单个合约，也可以是多个合约的数组。 如果目的（即参数指定的）合约中有不依赖于链接的库，则合约将被忽略。


示例:

```javascript
// 部署库LibA，然后将LibA链接到合约B，然后部署B.
deployer.deploy(LibA);
deployer.link(LibA, B);
deployer.deploy(B);

// 链接 LibA 到多个合约
deployer.link(LibA, [B, C, D]);
```

### deployer.then(function() {...})

就像 promise 一样，可运行任意部署步骤。 使用此选项可在迁移期间调用特定的合约函数，以添加，编辑和重新组织合约数据。


示例:

```javascript
var a, b;
deployer.then(function() {
  // 创建一个新版本的 A
  return A.new();
}).then(function(instance) {
  a = instance;
  // 获取部署的 B 实例
  return B.deployed();
}).then(function(instance) {
  b = instance;
  // 通过B的setA（）函数在B上设置A的新实例地址
  return b.setA(a.address);
});
```
