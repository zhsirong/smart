# 数据映射与核心对象定义

## 1. 文档目标

定义从邮件附件到 OCR 到 Xero 的核心字段映射关系，作为研发、测试和人工复核的共同参考。

## 2. 核心对象

| 对象 | 描述 |
|---|---|
| EmailMessage | 原始邮件对象 |
| EmailAttachment | 邮件附件对象 |
| OCRDocument | OCR 后结构化文档 |
| VoucherRecord | 标准化凭证记录 |
| XeroRecord | Xero 回写对象 |
| ChaseRecord | 催票记录 |
| ChecklistItem | 月结清单项 |

## 3. EmailMessage 字段

| 字段 | 来源 | 类型 | 说明 | 状态 |
|---|---|---|---|---|
| message_id | 邮箱 | string | 邮件唯一标识 | 待验证 |
| thread_id | 邮箱 | string | 邮件线程标识 | 待验证 |
| subject | 邮箱 | string | 主题 | 可用 |
| from_email | 邮箱 | string | 发件人邮箱 | 可用 |
| to_email | 邮箱 | string | 收件人邮箱 | 待验证 |
| sent_at | 邮箱 | datetime | 发送时间 | 可用 |
| customer_id | 映射表 | string | 客户唯一标识 | 待补充 |

## 4. EmailAttachment 字段

| 字段 | 来源 | 类型 | 说明 | 状态 |
|---|---|---|---|---|
| attachment_id | 邮箱 | string | 附件唯一标识 | 待验证 |
| filename | 邮箱 | string | 文件名 | 可用 |
| mime_type | 邮箱 | string | MIME 类型 | 可用 |
| size | 邮箱 | integer | 文件大小 | 可用 |
| file_hash | 系统计算 | string | 去重依据 | 待实现 |
| attachment_type | 系统分类 | enum | invoice / statement / receipt / other | 待实现 |

## 5. OCRDocument 字段

| 标准字段 | 说明 | 来源 |
|---|---|---|
| vendor_name | 开票方 / 供应商 | OCR |
| invoice_number | 发票号 | OCR |
| invoice_date | 开票日期 | OCR |
| due_date | 到期日 | OCR [待验证] |
| currency | 币种 | OCR |
| subtotal | 未税金额 | OCR [待验证] |
| tax_amount | 税额 | OCR [待验证] |
| total_amount | 总金额 | OCR |
| tax_code_raw | 原始税码 | OCR / 规则引擎 |
| bank_reference | 银行流水参考号 | OCR [待验证] |
| confidence_score | OCR 置信度 | OCR [待验证返回口径] |

## 6. VoucherRecord（标准化中间层）

| 字段 | 来源 | 说明 |
|---|---|---|
| voucher_id | 系统生成 | 内部主键 |
| source_message_id | EmailMessage.message_id | 来源邮件 |
| source_attachment_id | EmailAttachment.attachment_id | 来源附件 |
| customer_id | 映射表 | 客户主键 |
| document_type | attachment_type | invoice / statement / receipt |
| normalized_vendor_name | vendor_name | 标准化后名称 |
| normalized_invoice_number | invoice_number | 标准化后发票号 |
| posting_date | invoice_date | 入账日期 |
| amount | total_amount | 总金额 |
| currency | currency | 币种 |
| xero_account_code | 规则映射 | 科目编码 [待验证] |
| xero_tax_code | 规则映射 | 税码 [待验证] |
| review_status | 系统状态 | pending / reviewed / posted / failed |
| review_owner | 用户 | 复核责任人 |

## 7. Xero 映射表（初稿）

| VoucherRecord 字段 | Xero 目标字段 | 状态 |
|---|---|---|
| normalized_vendor_name | Contact / Supplier | 待验证 |
| normalized_invoice_number | InvoiceNumber / Reference | 待验证 |
| posting_date | Date | 可行 |
| amount | LineAmount / Total | 待验证 |
| currency | CurrencyCode | 可行 |
| xero_account_code | AccountCode | 待验证 |
| xero_tax_code | TaxType | 待验证 |
| source file | Attachment | 可选 |

## 8. ChaseRecord 字段

| 字段 | 说明 |
|---|---|
| chase_id | 催票记录主键 |
| customer_id | 客户 |
| cycle_month | 月结周期 |
| missing_doc_type | 缺失文件类型 |
| severity_level | L1/L2/L3 |
| suggested_email_body | 系统生成正文 |
| sent_status | draft / sent / failed |
| sent_at | 发送时间 |
| reply_status | replied / no_reply / unknown |

## 9. ChecklistItem 字段

| 字段 | 说明 |
|---|---|
| checklist_item_id | 清单项主键 |
| customer_id | 客户 |
| item_name | 清单项名称 |
| source_rule | ACRA / IRAS / Internal |
| due_date | 截止日 |
| status | not_started / in_progress / done / overdue |
| owner_role | 责任角色 |
| escalation_flag | 是否升级 |

## 10. 待补充映射表

- 客户邮箱与 customer_id 对照表
- vendor 名称标准化字典
- Xero account code 映射表
- GST / tax code 映射表
- checklist 模板结构化定义
