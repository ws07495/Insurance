# bt_okx_api：OKX交易所Python SDK接口调用指南

## 项目概述
👉 [OKX API文档中心](https://bit.ly/okx_welcome) 提供了完善的数字资产交易接口，本文档将详细介绍如何通过 `bt_okx_api` 开发工具包实现Python语言的接口调用。本项目基于OKX官方V5版本API协议开发，支持RESTful API与WebSocket两种通信方式，适用于加密货币行情监控、量化交易系统搭建等场景。

## 环境准备

### 系统依赖
- Python运行环境：3.6及以上版本
- 推荐依赖库版本：
  - `requests`（HTTP请求库）
  - `websockets==6.0`（WebSocket通信库）

### SDK获取
1. 克隆或下载项目代码包
2. 定位到 `okx-python-sdk-api-v5` 核心模块目录

```bash
# 安装依赖库
pip install requests
pip install websockets==6.0
```

## 配置指南

### API密钥申请
👉 [API密钥管理页面](https://bit.ly/okx_welcomeaccount/users/myApi) 是获取接口权限的官方渠道，创建API时需特别注意：
1. 启用对应权限（交易/账户/行情）
2. 设置IP白名单
3. 妥善保管密钥信息

### 参数配置
在以下文件中填写认证信息：
- REST API：`example.py`
- WebSocket API：`websocket_example.py`

```python
# 密钥配置示例
api_key = "您的API_KEY"      # API访问密钥
secret_key = "您的SECRET_KEY" # 签名加密密钥
passphrase = "您的密码短语"   # 密钥验证密码
```

## 接口调用详解

### REST API使用
1. 运行 `example.py` 示例文件
2. 解除对应接口的注释代码
3. 配置 `flag` 参数选择实盘/模拟盘：
   - 实盘：`flag = "0"`
   - 模拟盘：`flag = "1"`

支持功能：
- 行情数据查询（最新价/深度簿/资金费率）
- 交易操作（下单/撤单/改单）
- 账户管理（余额查询/历史订单）

### WebSocket实时通信

#### 频道连接配置
```python
# 公共频道（无需认证）
url_public = "wss://ws.okx.com:8443/ws/v5/public?brokerId=9999"

# 私有频道（需认证）
url_private = "wss://ws.okx.com:8443/ws/v5/private?brokerId=9999"
```

#### 调用示例
```python
# 订阅公共行情数据
loop.run_until_complete(subscribe_without_login(url_public, channels))

# 订阅账户实时数据
loop.run_until_complete(subscribe(url_private, api_key, passphrase, secret_key, channels))

# 执行交易操作
loop.run_until_complete(trade(url_private, api_key, passphrase, secret_key, trade_param))
```

### 进阶功能
- HTTP/2协议支持：详见 `http2_example.py`
- 多线程处理：建议使用 `asyncio` 协程框架
- 错误码处理：特别注意 `code=1006` 网络异常

## 常见问题解答

**Q：Python版本要求是否必须3.6以上？**  
A：建议使用Python 3.6+版本，新版本的async/await语法能显著提升WebSocket处理效率，低版本需自行适配异步框架。

**Q：WebSocket连接频繁断开如何处理？**  
A：请检查：
1. 网络防火墙设置
2. API权限配置
3. 参考[官方文档](https://bit.ly/okx_welcomedocs-v5/zh/)第4.6节心跳包机制
4. 升级到websockets 6.0以上版本

**Q：如何区分实盘与模拟盘交易？**  
A：通过修改连接URL实现：
- 实盘：`wss://ws.okx.com:8443/ws/v5/`
- 模拟盘：`wss://wspap.okx.com:8443/ws/v5/`

**Q：API密钥泄露怎么办？**  
A：立即执行以下操作：
1. 登录OKX账户撤销密钥
2. 修改密码并重新申请API
3. 检查历史交易记录
4. 联系官方客服备案

## 性能优化建议

| 优化维度 | REST API方案 | WebSocket方案 |
|---------|-------------|--------------|
| 数据获取 | 轮询机制 | 事件驱动推送 |
| 延迟表现 | 100-500ms | 实时性<50ms |
| 资源占用 | 低并发 | 高并发优化 |
| 适用场景 | 单次查询 | 实时行情监控 |

## 故障排查资源
- 官方API文档：
  👉 [中文文档](https://bit.ly/okx_welcomedocs-v5/zh/)
  👉 [英文文档](https://bit.ly/okx_welcomedocs-v5/en/)
- 开发者社区：
  - Python异步框架：`asyncio` 官方文档
  - WebSocket库：`websockets` GitHub仓库
- 常见错误：
  - `code=1006` 网络中断解决方案：[错误排查指南](https://github.com/aaugustin/websockets/issues/587)

通过本指南的配置实践，开发者可快速搭建基于OKX交易所的自动化交易系统。建议配合OKX官方沙盒环境进行测试，确保接口调用逻辑的稳定性与安全性。