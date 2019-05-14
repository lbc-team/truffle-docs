# 调试合约

Truffle 集成了一个调试器，以便我们可以调试合约进行的交易。 此调试器和传统开发环境中使用的命令行调试程序有点像。


## 概述

调试区块链上的交易与调试传统应用程序（例如，用C++或Javascript编写的应用程序）不同。 在区块链上调试交易时，没有实时运行代码; 相反，我们将逐步执行该交易的历史执行，并将该执行映射到其关联的代码上。
这为调试提供了许多自由，因此我们可以随时调试任何交易，只要我们拥有交易与之交互的合约的代码和工件(artifacts)即可。 可以将这些代码和工件（artifacts）类似于传统调试器所需的调试符号。


要调试交易，需要以下几个条件：

* Truffle 4.0 及以上；
* 所要调试区块链上的交易哈希值；
* 交易对应的合约源代码和工件(artifacts)。

注意，只要存在于链上交易，我们都可以调试它， 即使交易发生异常或者gas耗尽，也没关系。

## 调试命令

要使用，获取要调试的交易hash，运行以下命令启动调试器：

```
$ truffle debug <transaction hash>
```

使用以“0x8e5dadfb921dd ...”开头的交易为例，该命令为：

```
$ truffle debug 0x8e5dadfb921ddddfa8f53af1f9bd8beeac6838d52d7e0c2fe5085b42a4f3ca76
```

这将启动下面描述的调试界面。

## 调试器界面

![Truffle 调试器界面](https://img.learnblockchain.cn/2019/15578231135557.jpg)

启动调试器后，打开的交互界面和其他类型应用程序调试器差不多。 在它启动时，将看到以下内容：

* 在此交易过程中处理或创建的地址列表。 
* 调试器所支持的调试命令列表。 
* 以及交易的初始入口点，包括合约源文件和代码预览。 

`enter` 回车键设置为执行最后输入的命令。 当调试器启动时，在执行期间 `enter` 键被设置为步进到源代码的下一个逻辑元素（即，由以太坊虚拟机确定的下一个表达式或语句）。 此时，我们可以按 `enter` 单步执行交易，或者输入一个可用命令来更详细地分析交易。 下面是命令列表详述。


### (o) 跳过（step over）

此命令跳过当前行（即当前在Solidity源文件中语句或表达式的位置）， 如果我们不想在当前行上进入函数调用或合约创建，或者我们想快速跳转到源文件中的特定点，请使用此命令。

### (i) 进入（step into）

此命令进入当前所在的函数调用或合约创建。 使用此命令跳转到该函数内，并快速开始调试其中存在的代码。

### (u) 跳出（step out）

此命令退出当前运行的函数。 如果这是交易的入口点，使用此命令会快速返回到调用函数，或结束交易的执行。

### (n) 下一步（step next）

此命令将执行源代码中的下一个逻辑语句或表达式。 例如，在虚拟机可以评估完整表达式之前，需要首先评估子表达式。 如果要分析虚拟机评估的每个逻辑项，请使用此命令。


### (;) 单步指令（step instruction）

此命令允许我们逐步执行虚拟机评估的每个单独指令。 如果要了解 Solidity 源代码创建的低级字节码，这个命令非常有用。 使用此命令时，调试器还将在评估指令时打印出堆栈数据。

### (p) 打印指令（print instruction）

此命令打印当前指令和堆栈数据，但不会跳到下一条指令。 当我们使用上面调试命令导航到一个交易语句时，希望查看当前指令和堆栈数据时，就可以使用此命令。

### (h) print this help

打印可用调试命令列表。

### (q) 退出

退出调试器。

### (r) 重置（reset）

将调试器重置为交易的开头。


### (b) 设置断点（set a breakpoint）

此命令允许我们为任何源文件中的任何行设置断点（请参阅下面的[添加和移除断点示例](#adding-and-removing-breakpoints) ）。 命令后面可以接一个行号、或相对（当前）行号、 或者可以简单地在当前所在行添加断点。


### (B) 移除断点（大写的B）

此命令允许我们删除任何现有断点，方法和添加断点一样（请参阅下面的[添加和移除断点示例](#adding-and-removing-breakpoints) ）。 键入 `B all` 删除所有断点。


### (c) 跳到下一个断点

此命令将代码继续执行，直到到达下一个断点或执行到最后一行。


### (+) 添加一个监视表达式

使用 `+:<expression>` 添加一个监视表达式， 这样在每次执行的时候，都可以看到监视表达式的值。

译者注：比如在 [链上记事本](https://learnblockchain.cn/2019/03/30/dapp_noteOnChain/) , 我们要调试 `addNote(string memory note)` 函数, 就可以使用 `+:note`:

```
debug(development:0xd2acb9c3...)> +:note
```

来查看note的内容。


### (-) 移除监视表达式

使用 `-:<expression>` 删除监视表达式


### (?) 列出所有的监视表达式

此命令将显示所有当前监视表达式的列表。

### (v) 显示变量

此命令将显示当前变量及其值。 并非所有类型的数据都支持。


```eval_rst
.. _Adding and removing breakpoints:
```

## 添加和移除断点

以下是添加和删除断点的一些示例。 注意添加（小写'b'）和删除（大写'B'）之间的区别。


```
MagicSquare.sol:

11:   event Generated(uint n);
12:
13:   function generateMagicSquare(uint n)
      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

debug(develop:0x91c817a1...)> b 23
Breakpoint added at line 23.

debug(develop:0x91c817a1...)> B 23
Breakpoint removed at line 23.

debug(develop:0x91c817a1...)> b SquareLib:5
Breakpoint added at line 5 in SquareLib.sol.

debug(develop:0x91c817a1...)> b +10
Breakpoint added at line 23.

debug(develop:0x91c817a1...)> b
Breakpoint added at this point in line 13.

debug(develop:0x91c817a1...)> B all
Removed all breakpoints.
```
