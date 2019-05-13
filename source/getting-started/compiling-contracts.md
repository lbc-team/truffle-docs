# 编译合约

## 合约文件目录

所有合约都位于项目的 `contracts/` 目录中。 由于合约是用[Solidity语言](https://learnblockchain.cn/docs/solidity/)编写的，所有包含合约的文件都将具有 `.sol` 文件扩展名。 相关的 Solidity [库](https://learnblockchain.cn/docs/solidity/contracts.html#libraries)也将有一个`.sol`扩展名。

使用`truffle init`命令创建的空 Truffle [工程](../quickstart.md)会生成一个用于部署的`Migrations.sol` 合约文件。 如果我们使用 [Truffle Box](https://truffleframework.com/boxes) 来创建工程，则会有多个合约文件。


## 编译命令

要编译Truffle项目里的合约，请切换到项目工程所在根目录，然后在终端中键入以下内容：

```shell
truffle compile
```

首次运行时，将编译所有合约。 在后续运行中，Truffle将仅编译自上次编译以来有更改的合约。 如果我们想覆盖此行为，可以使用 `--all` 选项运行上面的命令。



## 构建 Artifacts

 ```note::
  译者注：Artifacts 主要是指编译的目标文件，因为中文中没有合适的对应词，保留英文。
 ```

编译的目标文件 Artifacts 将放在 `build/contracts/` 目录中，相对于项目根目录（如果该目录不存在，将创建该目录。）

这些 Artifacts 是Truffle内部工作的组成部分，它们在成功部署应用程序中起着重要作用。 **我们不应编辑这些文件**，因为这些文件将被合约编译和部署覆盖。

```eval_rst
.. _dependencies:
```

## 引入合约依赖文件

我们可以使用Solidity的 [import](https://learnblockchain.cn/docs/solidity/layout-of-source-files.html#import) 命令声明合约依赖文件。 Truffle 将以正确的顺序编译合约，并确保将所有依赖文件发送给编译器。 可以通过两种方式指定依赖关系：

### 通过文件名导入依赖文件

要从单独的文件导入合约，请将以下代码添加到Solidity源文件中：

```
import "./AnotherContract.sol";
```

这将导入 `AnotherContract.sol` 中的所有合约，这里 `AnotherContract.sol` 是在当前编写的合约目录下。

Solidity  还有其他的导入语法，可以参考[Solidity文档:导入源文件](https://learnblockchain.cn/docs/solidity/layout-of-source-files.html#import) 了解更多。


### 从外部包导入合约

Truffle支持通过[EthPM](package-management-via-ethpm.md)和[NPM](package-management-via-npm.md)安装的依赖。 可使用以下语法把依赖导入合约：


```
import "somepackage/SomeContract.sol";
```

这里，`somepackage` 表示通过EthPM或NPM安装的包，`SomeContract.sol` 表示该包提供的Solidity源文件。

请注意，在搜索从NPM安装的软件包之前，Truffle将首先从EthPM搜索已安装的软件包，因此在极少数情况下会出现命名冲突，将使用通过EthPM安装的软件包。

有关如何使用Truffle软件包管理功能的更多信息，请参阅Truffle [EthPM](package-management-via-ethpm.md)和[NPM](package-management-via-npm.md)文档。
