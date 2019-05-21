# Truffle 命令手册

本节将介绍Truffle应用程序中可用的每个命令。

## 使用方法

所有命令均采用以下形式：

```shell
truffle <command> [options]
```

传递没有参数相当于 `truffle help` ，将显示所有命令然后退出。
Passing no arguments is equivalent to `truffle help`, which will display a list of all commands and then exit.


## 命令列表


### 构建 build

已弃用。

```shell
truffle build
```


### 编译合约 compile

编译合约源文件。

```shell
truffle compile [--list <filter>] [--all] [--network <name>] [--quiet]
```

除非另有说明，否则将仅编译自上次编译以来已更改的合约。


选项:

* `--list <filter>`: 从solc-bin列出所有最近的稳定版本。如果指定了filter，则它将仅显示匹配的版本。 filter参数可以为：prereleases, releases, latestRelease or docker。
* `--all`: 编译所有合约，而不是仅编译自上次编译后更改的合约。
* `--network <name>`: 指定要使用的网络，保存特定于该网络的工件(artifacts)。网络名称必须存在于[配置文件](https://learnblockchain.cn/docs/truffle/reference/configuration.html)中。
* `--quiet`: 取消所有编译输出。

### config

显示是启用还是禁用分析，并提示是否切换设置。

```shell
truffle config [--enable-analytics|--disable-analytics]
```

选项:

* `--enable-analytics|--disable-analytics`: 启用或禁用分析。


### 启动控制台 console

运行具有合约抽象和命令的控制台。

```shell
truffle console [--network <name>] [--verbose-rpc]
```

通过命令行与合约进行交互的接口。此外，许多 Truffle 命令在控制台中使用（不需要 `truffle` 前缀）

需要外部以太坊客户端，例如[Ganache](https://truffleframework.com/ganache/using) 或 geth ，则使用 `truffle develop` 创建开发和测试环境的控制台。

有关详细信息，请参阅[使用控制台](https://learnblockchain.cn/docs/truffle/getting-started/using-truffle-develop-and-the-console.html) 。

选项:

* `--network <name>`: ：指定要使用的网络。网络名称必须存在于配置中。
* `--verbose-rpc`: 记录Truffle和以太坊客户端之间的通信log。


### 创建 create

用于创建新的合约，迁移和测试。

```shell
truffle create <artifact_type> <ArtifactName>
```

选项:

* `<artifact_type>`: (必须项) 创建一个新合约，迁移和测试， artifact_type 可以是：contract, migration 或 test. 创建的文件通常像这样: `contracts/ArtifactName.sol`, `migrations/####_artifact_name.js` or `tests/artifact_name.js`.
* `<ArtifactName>`: (必须项) 新工件（合约，迁移和测试）的名称。 

Camel 的命名将转换为下划线分隔的文件名，以用于迁移和测试。将自动生成迁移的编号前缀。


### 调试 debug

交互式调试区块链上的任何事务。

```shell
truffle debug <transaction_hash>
```

将在特定事务上启动交互式调试会话。允许您逐步执行每个操作并重播（replay）。有关详细信息，请参阅[调试合约](https://learnblockchain.cn/docs/truffle/getting-started/debugging-your-contracts.html) 。

 ```note::
   警告：调试命令依然是实验性的。
 ```

选项：

* `<transaction_hash>`: 用于调试的事务ID。 (必须项)


### 部署 deploy

 `migrate` 的别名. 参考 [migrate](#migrate) 。


### 控制台 develop

使用框架集成的 develop 链打开控制台

```shell
truffle develop
```

通过命令行与框架集成的 develop 链上合约进行交互的接口。此外，许多 Truffle 命令在控制台中使用（不需要 `truffle` 前缀）

如果想要现有的区块链上合约进行交互，请使用 `truffle console` 。

有关详细信息，请参阅[使用控制台](https://learnblockchain.cn/docs/truffle/getting-started/using-truffle-develop-and-the-console.html) 。


### exec

在Truffle环境中执行JS模块。

```shell
truffle exec <script.js> [--network <name>] [--compile]
```

会引入 `web3` ，根据指定的网络（如果有）设置默认提供者（provider），并在执行脚本时将引入我们的合约作为全局对象。提供的脚本必须导出Truffle可以运行的函数。

有关详细信息，请参阅[编写外部脚本](https://learnblockchain.cn/docs/truffle/getting-started/writing-external-scripts.html) 。

选项：

* `<script.js>`: 要执行的JavaScript文件。如果当前目录中不存在脚本，则可以包含路径信息。 （必须）
* `--network <name>`: 指定要使用的网络。网络名称必须存在于配置中。
* `--compile`: 在执行脚本之前编译合约。


### 帮助 help

显示所有命令的列表或特定命令的信息。

```shell
truffle help [<command>]
```

选项：

* `<command>`: 指定命令，显示指定命令的使用信息。


### 初始化工程 init

初始化新的（空的）以太坊项目

```shell
truffle init [--force]
```

在当前工作目录中创建一个新的空Truffle项目。

 ```note::
   **警告**: 较旧版本的 Truffle 使用 `truffle init bare` 来创建一个空项目。此用法已被弃用。
   基于某一个实例来创建应该使用如 `truffle unbox MetaCoin` 。
 ```



选项：

* `--force`: 无论当前工作目录的状态如何，都要初始化项目。请注意，这可能会覆盖名称冲突的现有文件。


### 安装 install

从以太坊包注册表（Ethereum Package Registry）安装包。

```shell
truffle install <package_name>[@<version>]
```

选项：

* `<package_name>`: 以太坊包注册表中列出的包的名称。 （必须）
* `@<version>`: 指定时，会安装特定版本的软件包，否则会安装最新版本。

有关详细信息，请参阅[使用EthPM进行包管理](https://learnblockchain.cn/docs/truffle/getting-started/package-management-via-ethpm.html) 。

```eval_rst
.. _migrate:
```

### migrate

运行迁移文件以部署合约。

```shell
truffle migrate [--reset] [--f <number>] [--to <number>] [--network <name>] [--compile-all] [--verbose-rpc] [--dry-run] [--interactive]
```

除非指定，否则将从上次完成的迁移开始。有关详细信息，请参阅[（迁移）合约部署](https://learnblockchain.cn/docs/truffle/getting-started/running-migrations.html) 。

选项：

* `--reset`: 从头开始运行所有迁移，而不是从上次完成的迁移中运行。
* `--f <number>`: 从特定的迁移中运行合约。该数字指的是迁移文件的前缀。
* `--to <number>`: 运行合约到特定的迁移。该数字指的是迁移文件的前缀。
* `--network <name>`: 指定要使用的网络，网络名称必须存在于配置中。
* `--compile-all`: 编译所有合约，而不是智能地选择需要编译的合约。
* `--verbose-rpc`: 记录Truffle和以太坊客户端之间的通信日志。
* `--dry-run`: 分叉（fork - 复制）指定的网络并仅执行测试迁移。
* `--interactive`: 在 dry run 之后，提示确认用户是否要继续。


```eval_rst
.. _networks:
```

### 网络 networks

显示每个网络上已部署合约的地址。

```shell
truffle networks [--clean]
```

在发布包之前使用此命令，以查看是否存在不希望发布的任何无关的网络的[工件（artifacts）](https://learnblockchain.cn/docs/truffle/getting-started/compiling-contracts.html#artifacts)。如果未指定选项，只输出当前的工件状态。

选项：

* `--clean`: 删除所有与命名网络(named network , 是指在配置文件中指明的网络)无关的网络工件。


### 操作码 opcode

打印给定合约的已编译操作码。

```shell
truffle opcode <contract_name>
```

选项：

* `<contract_name>`: 要打印操作码的合约的名称。必须是合约名称，而不是文件名。（必须）


### 发布 publish

将包发布到以太坊包注册表（Ethereum Package Registry）。

```shell
truffle publish
```

所有参数都从项目的配置文件中提取。没有参数，有关详细信息，请参阅[使用EthPM进行包管理](https://learnblockchain.cn/docs/truffle/getting-started/package-management-via-ethpm.html) 。

### 运行 run

```note::
   仍处于"准系统状态"的新功能，可向 Truffle 反馈改进意见！
```

运行第三方插件命令

```shell
truffle run <command>
```

选项：

* `<command>`: 由已安装的插件定义的命令的名称。（必须）

安装插件作为NPM包依赖项存在，参考[Truffle 配置](https://learnblockchain.cn/docs/truffle/reference/configuration.html#plugins) 了解插件
有关更多信息，请参阅[第三方插件命令](https://learnblockchain.cn/docs/truffle/getting-started/writing-external-scripts.html#third-party-plugin-commands).


### 测试 test

运行JavaScript和Solidity测试用例。

```shell
truffle test [<test_file>] [--compile-all] [--network <name>] [--verbose-rpc] [--show-events]
```

运行 `test/` 目录中的全部测试用例，或指定用例文件。有关详细信息，请参阅 [测试合约](https://learnblockchain.cn/docs/truffle/testing/testing-your-contracts.html) 。

选项：

* `<test_file>`: 要运行的测试文件的名称。如果当前目录中不存在该文件，则可以包含路径信息。
* `--compile-all`: 编译所有合约，而不是智能地选择需要编译的合约。
* `--network <name>`: 指定要使用的网络，网络名称必须存在于配置中。
* `--verbose-rpc`: 记录Truffle和以太坊客户端之间的通信日志。
* `--show-events`: 记录所有合约的事件。


### 解包 unbox

下载Truffle Box ， 它是一个预制（pre-built）的 Truffle 工程。

```shell
truffle unbox <box_name>
```

下载 [Truffle Box](https://truffleframework.com/boxes) 到当前工作目录。请参阅[可用boxes列表](https://truffleframework.com/boxes).

还可以设计和创建自己的boxes! 有关详细信息，请参阅[Truffle boxes](https://learnblockchain.cn/docs/truffle/advanced/creating-a-truffle-box.html) 。

选项：

* `<box_name>`: Truffle Box 的名称(必须 required)
* `--force`: 强制在当前目录中的解包项目，无论其状态如何。请注意，这可能会覆盖目录中存在的文件。



### 查看版本 version

显示版本号并退出。

```shell
truffle version
```

### 监视变化 watch

监视文件系统以进行更改并自动重新构建（rebuild） 项目。

```shell
truffle watch
```

此命令将启动对合约，应用程序和配置文件更改的监视。当有更改时，它将根据需要重新构建应用程序。

```note::
   警告：不推荐使用此命令。请使用外部工具来监视文件系统更改并重新运行测试。
```


