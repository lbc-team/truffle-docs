# 创建 Truffle 项目工程

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



 ```note::
  可以使用一个可选的选项 `--force` 在当前目录下初始化项目，不管当前目录的状态（即使它包含了其他人文件和目录）。
  这个选项可用于 `init` and `unbox` 命令，不过要小心他会覆盖当前已经存在的文件或目录。
 ```

 在操作完成之后，就有这样的一个项目目录结构：
 
* `contracts/`: [Solidity合约](../getting-started/interacting-with-your-contracts)目录
* `migrations/`: [部署脚本文件](../getting-started/running-migrations.html#migration-files)目录
* `test/`: 测试脚本目录，参考 [如何测试合约于应用？](../testing/testing-your-contracts)
* `truffle-config.js`: Truffle [配置文件](../reference/configuration)
