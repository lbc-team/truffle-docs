# Truffle 配置

## 配置文件位置

配置文件名为 `truffle-config.js` ，位于项目目录的根目录下。 它是Javascript文件，可以执行创建配置所需的任何代码。 它必须导出表示项目配置的对象，如下例所示：

```javascript
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*" // 匹配任何网络
    }
  }
};
```

默认配置附带开发网络的配置，运行在 `127.0.0.1:8545` 上。 还有许多其他配置选项，详情如下。


```eval_rst
.. _Resolving naming conflicts on Windows:
```

### 解决 Windows 命令名冲突


 ```warning::
   仅适用于Truffle 4 及以下版本。
 ```

在Windows上使用命令提示符时，默认配置文件名可能会导致与 `truffle` 可执行文件冲突，因此**我们可能无法在现有项目上正确运行Truffle命令**。


这是因为命令优先级在命令提示符上的工作方式。 `truffle.cmd` 可执行文件作为 npm 包的路径上，但 `truffle.js` 配置文件位于运行 `truffle` 命令的实际目录中。 而 `.js` 是默认的可接受的可执行扩展名，`truffle.js` 优先于 `truffle.cmd` ，导致意外的结果。


以下任何解决方案都可以解决此问题：

* 使用 `.cmd` 扩展名（ `truffle.cmd compile` ）显式调用可执行文件。
* 编辑系统 `PATHEXT` 环境变量并从可执行扩展列表中删除 `.JS;` 。
* 将 `truffle.js` 重命名为其他东西（`truffle-config.js`）。
* 使用 [Windows PowerShell](https://docs.microsoft.com/en-us/powershell/) 或 [Git BASH](https://git-for-windows.github.io/)， 或不会引起冲突的 shell .


## 常用配置选项

```eval_rst
.. _networks:
```

### 网络 networks

指定部署网络，以及与每个网络交互时的特定交易参数（例如，gas价格，账号地址等）。 在指定网络上进行编译和部署时，将保存并记录合约工件（artifacts）以供以后使用。
当合约抽象检测到我们的以太坊客户端连接到指定网络时，他们将使用与该网络相关联的合约工件（artifacts）来简化应用程序部署。 网络是通过以太坊的 `net_version`  RPC调用以及区块链URI来识别。

如下所示，`networks` 对象由网络名称作为键，并包含定义相应网络参数的对象。 
 `networks` 选项是必须项，如果没有网络配置，Truffle将无法部署我们的合约。 `truffle init` 提供的默认网络配置为我们提供了一个与其连接相匹配的开发网络 - 开发过程中非常有用，但不适合生产部署。 
 要将Truffle配置连接到其他网络，就需添加更多命名网络（named networks）并指定相应的网络ID。


网络名称可用于提示用户，例如它特定网络上运行迁移：


```bash
$ truffle migrate --network live
```

如:

```javascript
networks: {
  development: {
    host: "127.0.0.1",
    port: 8545,
    network_id: "*", // 匹配任何网络
    websockets: true
  },
  live: {
    host: "178.25.19.88", // 用于示例目的的随机IP（不要使用）
    port: 80,
    network_id: 1,        // 以太坊主网
    // 可选配置:
    // gas
    // gasPrice
    // from - Truffle 在进行交易是的默认发送地址
    // provider -  Truffle 用来连接以太坊网络的 web3 provider 实例
    //          - 如果是一个函数，需要返回 web3 provider 实例 (参考下文)
    //          - 如果指定了provider， host 和 port 或忽略。
    // skipDryRun: - 如果不想在实际迁移之前在本地测试运行迁移，则为true（默认为false）
    // timeoutBlocks: - 如果没有交易没有挖出，保持等待的区块数（默认为50）
  }
}
```

不管哪个网络，如果未指定交易选项，则将使用以下默认值：

* `gas`: 指定部署的 gas limit。 默认为 `4712388`。
* `gasPrice`: 指定部署的 gas价格。 默认为 `100000000000`（100香农）。
* `from`: 部署时使用的账号地址。 默认为我们的以太坊客户端提供的第一个可用帐户。
* `provider`: 默认web3提供者使用 `host` 和 `port` 指定，如 `new Web3.providers.HttpProvider("http://<host>:<port>")` 
* `websockets`: 我们需要启用此功能才能使用 `confirmmations` 监听器或使用 `.on` 或 `.once` 监听事件。 默认为 `false` 。

对于每个网络，我们可以指定 `host` / `port` 或 `provider` ，但不能同时指定两者。 如果我们需要 HTTP 提供者(Provider)，我们建议使用 `host` 和 `port` ，而如果我们需要自定义提供者(Provider)，如`HDWalletProvider`，则必须使用 `provider` 。

#### 提供者 Providers

以下网络列表由本地测试网络和 Infura 托管的 Ropsten 网络组成，两者均由 HDWalletProvider 提供。 确保在函数闭包中包装(wrap) `truffle-hdwallet` 提供者(Provider)，如下所示，以确保一次只连接一个网络。


```javascript
networks: {
  ropsten: {
    provider: function() {
       return new HDWalletProvider(mnemonic, "https://ropsten.infura.io/v3/YOUR-PROJECT-ID");
    },
    network_id: '3',
  },
  test: {
    provider: function() {
      return new HDWalletProvider(mnemonic, "http://127.0.0.1:8545/");
    },
    network_id: '*',
  },
}
```

如果指定 `host` 和 `port` 而不是 `provider` ，Truffle将使用该主机和端口创建自己的默认 HTTP 提供者(Provider)，在这种情况下，我们将无法使用自定义提供者(Provider)。

### 指定合约目录

默认(未编译合约)目录的是位于项目根目录的 `./contacts` 。 如果我们希望将合约保存在不同的目录中，则可以指定 `contracts_directory` 属性。


例如，让Truffle在编译时在 `./allMyStuff/someStuff/theContractFolder`（递归）文件中查找合约：


```javascript
module.exports = {
  contracts_directory: "./allMyStuff/someStuff/theContractFolder",
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*",
    }
  }
};
```


```note::
   除了指定相对路径外，还可以使用 globs/regular 表达式来有选择地编译合约。
```

### 指定合约构建生成目录

编译合约的默认输出目录是相对于项目根目录的 `./build/contracts` 。 这可以使用 `contracts_build_directory` 属性进行更改。

例如，将构建的合约工件（artifacts）放在 `./output/contracts` 中：

```javascript
module.exports = {
  contracts_build_directory: "./output",
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*",
    }
  }
};
```

构建的合约工件（artifacts）不需要位于项目根目录中：

```javascript
module.exports = {
  contracts_build_directory: "../../../output",
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*",
    }
  }
};
```

绝对路径也可以，但是不建议这样做，因为在另一个系统上编译时可能不存在绝对路径。 如果在Windows上使用绝对路径，请确保对路径使用双反斜杠（例如：`C:\\Users\\Username\\output`）。


### 迁移文件目录

默认迁移目录是项目根目录下的 `./migrations` 文件夹。 可以使用 `migrations_directory` 更改此设置。

如：

```javascript
module.exports = {
  migrations_directory: "./allMyStuff/someStuff/theMigrationsFolder",
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*",
    }
  }
};
```

### mocha

[MochaJS](http://mochajs.org/) 测试框架的配置选项。 此配置需要一个对象，详见Mocha的[文档](https://github.com/mochajs/mocha/wiki/Using-mocha-programmatically#set-options)。


例如:

```javascript
mocha: {
  useColors: true
}
```

## 指定编译器 

在 `compilers` 对象中，我们可以指定与Truffle使用的编译器相关的设置。

### solc

Solidity编译器设置， 支持 `solc` 的优化器（optimizer）设置。

可以指定...
+ [solc-bin](http://solc-bin.ethereum.org/bin/list.json) 上列出的所有的 solc-js 版本，需要的话，可以指定一个，Truffle 会自动获取它。
+ 一个原生编译的 solc （需要自己安装，下面的链接帮助）。
+ [这里](https://hub.docker.com/r/ethereum/solc/tags/)有发布的docker竞相的（dockerized ）solc 。
+ 本地可用的 solc 路径


Truffle 配置示例:

```javascript
module.exports = {
  compilers: {
    solc: {
      version: <string>, // 版本号或约束字符串 - 如： "^0.5.0"
                         // 也可以指定为 "native" ，表示使用 native solc
      docker: <boolean>, // 使用通过 docker 获得的版本
      settings: {
        optimizer: {
          enabled: <boolean>,
          runs: <number>   // 优化次数
        }
        evmVersion: <string> // 默认: "byzantium"
      }
    }
  }
}
```

有关详细信息，请参阅[Solidity文档 - 编译器输入输出JSON描述](https://learnblockchain.cn/docs/solidity/using-the-compiler.html#json).

### 使用外部编译器

如果要使用创建工件的更高级用法，可以使用外部编译器配置。
可以通过在Truffle配置中添加 `compilers.external` 对象来使用此功能：


```javascript
module.exports = {
  compilers: {
    external: {
      command: "./compile-contracts",
      targets: [{
        /* 编译输出 */
      }]
    }
  }
}
```


当我们运行 truffle compile 时，Truffle 将运行已配置的命令并查找 targets 指定的合约工件(artifacts)。

这个配置支持的几个主要用例：

+ 编译命令可直接输出 Truffle JSON 工件（artifacts）。
如果编译命令能直接生成工件（artifacts），或生成包含工件的所有信息的输出，请按如下方式配置目标（target）：


```javascript
module.exports = {
  compilers: {
    external: {
      command: "./compile-contracts",
      targets: [{
        path: "./path/to/artifacts/*.json"
      }]
    }
  }
}
```

Truffle 将执行我们的脚本，然后展开glob（*）并查找列出的路径中的所有.json文件，并将它们作为工件复制到 build/contracts/ 目录中。

+ 编译命令输出工件的各个部分，我们希望Truffle 生成工件（artifacts）。
上述用例可能不适用于所有用例。 我们可以将 target 配置为运行“后处理命令”，如：


```javascript
module.exports = {
  compilers: {
    external: {
      command: "./compile-contracts",
      targets: [{
        path: "./path/to/preprocessed-artifacts/*.json",
        command: "./process-artifact"
      }]
    }
  }
}
```

这将为每个匹配的 .json 文件运行 ./process-artifact ，该文件的内容作为 stdin 管道传输。 然后 ./process-artifact 命令（期望）输出一个完整的 Truffle 工件（artifact）作为 stdout。

如果想要提供路径代替文件名？ 在 taget 配置中添加 `stdin: false`。

+ 我们还可以指定合约的各个属性，并让 Truffle 自己生成工件 （artifacts）。

```javascript
module.exports = {
  compilers: {
    external: {
      command: "./compile-contracts",
      targets: [{
        properties: {
          contractName: "MyContract",
          /* other literal properties */
        },
        fileProperties: {
          abi: "./output/contract.abi",
          bytecode: "./output/contract.bytecode",
          /* other properties encoded in output files */
        }
      }]
    }
  }
}
```

指定 `properties` 和/或 `fileProperties` ，Truffle 将在构建工件时查找这些值。

要覆盖所有指定路径和运行命令的工作目录，请使用 `workingDirectory` 选项。
例如，以下将运行 `./proj/compile-contracts` 并读取  `./proj/output/contract.abi` ：


```javascript
module.exports = {
  compilers: {
    external: {
      command: "./compile-contracts",
      workingDirectory: "./proj",
      targets: [{
        fileProperties: {
          abi: "./output/contract.abi",
          bytecode: "./output/contract.bytecode",
        }
      }]
    }
  }
}
```


```eval_rst
.. _plugins:
```

## 插件 plugins

```note::
   仍处于"准系统状态"的新功能，同时还可向 Truffle 反馈改进意见！
```


为Truffle提供通过 NPM 安装（通过 dependencies 安装的）的第三方扩展的列表。

Truffle 插件目前仅支持自定义工作流命令的插件。 有关更多信息，请参阅[第三方插件命令-编写脚本](https://learnblockchain.cn/docs/truffle/getting-started/writing-external-scripts.html)。


## EthPM 配置

此配置是可选的，配置文件为 `ethpm.json` 和 `truffle.js` 在同一目录下。

### 包名：package_name

我们要发布的包的名称，包名称不能和已有 EthPM 注册表的包名相同（确保 unique）。

举例:
```javascript
package_name: "adder"
```

### 版本：version

该软件包的版本，使用[semver]（http://semver.org/）规范。

举例:
```javascript
version: "0.0.3"
```

### 描述：description

人类可读的文字说明。


举例:
```javascript
description: "Simple contract to add two numbers"
```

### 作者 authors
作者列表，可以有任何格式，但我们建议采用以下格式。

举例:
```javascript
authors: [
  "Tim Coulter <tim.coulter@consensys.net>"
]
```

### 关键字 keywords

关键字列表，使用有用的类别标记此包。

举例:
```javascript
keywords: [
  "ethereum",
  "addition"
],
```

### 依赖 dependencies

版本范围使用[semver](http://semver.org/)规范，列出软件包所依赖的EthPM软件包。

举例:
```javascript
dependencies: {
  "owned": "^0.0.1",
  "erc20-token": "1.0.0"
}
```


### 许可协议 license

用于此程序包的许可证，严格提供。


举例:
```javascript
license: "MIT",
```
