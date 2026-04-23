# 智能会计交付助手（新加坡）交付包

> 基于 `03_srp_smart_accounting_b3_v2.md` 生成
> 版本：v0.1-draft
> 日期：2026-04-23
> 状态：Draft for Review

## 1. 交付包说明

本交付包基于 SRP 中 AG-B3-01 场景整理，目标是支撑 PoC 立项、跨团队对齐、方案评审与需求落地。

**当前原则：**
- 严格以 SRP 为主线，不擅自扩 scope
- 缺少用户提供资料或接口确认的地方，统一标记为 **[待验证]** / **[待补充]**
- 技术实现方案优先采用公开可验证、成熟的企业集成方式
- PoC 期优先围绕 **M8 + M9 + M1 + M2 + M3** 打通

## 2. 文件清单

1. `01_PRD.md` —— 产品需求文档
2. `02_PoC_Plan.md` —— PoC 范围、里程碑与验收计划
3. `03_Service_Blueprint.md` —— As-Is / To-Be 业务流程与服务蓝图
4. `04_Feature_Breakdown.md` —— 功能拆解与模块需求清单
5. `05_Wireframe_Design_Spec.md` —— 关键页面设计稿说明 / 线框需求
6. `06_API_Integration_Spec.md` —— API 与外部系统集成说明
7. `07_Data_Mapping.md` —— 核心字段映射与数据对象定义
8. `08_Decision_Rules_and_Human_Gates.md` —— 决策规则与人工审核门
9. `09_UAT_Test_Cases.md` —— UAT 测试用例与验收清单
10. `10_KPI_Measurement_Plan.md` —— KPI 口径与测量方案
11. `11_Risk_Dependency_Log.md` —— 风险与依赖跟踪表
12. `12_Open_Questions_and_FDE_List.md` —— 待确认问题总表
13. `13_Demo_Script.md` —— 演示脚本与汇报结构

## 3. 当前推荐推进顺序

### 第一阶段（必须先对齐）
- PRD
- PoC Plan
- Service Blueprint
- API Integration Spec
- Risk / Dependency Log

### 第二阶段（研发/设计可并行）
- Feature Breakdown
- Wireframe Design Spec
- Data Mapping
- Decision Rules and Human Gates

### 第三阶段（上线前）
- UAT Test Cases
- KPI Measurement Plan
- Demo Script

## 4. 使用建议

- 给业务方：优先看 `01_PRD.md`、`02_PoC_Plan.md`
- 给设计/产品：优先看 `03_Service_Blueprint.md`、`04_Feature_Breakdown.md`、`05_Wireframe_Design_Spec.md`
- 给技术/架构：优先看 `06_API_Integration_Spec.md`、`07_Data_Mapping.md`
- 给 PMO / Owner：优先看 `10_KPI_Measurement_Plan.md`、`11_Risk_Dependency_Log.md`、`12_Open_Questions_and_FDE_List.md`

## 5. 当前明显空缺

以下内容仍需你或客户进一步补充：
- InCorp 实际邮箱系统类型与 API 权限
- file.ai 在 InCorp 的实际部署方式与 API / Webhook 可用性
- Xero 写权限开通路径与时点
- 月结清单模板与 SOP
- 当前真实 KPI baseline
- L1/L2/L3 催票分级标准
- 试点客户名单
- 财务报表模板与审批链
