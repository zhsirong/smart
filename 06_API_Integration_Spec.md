# API 与外部系统集成说明

## 1. 文档目标

说明 AG-B3-01 在 PoC 阶段与邮箱、OCR、财务系统的集成方式，明确接口假设、认证方式、事件流和待验证项。

## 2. 集成总览

| 系统 | 作用 | 当前建议 | 状态 |
|---|---|---|---|
| InCorp 邮箱 | 邮件读取 / 可能的邮件发送 | Gmail API 或 Microsoft Graph | 待验证 |
| file.ai | OCR / 文件结构化 | file.ai 公共 API / Webhook | 部分可查，实例待验证 |
| Xero | 凭证写入 / 报表读取 | Xero Accounting API | 可行 |
| IRAS 规则库 | GST 规则引用 | 规则配置 / 内部化规则表 | 待补充 |

## 3. 邮箱集成方案

### 3.1 方案 A：Gmail API
**适用前提：** InCorp 使用 Google Workspace / Gmail 企业邮箱。

**能力目标：**
- 拉取消息列表
- 拉取邮件详情
- 读取附件
- 发送或生成邮件 [待验证]

**建议认证：** OAuth 2.0 / 服务账号代理 [待验证]

**建议流程：**
1. 拉取指定 label / folder 的新消息
2. 获取消息详情
3. 解析 MIME 结构
4. 下载附件
5. 投递到处理队列

### 3.2 方案 B：Microsoft Graph
**适用前提：** InCorp 使用 Outlook / Microsoft 365。

**能力目标：**
- 拉取消息列表
- 读取附件
- 发送催票邮件 [待验证具体 mailbox 策略]

**建议认证：** Azure AD / Entra 应用注册 + OAuth 2.0

### 3.3 邮箱接口待确认项
- 实际邮箱平台是 Gmail 还是 Outlook
- 是否使用 shared mailbox
- 是否允许 application-level access
- 是否允许自动发信
- 是否需要邮件草稿而非直接发送

## 4. file.ai 集成方案

### 4.1 目标
接收邮件附件后，调用 file.ai 进行文件分类 / OCR / 结构化抽取。

### 4.2 当前可参考的公共能力
- workspace 内 file type 列表
- 基于 API key 的访问
- `GET /files` 增量拉取能力
- [待验证] 文件上传与 webhook 回调路径

### 4.3 集成流程建议
1. 上传附件至 file.ai
2. 指定 file type / schema [待验证]
3. 轮询或接收 webhook 回调
4. 解析结构化 JSON
5. 做字段校验与置信度判定
6. 进入 Xero 写入或人工复核流程

### 4.4 待验证项
- 上传接口路径
- 回调机制
- workspace / schema 配置
- OCR 置信度返回方式
- 发票 / 银行流水 schema 是否已存在

## 5. Xero 集成方案

### 5.1 目标
将结构化凭证写入 Xero，并在需要时上传附件或读取报表。

### 5.2 范围建议
- 凭证 / invoice 创建
- 账户 / 科目表读取
- 附件上传 [可选]
- 报表拉取 [后续]

### 5.3 集成流程建议
1. 获取 Xero tenant 与认证信息
2. 读取 chart of accounts / tax codes
3. 构造 invoice / bill payload
4. 以幂等键写入
5. 写入成功后保存 Xero 记录 ID
6. 必要时上传源附件

### 5.4 待验证项
- Xero 中是写 AP bill、invoice 还是其他对象
- 租户是否可写
- tax code / account code 映射规则
- 是否要求附件回传 Xero
- 是否需要草稿态而非直接正式入账

## 6. 认证与安全

| 系统 | 建议认证 | 备注 |
|---|---|---|
| Gmail | OAuth 2.0 | 具体 scope 待根据邮箱权限确认 |
| Microsoft Graph | OAuth 2.0 / App Registration | shared mailbox 策略待验证 |
| file.ai | API Key | 以 workspace 为单位管理 |
| Xero | OAuth 2.0 | tenant 绑定与 scope 待确认 |

## 7. 编排方式建议

### 7.1 推荐
- 事件驱动 + 队列编排
- 邮件拉取服务
- OCR 编排服务
- Xero 回写服务
- 状态服务
- 审计服务

### 7.2 原因
- 邮件/OCR/Xero 天然异步
- 需要重试与幂等
- 需要将失败项送入人工队列

## 8. 关键技术约束

- 邮件正文与附件格式复杂
- 财务数据敏感，需要最小权限
- OCR 与回写存在异步时延
- 重复邮件和重复附件必须去重
- 自动发信策略需合规审查

## 9. 接口模板（占位）

### 9.1 邮件拉取
- Endpoint: [待验证]
- Method: [待验证]
- Input: mailbox / label / cursor
- Output: message list + attachment metadata

### 9.2 OCR 上传
- Endpoint: [待验证]
- Method: [待验证]
- Input: file + fileType/schema
- Output: job id / file id

### 9.3 OCR 结果获取
- Endpoint: [待验证]
- Method: [待验证]
- Input: job id / file id
- Output: structured JSON + confidence

### 9.4 Xero 写入
- Endpoint: [待验证 per object]
- Method: POST / PUT [待验证]
- Input: normalized voucher payload
- Output: xero record id

## 10. 非功能要求

- 所有外部调用必须记录 request id
- 重试次数必须可配置
- 所有写操作必须具备幂等策略
- 敏感字段脱敏后写审计日志
- 接口失败必须产生告警
