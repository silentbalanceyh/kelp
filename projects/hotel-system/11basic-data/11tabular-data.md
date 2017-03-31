# Tabular List

## 1. Comments

* **code.money**：佣金码
* **code.group**：团购码
* **code.market**：市场码
* **code.source**：来源码
* **code.shift**：班别码
* ## 2. Table List

| type（类型） | code（系统内码） | marker（显示，用户内码） | name（名称） |
| :--- | :--- | :--- | :--- |
| code.money | Rate | 按比例 | MRT-按比例 |
|  | RoomNight | 按房晚 | MRN-按房晚 |
|  | Person | 按人数 | MPS-按人数 |
|  | Fixed | 定额 | MFD-定额 |
| code.group | Timer | 限时团购 | TC-限时团购 |
|  | Package | 批量团购 | BC-批量团购 |
| code.market | Frant | 前台散客 | MFT-前台散客 |
|  | Qunar | 去哪儿网 | MQR-去哪儿网 |
|  | LY | 同程网 | MLY-同程网 |
|  | Ctrip | 携程网 | MCP-携程网 |
|  | ELong | 艺龙网 | MEL-艺龙网 |
|  | TravelAgent | 旅行社 | MTA-旅行社 |
|  | Internal | 内招 | MIL-内招 |
|  | Self | 自用 | MIL-自用 |
| code.source | Enterprise | 企业单位 | SEP-企业单位 |
|  | Goverment | 事业单位 | SGM-事业单位 |
|  | Non-Profit | 非营利组织 | SNP-非营利组织 |
|  | Overseas | 国外公司 | SOS-国外公司 |
|  | Customer | 回头客 | SCM-回头客 |
|  | Direct | 直接上门 | SDT-直接上门 |
|  | Center | 订房中心 | SCT-订房中心 |
|  | AreaSale | 地区销售 | SAS-地区销售 |
| code.shift | Morning | 早班 | MNS-早班 |
|  | Middle | 中班 | MDS-中班 |
|  | Evening | 晚班 | EVS-晚班 |
|  | Night | 夜班 | NTS-夜班 |
| card.type | Inactive |  | 临时会员卡（未激活） |
|  | Using |  | 正式会员卡（已激活） |
|  | Best |  | 白金会员卡 |
| member.status | Active |  | 已激活 |
|  | New |  | 未激活 |
|  | Verified |  | 已认证 |
|  | Locked |  | 已锁定 |
| member.type | Registered |  | 注册会员 |
|  | Silver |  | 白银会员 |
|  | Gold |  | 黄金会员 |
|  | Platinum |  | 白金会员 |
|  | Diamond |  | 钻石会员 |
| deposit.source | Cash |  | 现金 |
|  | Card |  | 刷卡 |
|  | Present |  | 赠送 |
|  | Alipay |  | 支付宝 |
|  | WeChat |  | 微信 |
| deposit.type | Direct |  | 充值 |
|  | Present |  | 赠送 |
| in.room.type | Common |  | 普通房 |
|  | Hourly |  | 钟点房 |
|  | Special |  | 特殊房 |
|  | Self |  | 自用房 |
|  | Free |  | 免费房 |
|  | Apartment |  | 公寓房 |
| partner.type | Corporation |  | 协议单位 |
|  | Vendor |  | 供应商 |
| contract.type | Company |  | 公司协议 |
|  | Agent |  | 中介协议 |
|  | Travel |  | 旅行协议 |
| employee.type | CW |  | 试用员工 |
|  | Regular |  | 正式员工 |
| inoccup.status | Created |  | 新创建 |
|  | Registered |  | 已登记 |
|  | Delay |  | 延迟 |
|  | Cancel |  | 已撤销 |
| inoccup.type | Single |  | 散客入住 |
|  | Team |  | 团队入住 |
| traveler.status | OnGoing |  | 在住宾客 |
|  | History |  | 历史宾客 |
| credential.type | First |  | 一代身份证 |
|  | Second |  | 二代身份证 |
|  | Driving.License |  | 驾驶证 |
|  | Passport |  | 护照 |
|  | Osifer |  | 军官证 |
|  | Soldier |  | 士兵证 |
|  | HK.Macau |  | 港澳通行证 |
|  | Home.Return |  | 回乡证 |
|  | Interim |  | 临时身份证 |
|  | Household |  | 户口薄 |
|  | Police |  | 警官证 |
|  | Other |  | 其他 |
| room.op.status | Taken |  | 占用房 |
|  | Empty |  | 空房间 |
|  | Leaving |  | 预离房 |
|  | Arriving |  | 预抵房 |
| room.clean.status | Dirty |  | 脏房 |
|  | Checked |  | 清洁已检查 |
|  | Unchecked |  | 清洁未检查 |
| room.status | Operation |  | 可操作 |
|  | Mind |  | 维修中 |
|  | Disabled |  | 停用房 |
|  | Self |  | 自用房 |
|  | Left |  | 预留房 |
| pay.type | Cash |  | 现金 |
|  | Credit |  | 信用卡 |
|  | Debit |  | 借记卡 |
|  | Cheque |  | 支票 |
|  | Transfer |  | 银行转账 |
|  | Net |  | 网上支付 |
| accounting.type | LiquidAssets |  | 流动资产 |
|  | NonLiquidAssets |  | 非流动资产 |
|  | LiquidLiabilities |  | 流动负债 |
|  | NonLiquidLiabilities |  | 非流动负债 |
|  | OwnerEquity |  | 所有者权益 |
|  | Cost |  | 成本 |
|  | OprRevenue |  | 营业收入 |
|  | OtherEquity |  | 其他收益 |
|  | CostTax |  | 营业成本及税金 |
|  | OtherExpense |  | 其他损失 |
|  | Expense |  | 期间费用 |
|  | IncomeTax |  | 所得税 |
| invoice.type | Order |  | 订单发票 |
|  | Deposit |  | 充值发票 |
| bill.type | PreOrder |  | 预定账单 |
|  | Consume |  | 消费账单 |
|  | RunUp |  | 挂账账单 |
|  | Knocker |  | 赊账账单 |
|  | Virtual |  | 虚拟账单 |
| accmgmt.type | Cooperation.Account |  | 协议单位账号 |
|  | Vendor.Account |  | 供应商账号 |
|  | Member.Account |  | 酒店会员账号 |
| accounting.code | Expenses |  | 费用项 |
|  | Collection |  | 代收代付 |
|  | Discount |  | 折扣款待 |
|  | Receivables |  | 收款项 |
| price.type | Standard | 挂牌价 | PSD-挂牌价 |
|  | Single | 散客执行价 | PSE-散客执行价 |
|  | Member | 会员价 | PMB-会员价 |
|  | Cooperation | 协议单位价 | PCP-协议单位价 |
|  | TravelAgent | 旅行社协议价 | PTA-旅行社协议价 |
|  | Internal | 内招价 | PIL-内招价 |
|  | HourRoom | 钟点房 | PHR-钟点房 |
|  |  |  |  |



