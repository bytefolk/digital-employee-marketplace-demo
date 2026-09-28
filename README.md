# 数字员工交易平台 · 功能图

一个可交互的功能图，说明 ByteFolk 数字员工交易平台**做什么、不做什么、以及当前实现到哪一步**。

在线查看：<https://bytefolk.github.io/digital-employee-marketplace-demo/>

## 这是什么

`bytefolk/digital-employee-platform` 是一个私有的 marketplace 控制面。这个仓库不是它的源码，
而是它的**功能图**——把买方侧 catalog / order 域的链路、模块、状态机、授权拒绝原因与平台边界
画在一页上，供评审、派工和对外展示使用。

图上的每一个元素都对应需求记录与实现中的真实对象：

- 四个域模块对应 `src/catalog/` 下的 `principal.ts` / `listing.ts` / `order.ts` / `release-authorizer.ts`
- 订单状态机对应 `ORDER_STATUSES` 与 `TERMINAL_ORDER_STATUSES`
- 六个拒绝原因码对应 `CATALOG_RELEASE_AUTHORIZATION_REASON_CODES`
- 角色与操作白名单对应 `CATALOG_ROLES` 与 `CATALOG_OPERATIONS`

## 需求记录

父记录 [#12](https://github.com/bytefolk/digital-employee-platform/issues/12)（`ready`），
下挂三条**串行链**子记录——listing 要先有已认证的主体，order 要先有 listing，因此不能并行铺开：

| 子记录 | 范围 | 承接 |
| --- | --- | --- |
| [#14](https://github.com/bytefolk/digital-employee-platform/issues/14) C1 | 租户 / 买卖方主体与 API 边界认证 | REQ-001 / AC-001 |
| [#15](https://github.com/bytefolk/digital-employee-platform/issues/15) C2 | 不可变 listing 版本与持久化 | REQ-002 / REQ-006 / AC-002 / AC-006 |
| [#16](https://github.com/bytefolk/digital-employee-platform/issues/16) C3 | 订单生命周期与真实 `ReleaseAuthorizer` | REQ-003/004/005 / AC-003/004/005 |

实现进展：[PR #13](https://github.com/bytefolk/digital-employee-platform/pull/13)。

## 范围声明（重要）

这张图同时也是一份范围声明。已落地的是**内存参考实现**——域模型、状态机、授权器与端到端闭环
均有测试覆盖；下面这些**尚未落地**，图上已逐处标注：

- **持久化与迁移**：既有 29 张表里没有 listing / order / tenant / buyer，需要新迁移；编号须避开被 M3 预留的 `005`。
- **面向买方的 HTTP API**：目前只能在进程内构造命令调用。
- **网关签名断言适配器**：租户主体沿用既有的签名断言纪律，但适配器尚未接上。
- **M3 信任权威**仍为 HOLD。本平台只产出**授权证据**，不是**计费权威**。

## 平台边界

平台是一个**可选的控制面与记账权威**，不是第二个 Agent 运行时。它不运行员工、不存包体、
不解析本地包路径、不持有 Host / 模型凭据；也不写 Credit、预留、结算、应收或任务 / 尝试 / 回执状态。

## 本地查看

```sh
# 直接打开
open index.html      # macOS
start index.html     # Windows
```

无外部依赖、无构建步骤——单文件页面，CSS 与 JS 全部内联。
