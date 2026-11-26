# 多站对象库的驻留变更与删标复制

总部是修订唯一权威。站点按 region 管理，驻留以对象的 class 为单位。epoch 标识一次策略生效期，copy 标识特定物理副本。 online 仅表示站点和总部的数据链路。收到延迟回执并不代表链路已恢复，本地读仍可独立请求。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **site**　name、region；online 表示初始连通性。
- **policy**　class、epoch 和允许存字节的 regions。
- **put**　object、class、rev、text。
- **delete**　object/rev 的总部删除标记。
- **replicate**　请求把 object/rev 复制到 site，intent 系唯一回执号并记录 epoch。
- **ack**　intent 在目的站返回 outcome、copy 或已应用的 marker。
- **purge**　针对 site/copy 请求清理，receipt 用于确认。
- **purge_ack**　receipt 返回 removed 或 unknown。
- **connect**　site 的 online 状态变化。
- **read**　site/object，min 为所需修订，可缺省。
- **restart**　服务重启。
- **inspect**　查询 object 的物理与逻辑视图。

## 示例

- policy-change-during-copy.jsonl
- delete-before-old-bytes.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会按文件统计各类参与方消息的数量。演练记录只描述各参与方交给系统的输入。
