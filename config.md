# 配置说明

默认的创世块和配置样例在 `config` 文件夹中，此处对其中的一些字段进行说明。

## 创世块

`genesis.toml`:

```toml
timestamp = 0
prevhash = "44915be5b6c20b0678cf05fcddbbaa832e25d7e6ac538784cd5c24de00d47472"

[[services]]
name = "asset"
payload = '''
{
    "id": "f56924db538e77bb5951eb5ff0d02b88983c49c45eea30e8ae3e7234b311436c",
    "name": "Muta Token",
    "symbol": "MT",
    "supply": 1000000000,
    "issuer": "f8389d774afdad8755ef8e629e5a154fddc6325a"
}
'''

[[services]]
name = "metadata"
payload = '''
{
    "chain_id": "b6a4d7da21443f5e816e8700eea87610e6d769657d6b8ec73028457bf2ca4036",
    "common_ref": "703873635a6b51513451",
    "timeout_gap": 20,
    "cycles_limit": 999999999999,
    "cycles_price": 1,
    "interval": 3000,
    "verifier_list": [
        {
            "bls_pub_key": "04188ef9488c19458a963cc57b567adde7db8f8b6bec392d5cb7b67b0abc1ed6cd966edc451f6ac2ef38079460eb965e890d1f576e4039a20467820237cda753f07a8b8febae1ec052190973a1bcf00690ea8fc0168b3fbbccd1c4e402eda5ef22",
            "address": "f8389d774afdad8755ef8e629e5a154fddc6325a",
            "propose_weight": 1,
            "vote_weight": 1
        }
    ],
    "propose_ratio": 15,
    "prevote_ratio": 10,
    "precommit_ratio": 10,
    "brake_ratio": 7
}
'''

[[services]]
name = "node_manager"
# private key of this admin:
# 2b672bb959fa7a852d7259b129b65aee9c83b39f427d6f7bded1f58c4c9310c2
payload = '{"admin": "0xcff1002107105460941f797828f468667aa1a2db"}'
```

`services` 为各个 service 的初始化参数。

各 service 的初始化参数说明：
- `asset`: 如果链需要发行原生资产，可以参考上面的例子填写，否则可以去掉
- `metadata`: 链的元数据，必须填写
  - `chain_id`: 链唯一 id
  - `common_ref`: BLS 签名需要
  - `timeout_gap`: 交易池能接受的最大超时块范围。用户在发送交易的时候，需要填写 `timeout` 字段，表示块高度超过这个值后，如果该交易还没有被打包，则以后都不会被打包，这样可以确保之前的某笔交易超时后一定会失败，避免用户的交易很长时间未被打包后换 `nonce` 重发交易，结果两笔交易都上链的情况。当用户填写的 `timeout` > `chain_current_height` + `timeout_gap` 时，交易池会拒绝这笔交易。
  - `cycles_limit`: 区块最大 `cycle` 限制
  - `cycles_price`: 最小 cycle 价格，目前没有使用
  - `interval`: 出块间隔，单位为 ms
  - `verifier_list`: 共识列表
    - `bls_pub_key`: 节点的 BLS 公钥
    - `address`: 节点的地址
    - `propose_weight`: 节点的出块权重。如果有四个共识节点，出块权重分别为 `1, 2, 3, 4`，则第一个节点的出块概率为 `1 / (1 + 2 + 3 + 4)`。投票权重的逻辑类似。
    - `vote_weight`: 节点的投票权重
  - `propose_ratio`: propose 阶段的超时时间与出块时间的比例。例如 `propose_ratio` 为 5, `interval` 为 3000，则 propose 阶段的超时时间为 `15 / 10 * 3000 = 4500`，单位均为毫秒。
  - `prevote_ratio`: prevote 阶段的超时时间与出块时间的比例
  - `precommit_ratio`: precommit 阶段的超时时间与出块时间的比例
  - `brake_ratio`: brake 阶段的超时时间与出块时间的比例
- `node_manager`:
  - 如果有共同认可的超级管理员，则将其地址填入此处，否则可以填写全零地址


## 链的运行配置

`chain.toml`:

```toml
privkey = "45c56be699dca666191ad3446897e0f480da234da896270202514a0e1a587c3f"

data_path = "./data"

[graphql]
listening_address = "0.0.0.0:8000"
graphql_uri = "/graphql"
graphiql_uri = "/graphiql"

[network]
listening_address = "0.0.0.0:1337"
rpc_timeout = 10

[[network.bootstraps]]
pubkey = "031288a6788678c25952eba8693b2f278f66e2187004b64ac09416d07f83f96d5b"
address = "0.0.0.0:1888"

[mempool]

pool_size = 20000
broadcast_txs_size = 200
broadcast_txs_interval = 200

[executor]
light = false

[logger]
filter = "info"
log_to_console = true
console_show_file_and_line = false
log_path = "logs/"
log_to_file = true
metrics = true
# you can specify log level for modules with config below
# modules_level = { "overlord::state::process" = "debug", core_consensus = "error" }
```


- `privkey`: 节点私钥，节点的唯一标识，在作为 bootstraps 节点时，需要给出地址和该私钥对应的公钥让其他节点连接；如果是出块节点，该私钥对应的地址需要在 consensus verifier_list 中
- `data_path`: 链数据所在目录
- `graphql`
  - `listening_address`: GraphQL 监听地址
  - `graphql_uri`: GraphQL 服务访问路径
  - `graphiql_uri`: GraphiQL 访问路径
- `network`:
  - `listening_address`: 链 p2p 网络监听地址
  - `rpc_timeout`: RPC 调用（例如从其它节点拉交易）超时时间，单位为秒
  - `bootstraps`: 起链时连接的初始节点信息
- `mempool`: 交易池相关配置
  - `pool_size`: 交易池大小
  - `broadcast_txs_size`: 一次批量广播的交易数量
  - `broadcast_txs_interval`: 交易广播间隔
- `executor`:
  - `light`: 设为 true 时，节点将只保存最新高度的 state
- `logger`: 日志相关配置
  - `filter`: 全局日志级别
  - `log_to_console`: 是否输出日志到 console，生产环境建议设为 false
  - `console_show_file_and_line`: 当 `log_to_console` 和本配置都置为 true 时，console 输出的日志里会包含日志打印处的文件和行数。本地通过日志调试时有用，一般可以设为 false。
  - `log_to_file`: 是否输出日志到文件
  - `metrics`: 是否输出 metrics。logger 模块中有专门的 metrics 输出函数，如有需要，可以用来输出 metrics 日志，不受全局日志级别的影响，且对应的日志会输出到专门的文件。
  - `log_path`: 会在该路径生成两个日志文件：`muta.log` 和 `metrics.log`。`metrics.log`中包含了专门的 metrics 日志，`muta.log` 中包含了其它所有 log 输出。
  - `modules_level`: 对特定模块指定不同于全局的日志级别


## 日志示例

文件中的日志均为 json 格式，方便用程序处理。其中 message 一般为一个嵌套的 json 结构，用来表达结构化信息。

```bash
$ tail logs/muta.log -n 1
{"time":"2020-02-12T17:11:04.187149+08:00","message":"update_after_exec cache: height 2, exec height 0, prev_hash 039d2f399864dba72c5b0f26ec989cba9bdcb9fca23ce48c8bc8c4398cb2ad0b,latest_state_root de37f62c1121e283ad52fe5b3e260c899f03d42da29fdfe08e82655185d9b772 state root [de37f62c1121e283ad52fe5b3e260c899f03d42da29fdfe08e82655185d9b772], receipt root [], confirm root [], cycle used []","module_path":"core_consensus::status","file":"/Users/huwenchao/.cargo/git/checkouts/muta-cad92efdb84944c1/34d052a/core/consensus/src/status.rs","line":114,"level":"INFO","target":"core_consensus::status","thread":"main","thread_id":4576796096,"mdc":{}}

$ tail logs/metrics.log -n 1
{"time":"2020-02-12T17:11:04.187240+08:00","message":"{\"timestamp\":1581498664187,\"event_name\":\"update_exec_info\",\"event_type\":\"custom\",\"tag\":{\"confirm_root\":\"56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421\",\"exec_height\":1,\"receipt_root\":\"56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421\",\"state_root\":\"de37f62c1121e283ad52fe5b3e260c899f03d42da29fdfe08e82655185d9b772\"},\"metadata\":{\"address\":\"f8389d774afdad8755ef8e629e5a154fddc6325a\",\"v\":\"0.3.0\"}}","module_path":"core_consensus::trace","file":"/Users/huwenchao/.cargo/git/checkouts/muta-cad92efdb84944c1/34d052a/core/consensus/src/trace.rs","line":24,"level":"TRACE","target":"metrics","thread":"main","thread_id":4576796096,"mdc":{}}
```