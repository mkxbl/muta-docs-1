# Service 概览

Service 是 Muta 框架中用于扩展的抽象层，用户可以基于 Service 定义区块治理、添加 VM、或实现一个 dapp。Muta 当前内置了 Metadata Service，Asset Service，RISC-V Service，Node Manager Service 四个 built-in service。

* [Metadata Service](./metadata_service.md)：支持链的运营方在起链前对链的相关信息进行配置。
* [Asset Service](./asset_service.md)：支持用户发行 UDT，支持转账，查询等操作。
* [RISC-V Service](./riscv_service.md)：支持用户用 c 语言进行合约的开发，本文档还将提供一个 [demo](./contract_demo) 详细阐述开发过程。
* [Node Manager Service](./node_manager_service.md)：支持动态添加、删除节点。

