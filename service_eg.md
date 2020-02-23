# Service 示例

## 概述

在本面文档里，将给出一些已实现的 Service 示例。包括：

* [Metadata Service](./metadata_service.md)：支持链的运营方在起链前对链的相关信息进行配置。
* [Asset Service](https://huobigroup.github.io/huobi-chain/#/./asset_service)：支持用户发行自定义资产，支持转账，查询等操作。
* [RISC-V Service](https://huobigroup.github.io/huobi-chain/#/riscv_service)：支持用户用 c 语言进行合约的开发。
* [Node Manager Service](https://huobigroup.github.io/huobi-chain/#/node_manager_service)：支持动态添加、删除节点。

## Metadata Service

目前 Muta 框架仅内置一个 Service - Metadata Service，来存储链的元数据信息，以支持链的运营方在起链前对链的相关信息进行配置。

详细信息和接口请参考[Metadata Service 文档](./metadata_service)。

## Asset Service

Asset Service 是一个资产管理服务，用来支持用户发行原生资产，并且这些原生资产。

详细信息和接口请参考[Asset Service 文档](https://huobigroup.github.io/huobi-chain/#/./asset_service)。

## RISC-V Service

[RISC-V Service](https://huobigroup.github.io/huobi-chain/#/riscv_service)是一个基于 [`CKB-VM`](https://github.com/nervosnetwork/ckb-vm) 开发的虚拟机服务。

该服务内置了一个 [RISC-V](https://riscv.org/) 指令集解释器作为虚拟机。通过该服务，用户可以自由的部署和调用合约，实现强大的自定义功能。
任何支持 [RV64I]((https://riscv.org/specifications/)) 的编译器 (如 [riscv-gcc](https://github.com/riscv/riscv-gcc), [riscv-llvm](https://github.com/lowRISC/riscv-llvm), [Rust](https://github.com/rust-embedded/wg/issues/218)) 生成的可执行文件均可以作为合约使用。目前该模型支持用户用 C 语言编写合约，后面将支持更多语言。

详细信息和接口请参考[RISC-V Service 文档](https://huobigroup.github.io/huobi-chain/#/riscv_service)。

## Node Manager Service

[Node Manager Service](https://huobigroup.github.io/huobi-chain/#/node_manager_service) 是负责变更节点的共识配置，并对变更权限进行管理的服务。这些信息存储在 Metadata Service 中，在 Metadata 中可以动态变更的字段有 interval、verifier_list、 propose_ratio、 prevote_ratio、precommit_ratio、brake_ratio 。只有 admin 账户有权限进行变更操作，admin 账户的初始值写在 config/genesis.toml 配置文件中，起链后可以发交易给 Node Manager Service 进行修改。

详细信息和接口参考[Node Manager Service 文档](https://huobigroup.github.io/huobi-chain/#/node_manager_service)。

## 其他

更多的 Service 还在继续开发中，您基于 Muta 搭建的区块链开发了哪些 Service？

如果您想要把您开发的 Service 添加到该文档中供其他人参考，点击该文档顶部的 `Edit Document`， 通过提交 Pull Request 来添加相关信息，期待~