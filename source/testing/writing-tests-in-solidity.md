# 用 Solidity 写测试用例

Solidity测试“.sol”合约的时候类似Javascript的测试。当执行“truffle test”的时候，对于每一个测试合约，都会包含一个单独的测试套件。这些合约保持了所有Javascript测试的优点：比如对每一个测试套件的净室环境，直接进入你部署的合约和导入任意合约依赖的能力。除了这些特性，Truffle的Solidity测试框架还基于以下想法构建：


* Solidity测试不会扩展任意合约（比如“Test”合约）。这意味着你的测试可以尽可能地小，并且对于你写的合约具有完全的控制权。

* Solidity测试不依赖于任何断言库。Truffle提供一个默认的断言库，但是你能够随时调整它以满足你的需求。

* 你能够再任何以太坊客户端运行你的Solidity测试。

## 例子

让我们在深入之前先来看一个例子。这是一个通过`truffle unbox metacoin`演示Solidity测试功能的例子：


```javascript
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/MetaCoin.sol";

contract TestMetacoin {
  function testInitialBalanceUsingDeployedContract() {
    MetaCoin meta = MetaCoin(DeployedAddresses.MetaCoin());

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }

  function testInitialBalanceWithNewMetaCoin() {
    MetaCoin meta = new MetaCoin();

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }
}
```

这生成了以下输出：

```
$ truffle test
Compiling ConvertLib.sol...
Compiling MetaCoin.sol...
Compiling truffle/Assert.sol
Compiling truffle/DeployedAddresses.sol
Compiling ../test/TestMetacoin.sol...

  TestMetacoin
    ✓ testInitialBalanceUsingDeployedContract (61ms)
    ✓ testInitialBalanceWithNewMetaCoin (69ms)

  2 passing (3s)
```

## 测试结构

为了更好地理解发生了什么，让我们讨论一下细节。

### 断言

Your assertion functions like `Assert.equal()` are provided to you by the `truffle/Assert.sol` library. This is the default assertion library, however you can include your own assertion library so long as the library loosely integrates with Truffle's test runner by triggering the correct assertion events. You can find all available assertion functions in [Assert.sol](https://github.com/trufflesuite/truffle/blob/develop/packages/truffle-core/lib/testing/Assert.sol).

### Deployed addresses

The addresses of your deployed contracts (i.e., contracts that were deployed as part of your migrations) are available through the `truffle/DeployedAddresses.sol` library. This is provided by Truffle and is recompiled and relinked before each suite is run to provide your tests with Truffle's a clean room environment. This library provides functions for all of your deployed contracts, in the form of:

```solidity
DeployedAddresses.<contract name>();
```

This will return an address that you can then use to access that contract. See the example test above for usage.

In order to use the deployed contract, you'll have to import the contract code into your test suite. Notice `import "../contracts/MetaCoin.sol";` in the example. This import is relative to the test contract, which exists in the `./test` directory, and it goes outside of the test directory in order to find the MetaCoin contract. It then uses that contract to cast the address to the `MetaCoin` type.

### Test contract names

All test contracts must start with `Test`, using an uppercase `T`. This distinguishes this contract apart from test helpers and project contracts (i.e., the contracts under test), letting the test runner know which contracts represent test suites.

### Test function names

Like test contract names, all test functions must start with `test`, lowercase. Each test function is executed as a single transaction, in order of appearance in the test file (like your Javascript tests). Assertion functions provided by `truffle/Assert.sol` trigger events that the test runner evaluates to determine the result of the test. Assertion functions return a boolean representing the outcome of the assertion which you can use to return from the test early to prevent execution errors (as in, errors that [Ganache](/ganache) or Truffle Develop will expose).

### before / after hooks

You are provided many test hooks, shown in the example below. These hooks are `beforeAll`, `beforeEach`, `afterAll` and `afterEach`, which are the same hooks provided by Mocha in your Javascript tests. You can use these hooks to perform setup and teardown actions before and after each test, or before and after each suite is run. Like test functions, each hook is executed as a single transaction. Note that some complex tests will need to perform a significant amount of setup that might overflow the gas limit of a single transaction; you can get around this limitation by creating many hooks with different suffixes, like in the example below:

```solidity
import "truffle/Assert.sol";

contract TestHooks {
  uint someValue;

  function beforeEach() {
    someValue = 5;
  }

  function beforeEachAgain() {
    someValue += 1;
  }

  function testSomeValueIsSix() {
    uint expected = 6;

    Assert.equal(someValue, expected, "someValue should have been 6");
  }
}

```

This test contract also shows that your test functions and hook functions all share the same contract state. You can setup contract data before the test, use that data during the test, and reset it afterward in preparation for the next one. Note that just like your Javascript tests, your next test function will continue from the state of the previous test function that ran.

## Advanced features

Solidity tests come with a few advanced features to let you test specific use cases within Solidity.

### Testing for exceptions

You can easily test if your contract should or shouldn't raise an exception (i.e., for `require()`/`assert()`/`revert()` statements; `throw` on previous versions of Solidity).

This topic was first written about by guest writer Simon de la Rouviere in [his tutorial Testing for Throws in Truffle Solidity Tests](/tutorials/testing-for-throws-in-solidity-tests).  N.B. that the tutorial makes heavy use of exceptions via the deprecated keyword `throw`, replaced by `revert()`, `require()`, and `assert()` starting in Solidity v0.4.13.

Also, since Solidity v0.4.17, a function type member was added to enable you to access a function selector (e.g.: `this.f.selector`), and so, testing for throws with external calls has been made much easier:
```solidity
pragma solidity ^0.5.0;

import "truffle/Assert.sol";

contract TestBytesLib2 {
    function testThrowFunctions() public {
        bool r;

        // We're basically calling our contract externally with a raw call, forwarding all available gas, with 
        // msg.data equal to the throwing function selector that we want to be sure throws and using only the boolean
        // value associated with the message call's success
        (r, ) = address(this).call(abi.encodePacked(this.IThrow1.selector));
        Assert.isFalse(r, "If this is true, something is broken!");

        (r, ) = address(this).call(abi.encodePacked(this.IThrow2.selector));
        Assert.isFalse(r, "What?! 1 is equal to 10?");
    }

    function IThrow1() public pure {
        revert("I will throw");
    }

    function IThrow2() public pure {
        require(1 == 10, "I will throw, too!");
    }
}
```

### Testing ether transactions

You can also test how your contracts react to receiving Ether, and script that interaction within Solidity. To do so, your Solidity test should have a public function that returns a `uint`, called `initialBalance`. This can be written directly as a function or a public variable, as shown below. When your test contract is deployed to the network, Truffle will send that amount of Ether from your test account to your test contract. Your test contract can then use that Ether to script Ether interactions within your contract under test. Note that `initialBalance` is optional and not required.

```javascript
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/MyContract.sol";

contract TestContract {
  // Truffle will send the TestContract one Ether after deploying the contract.
  uint public initialBalance = 1 ether;

  function testInitialBalanceUsingDeployedContract() {
    MyContract myContract = MyContract(DeployedAddresses.MyContract());

    // perform an action which sends value to myContract, then assert.
    myContract.send(...);
  }

  function () {
    // This will NOT be executed when Ether is sent. \o/
  }
}
```

Note that Truffle sends Ether to your test contract in a way that does **not** execute a fallback function, so you can still use the fallback function within your Solidity tests for advanced test cases.
