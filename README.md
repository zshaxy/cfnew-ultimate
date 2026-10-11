# CFnew Ultimate - 终极版本

基于 **byJoey/cfnew v3.0** 为基底，移植 **cmliu/edgetunnel** 核心优势功能的终极版本 Cloudflare Workers 代理。

## 版本信息

- **基底**: byJoey/cfnew v3.0 (9055行)
- **移植来源**: cmliu/edgetunnel v2.1 (6645行)
- **终极版本**: 9435行 (+380行)
- **语法检查**: node --check 通过

## 移植功能

### P0 - 预加载竞速
- **DoH查询**: DNS wire format编码 + POST发送dns-message + 指针压缩解析
- **构建预加载竞速候选列表**: 并发查询A/AAAA记录，按拨号上限选取IP列表
- **并发打开候选连接**: Promise.any竞速连接，最快者胜出
- **环境变量**: `PRELOAD_RACE_DIAL=1` 开启

### P0 - 连接世代管理
- **开始TCP连接世代**: 世代递增 + 旧连接自动关闭 + 下行流停止并刷新
- 避免旧连接占用资源，提升稳定性

### P1 - 运营商适配
- **识别运营商**: ASN映射 + 关键词匹配 (ct/cu/cmcc/cf)
- **中国移动自动降单路**: 避免移动网络并发问题
- **环境变量**: `TCP_CONCURRENT_DIAL=2` 配置并发数

### P1 - GrainTCP 上行优化
- **创建Grain收纳器**: 容量控制 + 合包缓冲 + 队列管理
- **创建上行Grain合包流**: 目标20KB合包 + 定时器冲刷 + 串行写入
- 提升上行传输效率

### P2 - SS协议 (已跳过)
- cfnew 现有 VLESS + Trojan + xhttp 三种协议已覆盖主流需求
- SS协议在 Cloudflare Workers 上使用场景较少

## 环境变量

| 变量名 | 默认值 | 说明 |
|--------|--------|------|
| `PRELOAD_RACE_DIAL` | `false` | 预加载竞速拨号开关 (`1`/`true` 开启) |
| `TCP_CONCURRENT_DIAL` | `2` | TCP并发拨号数 (中国移动自动降为1) |
| `PROXY_CONCURRENT_DIAL` | `1` | 反代并发拨号数 |

## 原有功能 (cfnew v3.0)

- 多协议支持: VLESS / Trojan / xhttp
- 内置延迟测试: 1-50线程并发测试
- 地区筛选: 10个地区可选
- API管理: RESTful API动态管理优选IP
- 图形化面板: KV存储配置，改完立即生效
- ECH: Encrypted Client Hello
- 出站代理: SOCKS5 / HTTP / HTTPS
- 订阅转换: 内置生成 Clash/Sing-box/Surge 等配置
- 多语言: 中文 / 波斯语

## 文件结构

```
cfnew-ultimate/
├── _worker.js          ← 终极版本 Worker (9435行)
├── cfnew_base.js       ← cfnew v3.0 原始源码 (9055行)
├── edgetunnel_ref.js   ← edgetunnel v2.1 参考源码 (6645行)
└── README.md           ← 本文件
```

## 部署方式

与 cfnew 完全相同：

1. 在 Cloudflare Workers 中创建新 Worker
2. 将 `_worker.js` 内容粘贴到 Worker 编辑器
3. 设置环境变量 (至少 `u` 为 UUID)
4. 绑定 KV 命名空间 (变量名 `C`)
5. 部署

## 致谢

- **byJoey/cfnew**: 基底项目，优选机制最强
- **cmliu/edgetunnel**: 移植来源，通道技术最强
