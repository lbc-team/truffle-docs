# 创建 Truffle 项目

To use most Truffle commands, you need to run them against an existing Truffle project. So the first step is to create a Truffle project.

You can create a bare project template, but for those just getting started, you can use [Truffle Boxes](https://truffleframework.com/boxes), which are example applications and project templates. We'll use the [MetaCoin box](https://truffleframework.com/boxes/metacoin), which creates a token that can be transferred between accounts:

1. Create a new directory for your Truffle project:

   ```shell
   mkdir MetaCoin
   cd MetaCoin
   ```

1. Download ("unbox") the MetaCoin box:

   ```shell
   truffle unbox metacoin
   ```

   <p class="alert alert-info">
   <strong>Note</strong>: You can use the `truffle unbox <box-name>` command to download any of the other Truffle Boxes.
   </p>

   <p class="alert alert-info">
   <strong>Note</strong>: To create a bare Truffle project with no smart contracts included, use `truffle init`.
   </p>

   <p class="alert alert-info">
   <strong>Note</strong>: You can use an optional `--force` to initialize the project in the current directory regardless of its state (e.g. even if it contains other files or directories). This applies to both the `init` and `unbox` commands. Be careful, this will potentially overwrite files that exist in the directory.
   </p>

Once this operation is completed, you'll now have a project structure with the following items:

* `contracts/`: Directory for [Solidity contracts](../getting-started/interacting-with-your-contracts)
* `migrations/`: Directory for [scriptable deployment files](../getting-started/running-migrations#migration-files)
* `test/`: Directory for test files for [testing your application and contracts](../testing/testing-your-contracts)
* `truffle-config.js`: Truffle [configuration file](../reference/configuration)
