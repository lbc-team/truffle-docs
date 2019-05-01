# 与合约进行交互

## 介绍


如果我们为了与合约进行（测试）交互而向每次都向以太坊网络进行原始请求，我们很快就会意识到编写这些请求是笨重而繁琐的。 同样，我们可能会发现管理每个请求的状态是 _复杂的_。 幸运的是，Truffle为我们处理这种复杂性，使我们与合约的互动变得轻而易举。

## 数据的读和写

以太坊网络区分将数据写入网络和从网络读取数据，在编写应用程序我们需要关注这个区别。 通常，写入数据称为**交易 transaction**，而读取数据称为 **调用 call**。 `交易`和`调用`的处理方式是截然不同的，下面介绍：

### 交易 Transactions

`交易`从改变了网络的状态。 `交易`可以像 发送 Ether 到一个帐户一样简单，也可以像执行合约函数或向网络部署新合约一样复杂。 `交易`的特征是它**写入（或更改）数据**。 一个`交易`需要耗费以太运行，称为 “gas”，`交易`同样需要（较长）时间来处理。 当我们通过`交易`执行合约的函数时，我们无法接收该函数的返回值，因为交易不会立即处理。 通常，通过交易执行的函数不会返回值，仅仅是返回一个交易ID。 可总结`交易`的特征如下：



* 消耗Gas 费用（以太）
* 会更改网络状态
* 不会立即执行（需要等待网络矿工打包）
* 没有执行返回值（只是一个交易ID）。

### 调用 Calls

`调用`则不同，`调用`依然可以在网络上执行合约代码，但不会永久更改任何数据（如状态变量）。 `调用`的特征是读取数据。 当我们通过`调用`执行合约函数时，我们可以立刻获取到返回值。 可总结`调用Call`的特点：

* 免费（不消耗 Gas）
* 不改变网络状态
* 立即执行
* 有返回值

选择使用 `交易`还是`调用` 关键是看 读取数据 还是需要写入数据。


## 什么是合约抽象

合约抽象（ Contract abstraction）是从 Javascript 与以太坊合约交互的基础和黄油。 简单说，合约抽象是一种代码封装，让我们可以轻松地与合约进行交互，从而让我们忘记在引擎盖下执行的引擎和齿轮。 Truffle 通过[truffle-contract](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract)模块使用合约抽象，下面会介绍。


在这里，我们通过一个例子 `metacoin`，来介绍合约抽象的作用，通过Truffle Boxes，执行`truffle unbox metacoin`使用MetaCoin合约，下面合约代码：


```javascript
pragma solidity ^0.4.2;

import "./ConvertLib.sol";



//这只是一个类似Coin 合约的简单例子，并不是一个标准代币合约
// 常见Token合约可参考：https://github.com/ConsenSys/Tokens

contract MetaCoin {
	mapping (address => uint) balances;

	event Transfer(address indexed _from, address indexed _to, uint256 _value);

	function MetaCoin() {
		balances[tx.origin] = 10000;
	}

	function sendCoin(address receiver, uint amount) returns(bool sufficient) {
		if (balances[msg.sender] < amount) return false;
		balances[msg.sender] -= amount;
		balances[receiver] += amount;
    emit Transfer(msg.sender, receiver, amount);
		return true;
	}

	function getBalanceInEth(address addr) returns(uint){
		return ConvertLib.convert(getBalance(addr),2);
	}

	function getBalance(address addr) returns(uint) {
		return balances[addr];
	}
}

```

 ```note::
  译者注： 这段代码其实有点旧了（不过并不影响本节要表达的意思）， 现在Solidity 升级到 0.5 以上，应该在合约里显示的标明合约是否修改状态。
 ```

除了构造函数之外，这个合约还有三个方法（`sendCoin`，`getBalanceInEth`和`getBalance`），这三种方法都可以作为`交易`或`调用`来执行。

现在让我们来看看Truffle为我们提供的名为 “MetaCoin” 的 Javascript 对象，它可以在[Truffle控制台](https://learnblockchain.cn/docs/truffle/getting-started/using-truffle-develop-and-the-console.html)访问，如：


```javascript
truffle(develop)> let instance = await MetaCoin.deployed()
truffle(develop)> instance

// outputs:
//
// Contract
// - address: "0xa9f441a487754e6b27ba044a5a8eb2eec77f6b92"
// - allEvents: ()
// - getBalance: ()
// - getBalanceInEth: ()
// - sendCoin: ()
// ...
```

注意: 合约抽象包含与合约中完全相同的函数。 它还包含一个指向 MetaCoin合约 部署版本的地址。


## 执行合约函数

使用合约抽象，我们可以轻松地在以太坊网络上执行合约函数。

### 执行交易Transactions

我们可以执行MetaCoin合约上的三个函数。 如果我们分析每一个函数，会发现`sendCoin`是唯一一个会改变网络状态的函数。 `sendCoin` 的作用是从一个帐户“发送”一些 Meta coins 到另一个帐户，这个变化是需要持续保存的。

当调用`sendCoin`时，我们需要它作为一个`交易`执行。 如在下面的示例中，使用`交易`调用的方式从一个帐户向另一个帐户发送10个币：


```javascript
truffle(develop)> let accounts = await web3.eth.getAccounts()
truffle(develop)> instance.sendCoin(accounts[1], 10, {from: accounts[0]})
```

以上代码有一些有趣的事情：

* 我们直接调用了`抽象合约`的`sendCoin`函数。 它默认使用`交易`的方式去执行，而不是使用`调用`。
* 我们还用一个对象作为第三个参数传递给`sendCoin`函数。 注意，在 Solidity 合约中的`sendCoin`函数没有第三个参数。 这是一个特殊对象，它始终可以作为最后一个参数传递给函数，该函数允许我们编辑有关`交易`的特定信息。 在这里，我们设置了`from`地址，确保此交易来自`accounts [0]`。


### 执行调用 call

继续使用MetaCoin，注意`getBalance`函数是从网络读取数据的理想选择。 它不需要进行任何更改，因为它只返回地址参数的 MetaCoin 余额。 让我们试一试：


```javascript
truffle(develop)> let balance = await instance.getBalance(accounts[0])
truffle(develop)> balance.toNumber()
```


调用会得到返回值。 注意，由于以太坊网络可以处理非常大的数字，我们会得到一个[BigNumber](https://github.com/MikeMcl/bignumber.js/)对象，然后将其转换为数字。

 ```note::
  这里返回值转换为数字，因为在此示例中数字很小。 但是如果尝试转换大于 Javascript 支持的最大整数的 BigNumber，可能会遇到错误或无法预期的行为。
 ```


### 处理交易结果

当我们进行`交易`时，我们会得到一个`result`对象，它为我们提供了大量有关`交易`的信息。


```javascript
truffle(develop)> let result = await contract.sendCoin(accounts[1], 10, {from: accounts[0]})
truffle(develop)> result
```

具体来说，我们将获得以下内容：


* `result.tx` *(string)* - 交易哈希 hash
* `result.logs` *(array)* - 解码过的事件 (日志)
* `result.receipt` *(object)* - 交易收据 receipt（包括使用的gas）

想了解更多，可参阅`truffle-contract`包中的[README](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract)。


### 捕获事件 events

通过捕获合约触发的事件，可以更深入地了解合约正在做什么。 处理事件的最简单方法是处理交易结果`result`对象中包含的`logs`数组。

如果我们显式输出第一个日志条目，我们可以看到 `sendCoin` 函数触发事件（`Transfer（msg.sender，receiver，amount）;`)的细节:


```javascript
truffle(develop)> result.logs[0]
{ logIndex: 0,
  transactionIndex: 0,
  transactionHash: '0x3b33960e99416f687b983d4a6bb628d38bf7855c6249e71d0d16c7930a588cb2',
  blockHash: '0xe36787063e114a763469e7dabc7aa57545e67eb2c395a1e6784988ac065fdd59',
  blockNumber: 8,
  address: '0x6891Ac4E2EF3dA9bc88C96fEDbC9eA4d6D88F768',
  type: 'mined',
  id: 'log_3181e274',
  event: 'Transfer',
  args:
   Result {
     '0': '0x8128880DC48cde7e471EF6b99d3877357bb93f01',
     '1': '0x12B6971f6eb35dD138a03Bd6cBdf9Fc9b9a87d7e',
     '2': <BN: a>,
     __length__: 3,
     _from: '0x8128880DC48cde7e471EF6b99d3877357bb93f01',
     _to: '0x12B6971f6eb35dD138a03Bd6cBdf9Fc9b9a87d7e',
     _value: <BN: a> } }
```

### 部署新合约


在上述所有情况中，我们一直在使用已经部署的合约抽象。 还可以使用`.new（）`函数把自己版本的合约部署到网络：


```javascript
truffle(develop)> let newInstance = await MetaCoin.new()
truffle(develop)> newInstance.address
'0x64307b67314b584b1E3Be606255bd683C835A876'
```

### 指定合约地址

如果我们已有合约的地址，则可以在该地址上创建新的抽象。


```javascript
let specificInstance = await MetaCoin.at("0x1234...");
```

### 给合约发送以太

可以简单的把 Ether 直接发送给合约地址，或是触发合约的[Fallback 函数](https://learnblockchain.cn/docs/solidity/contracts.html#fallback)。有两种方式：

1. 方法1：通过`instance.sendTransaction（）`将交易直接发送到合约。 它像执行所有可用的合约实例函数一样有效，并且和`web3.eth.sendTransaction`功能相同，但没有回调。 如果没有指定，目标地址`to`值将自动填入。


```javascript
instance.sendTransaction({...}).then(function(result) {
  // Same transaction result object as above.
});
```

2.  方法2：还可以直接调用`send`：


```javascript
instance.send(web3.toWei(1, "ether")).then(function(result) {
  // Same result object as above.
});
```

## 延伸阅读

Truffle 提供的合约抽象包含大量实用工具，可以轻松地与我们的合约进行交互。 查看[github truffle-contract](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-contract)文档，了解更多。


