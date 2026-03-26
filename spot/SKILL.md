---
{
  "name": 'Coinw Spot Skill',
  "description": 'Coinw 现货 REST API 技能：覆盖交易市场数据、下单/撤单、订单查询、账户余额与资产转账。',
  "metadata": {"version": "1.2.0","author": "Coinw"}
}
---

# Coinw Spot Skill

Coinw 现货 REST API 技能：覆盖交易市场数据、下单/撤单、订单查询、账户余额与资产转账。

## 主要特色
- 市场数据：交易品种、24 小时摘要、订单簿、最近成交、K 线、热门成交量
- 交易操作：下单、取消订单/全部订单
- 查询与账户：订单查询、交易历史、现货余额、资产转账

## 快速参考

### 市场信息

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1.1 | 获取交易品种信息 | `/api/v1/public?command=returnSymbol` | 此接口提供所有现货交易品种的详细信息，包括最低和最高订单价格、订单数量和价格精度。 | GET | 公共 | — | currencyPair, currencyBase, currencyQuote, maxBuyCount, minBuyCount, pricePrecision, countPrecision, minBuyAmount, maxBuyAmount 等12项 |
| 1.2 | 获取所有交易品种24小时交易摘要 | `/api/v1/public?command=returnTicker` | 此接口提供交易所所有可用交易品种的24小时交易摘要，包括最新成交价、最高买价、最低卖价、24小时交易量等关键指标。 | GET | 公共 | — | id, last, lowestAsk, highestBid, percentChange, isFrozen, high24hr, low24hr, baseVolume |
| 1.4 | 获取订单簿 | `/api/v1/public?command=returnOrderBook` | 此接口允许用户查询指定交易品种的现货订单簿数据。用户可以选择查询5级或20级的买单和卖单。 | GET | 公共 | size, symbol | asks, quantity, price, bids, quantity, price, pair |
| 1.5 | 获取最近成交 | `/api/v1/public?command=returnTradeHistory` | 此接口允许查询指定交易品种的最近成交数据，包括成交数量、成交价格、总成交金额、成交时间、成交方向和成交记录ID。 | GET | 公共 | symbol | id, type, price, amount, total, time, pair |
| 1.6 | 获取K线 | `/api/v1/public?command=returnChartData` | 此接口允许查询指定交易品种的K线（蜡烛图）数据，包括开盘价、收盘价、最高价、最低价和交易量。 | GET | 公共 | currencyPair, period | date, high, low, open, close, volume, pair |
| 1.7 | 获取热门交易品种24小时交易量 | `/api/v1/public?command=return24hVolume` | 此接口允许查询交易所热门交易品种的24小时交易量。它提供主要交易品种的汇总交易量数据，包括BTC、ETH、LTC和USDT，以及总市场交… | GET | 公共 | — | data, totalETH, totalUSDT, totalBTC, ETH_USDT, ETH, USDT, LTC_CNYT, LTC 等28项 |

### 下单

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2.1 | 下单 | `/api/v1/private?command=doTrade` | 此接口允许用户通过指定订单类型、数量、价格和外部交易号来下现货交易订单。 | POST | 私有 | api_key, sign, symbol, type, amount, rate, isMarket, out_trade_no | orderNumber |
| 2.2 | 取消订单 | `/api/v1/private?command=cancelOrder` | 此接口允许用户通过指定订单ID取消未成交的现货订单。 | POST | 私有 | api_key, sign, orderNumber | clientOrderId |
| 2.3 | 取消所有订单 | `/api/v1/private?command=cancelAllOrder` | 此接口允许用户取消指定交易品种的所有未成交订单。 | POST | 私有 | api_key, sign, currencyPair | msg |

### 查询订单

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 3.1 | 获取当前订单 | `/api/v1/private?command=returnOpenOrders` | 此接口允许用户获取指定交易品种的所有当前未成交订单，提供订单ID、交易时间、数量和状态等详细信息。 | POST | 私有 | api_key, sign, currencyPair, startAt, endAt | orderNumber, date, startingAmount, total, type, prize, success_count, success_amount, status |
| 3.2 | 获取历史订单 | `/api/v1/private?command=getUserTrades` | 此接口允许用户检索所有交易品种的历史订单。它支持通过特定交易品种进行可选过滤，每个请求最多返回100条记录。用户可以使用基于时间的过滤器… | POST | 私有 | api_key, sign, symbol | tradeId, orderId, price, size, side, orderType, time, fee, before 等10项 |
| 3.3 | 批量获取历史订单 | `/v1/private?command=getBatchHistoryOrders` | 按订单 ID 列表批量查询近三个月历史订单（原文档 api 说明占位，细节见 api-doc 3.3）。 | POST | 私有 | api_key, sign, orderIds | data, orderId, date, side, type, dealSize, dealFunds, dealAvgPrice, fee 等16项 |
| 3.4 | 获取订单详情 | `/api/v1/private?command=returnOrderTrades` | 此接口通过指定订单ID获取订单的详细信息。 | POST | 私有 | api_key, sign, orderNumber | tradeID, currencyPair, type, amount, success_amount, total, success_total, fee, date 等10项 |
| 3.5 | 获取订单状态 | `/api/v1/private?command=returnOrderStatus` | 此接口允许用户通过提供相应的订单 ID 来查询订单的状态。它返回的信息包括交易品种、方向、数量、执行状态以及时间戳。 | POST | 私有 | api_key, sign, orderNumber | currencyPair, type, total, startingAmount, status, date |
| 3.6 | 获取交易历史 | `/api/v1/private?command=returnUTradeHistory` | 此接口返回指定交易品种的成交记录。 | POST | 私有 | api_key, sign, currencyPair | tradeID, type, amount, success_amount, total, success_count, fee, prize, date 等11项 |

### 账户信息

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 4.1 | 获取现货账户余额 | `/api/v1/private?command=returnBalances` | 此接口检索用户现货账户的可用余额，包括不同交易品种的所有持有量。 | POST | 私有 | api_key, sign | data, msg |
| 4.2 | 获取现货账户总余额 | `/api/v1/private?command=returnCompleteBalances` | 此接口允许用户检索其现货交易账户的完整余额详情。这里的“完整” 指的是数据维度的全面性，既包括可用余额，还包括被挂单冻结的余额，提供了账… | POST | 私有 | api_key, sign | data, available, onOrders |
| 4.7 | 资产转账 | `/api/v1/private?command=spotWealthTransfer` | 此API允许在现货账户和资金账户之间进行资产转账，以实现高效的资金管理。 | POST | 私有 | api_key, sign, accountType, targetAccountType, bizType, coinCode, amount | data, msg |

## 公共参数描述和枚举值

### 鉴权与 URL
- Base URL：`https://api.coinw.com`。
- 公共 REST：`GET/POST https://api.coinw.com/api/v1/public?command=...`。
- 私有 REST：通常 `POST https://api.coinw.com/api/v1/private?command=...`，并在 query/body 中附带 `api_key`、`sign`（MD5，详见 Reference）。
- 部分接口文档示例路径为 `/v1/private?command=...`，与 `/api/v1/private` 并列出现，以实现为准。
### `command` 取值（本表已覆盖的私有/公共接口）
`cancelAllOrder`, `cancelOrder`, `doTrade`, `getBatchHistoryOrders`, `getUserTrades`, `return24hVolume`, `returnBalances`, `returnChartData`, `returnCompleteBalances`, `returnOpenOrders`, `returnOrderBook`, `returnOrderStatus`, `returnOrderTrades`, `returnSymbol`, `returnTicker`, `returnTradeHistory`, `returnUTradeHistory`, `spotWealthTransfer`
### 常见请求字段
- **symbol / currencyPair**：交易对，如 `BTC_USDT`（各接口字段名以示例为准）。
- **isMarket**：市价相关；**type**：订单类型；**rate / amount / funds**：价格、数量或金额（见下单说明）。
### 标准响应封装（多见于 REST）
- 常见顶层字段：`code`、`msg` / `message`、`success`、`failed`、`data`（具体以各节响应为准）。
### 常用枚举
- **failed**：true/false; 表示请求是否失败：true/false
- **isFrozen**：是否冻结：0：否，1：是
- **side**：BUY/SELL
- **state**：交易品种状态 1：正常， 2：禁用
- **status**：状态：1：未完成，2：部分完成，3：完全完成，4：用户取消; 订单状态 1：未完成，2：部分完成，3：完全完成，4：用户取消 5：已触发 6 触发失败
- **success**：true/false; 表示请求是否成功：true/false
- **type**（订单，见批量历史订单等）：`LIMIT`、`MARKET`、`HL_LIMIT`、`PLANNING`、`STOP_LIMIT_ORDER`、`SMART_MARKET_ORDER`、`ICEBERG` 等

## 示例
### GET（公共接口）
```bash
curl "https://api.coinw.com/api/v1/public?command=returnSymbol"
```
### 需要认证（私有接口）
```bash
params="api_key=$COINW_API_KEY&amount=0.001&funds=1&isMarket=1&out_trade_no=1&rate=40000&symbol=BTC_USDT"
sign_string="$params&secret_key=$COINW_SECRET_KEY"
sign=$(echo -n "$sign_string" | openssl md5 | cut -d' ' -f2 | tr '[:lower:]' '[:upper:]')
curl -X POST "https://api.coinw.com/api/v1/private?command=doTrade&$params&sign=$sign"
```
## Security
- 不要输出完整 api_key secret_key；如需展示，只保留尾部。
- 需要做交易时先让用户确认。
- 保存用户api_key和secret_key到安全的地方

## Agent Behavior

1. Credentials requested: Mask secrets (show last 5 chars only)
2. Listing accounts: Show names never keys
3. New credentials: Prompt for name, signing mode

## Adding New Accounts

When user provides new credentials:

* Ask for account name 
* Store in `TOOLS.md` with masked display confirmation 

## Reference
- Authentication`./references/Authentication.md`
- errorcode: `./references/errorcode.md`
- notes: `./references/notes.md`
- api-key create steps: `./references/api-key-creation-steps.md`
