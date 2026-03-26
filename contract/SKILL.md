---
{
  "name": 'Coinw Contract Skill',
  "description": 'Coinw 合约 REST API 技能：覆盖市场行情、下单/撤单、止损止盈、持仓与订单查询、账户资产与持仓模式、杠杆查询等。',
  "metadata": {"version": "1.3.0","author": "Coinw"}
}
---

# Coinw Contract Skill

Coinw 合约 REST API 技能：覆盖市场行情、下单/撤单、止损止盈、持仓与订单查询、账户资产、持仓模式与杠杆等。

## 主要特色
- 市场数据：合约品种、订单簿、K 线、成交、保证金要求
- 交易与风控：下单、平仓/反向/止损止盈（含追踪）
- 查询与持仓：订单历史、止损止盈信息、持仓保证金率与持仓查询
- 账户与通用：可转余额、成交明细、账户资产与费率、万能金、张币转换、持仓模式、用户可用规模、杠杆查询


## 快速参考

### 市场信息

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1.1 | 获取交易品种信息 | `/v1/perpum/instruments` | 此接口允许查询交易所上所有交易品种基本信息，包括杠杆率、保证金、止损率、止盈率、maker/taker费用、资金费率、最大持仓量等。用户… | GET | 公共 | name | base, defaultLeverage, defaultStopLossRate, defaultStopProfitRate, indexId, leverage, makerFee, maxLeverage, minLeverage 等37项 |
| 1.2 | 获取批量交易品种信息 | `/v1/perpum/instrumentList` | 该接口用于一次性查询多个交易品种的基础信息，包括杠杆倍数、保证金要求、止损率、止盈率、挂单/吃单手续费、结算汇率、最大持仓量等参数。 注… | GET | 公共 | symbols | base, defaultLeverage, defaultStopLossRate, defaultStopProfitRate, indexId, leverage, makerFee, maxLeverage, minLeverage 等38项 |
| 1.3 | 获取指定交易品种最新交易摘要 | `/v1/perpumPublic/ticker` | 此接口允许查询指定交易品种最新交易摘要，包括最高价格、最低价格、最大杠杆率、总交易量、最新价格和合约大小。 | GET | 公共 | instrument | contract_id, name, base_coin, quote_coin, price_coin, max_leverage, contract_size, last_price, high 等13项 |
| 1.4 | 获取批量最新交易摘要 | `/v1/perpumPublic/ticker/list` | 该接口用于一次性查询多个交易品种的最新交易摘要，包括最高价、最低价、最大杠杆、成交量、最新成交价、合约张数等。 注意： 批量获取最新交易… | GET | 公共 | symbols | contract_id, name, base_coin, quote_coin, price_coin, max_leverage, contract_size, last_price, high 等13项 |
| 1.5 | 获取所有交易品种最新交易摘要 | `/v1/perpumPublic/tickers` | 此接口允许查询交易所上所有交易品种最新交易摘要，包括最高价格、最低价格、最大杠杆率、总交易量、最新价格、合约大小等。 | GET | 公共 | — | contract_id, name, base_coin, quote_coin, price_coin, max_leverage, contract_size, last_price, high 等13项 |
| 1.6 | 获取指定交易品种的历史K线数据 | `/v1/perpumPublic/klines` | 该接口用于查询指定交易品种的历史和最新K线数据，返回包括最高价、最低价、开盘价、收盘价、时间戳以及成交量等信息。 | GET | 公共 | 见 api-doc 1.6（如 granularity、klineType、limit、链路上报币种与时间区间参数等） | K 线数组：每元素含时间戳、高/开/低/收、成交量（响应表为匿名列） |
| 1.7 | 获取最近一次结算资金费率 | `/v1/perpum/fundingRate` | 此接口返回的是最近一次结算时点的资金费率，此费率与下一次结算时点的资金费率无必然联系，且距此时已过去较长时间了。 | GET | 公共 | instrument | data |
| 1.8 | 获取指定交易品种的订单簿 | `/v1/perpumPublic/depth` | 此接口允许查询指定交易品种的深度数据，包括买单和卖单。 | GET | 公共 | base | asks, bids, m, p, n |
| 1.9 | 获取指定交易品种交易数据 | `/v1/perpumPublic/trades` | 此接口允许查询指定交易品种交易数据，包括合约大小、方向、ID、时间戳和价格。默认情况下，它将返回最近20笔交易的数据。 | GET | 公共 | base | createdDate, piece, direction, price, quantity, id |
| 1.10 | 获取所有交易品种保证金要求 | `/v1/perpum/ladders` | 此接口允许查询所有交易品种的分层保证金要求，包括初始保证金、维持保证金和最大杠杆率。 | GET | 私有 | api_key, sign | id, instrument, ladder, lastLadder, marginKeepRate, maxLeverage, marginStartRate, maxPiece |
| 1.11 | 获取历史公开交易 | `/v1/perpum/orders/trades` | 此接口允许查询指定交易品种的获取历史公开交易。 注意 :历史公开交易数据只能通过 RESTful API 获取。 | GET | 私有 | api_key, sign, instrument, pageSize | closedPiece, createdDate, dealPrice, direction, id |

### 下单

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2.1 | 下单 | `/v1/perpum/order` | 此接口允许用户进行合约交易下单，支持各种订单类型，包括市价单、限价单和触发单。 | POST | 私有 | api_key, sign, instrument, direction, leverage, quantityUnit, quantity, positionModel, positionType, openPrice, stopProfitPrice, stopLossPrice, thirdOrderId | code, data |
| 2.2 | 批量下单 | `/v1/perpum/batchOrders` | 此接口允许用户进行合约交易批量下单，支持各种订单类型，包括市价单、限价单和触发单。 | POST | 私有 | api_key, sign, instrument, direction, leverage, quantityUnit, quantity, positionModel, positionType, openPrice, stopProfitPrice, stopLossPrice, triggerPrice, triggerType, thirdOrderId | code, openId, thirdOrderId |
| 2.3 | 平仓 | `/v1/perpum/positions` | 此接口允许用户部分或完全平仓未平仓的合约持仓（已成交订单）。用户可以指定持仓ID，并选择基于固定合约大小或总持仓的百分比进行平仓。还可以… | DELETE | 私有 | api_key, sign, id, positionType, closeNum, orderPrice | data |
| 2.4 | 批量平仓 | `/v1/perpum/batchClose` | 此接口允许用户通过指定thirdOrderId（用户下单时定义的自定义ID）批量以市价平仓多个持仓。 | DELETE | 私有 | api_key, sign, thirdOrderId | openId, thirdOrderId |
| 2.5 | 市价平仓 | `/v1/perpum/allpositions` | 此接口允许用户以市价平仓指定交易品种所有持仓。 | DELETE | 私有 | api_key, sign | — |
| 2.6 | 反向持仓 | `/v1/perpum/positions/reverse` | 此接口允许用户即时反转现有永续合约持仓。通过指定持仓ID，系统将平仓当前持仓，并以相同持仓大小在相反方向（例如，从做多到做空或从做空到做… | POST | 私有 | api_key, sign, id | data |
| 2.7 | 调整保证金 | `/v1/perpum/positions/margin` | 此接口允许用户调整现有持仓（已成交订单）的保证金。通过指定持仓ID，用户可以增加或减少特定保证金金额。 | POST | 私有 | api_key, sign, id, addMargin | msg |
| 2.8 | 设置止损/止盈 | `/v1/perpum/TPSL` | 此接口允许用户为已成交和未成交订单设置止盈(TP)和止损(SL)。 | POST | 私有 | api_key, sign, id, instrument, stopProfitPrice, stopLossPrice | msg |
| 2.9 | 设置追踪止损/止盈 | `/v1/perpum/moveTPSL` | 此接口允许用户根据指定的回调率配置追踪止损(SL)和止盈(TP)机制，仅适用于已成交订单。要设置追踪SL/TP，用户必须提供有效的持仓I… | POST | 私有 | api_key, sign, openId, callbackRate, quantity, quantityUnit | — |
| 2.10 | 批量设置止损/止盈 | `/v1/perpum/addTpsl` | 此接口允许用户批量设置合约交易的止损(SL)和止盈(TP)。 | POST | 私有 | api_key, sign, id, instrument, stopLossPrice, stopProfitPrice, priceType, stopFrom, stopType, closePiece | msg |
| 2.11 | 批量修改止损/止盈 | `/v1/perpum/updateTpsl` | 此接口允许用户批量修改合约交易的止损(SL)和止盈(TP)订单。 | POST | 私有 | api_key, sign, id, instrument, stopLossPrice, stopProfitPrice, priceType, stopFrom, stopType, closePiece | msg |
| 2.12 | 修改订单 | `/v1/perpum/order` | 此接口允许用户修改未成交订单。请注意，某些参数，如保证金和杠杆率，无法使用此接口修改。 | PUT | 私有 | api_key, sign, id, instrument, direction, leverage, quantityUnit, quantity, positionModel, positionType, openPrice, stopProfitPrice, stopLossPrice, triggerPrice, triggerType | originId, editId |
| 2.13 | 取消订单 | `/v1/perpum/order` | 此接口允许用户通过指定订单ID取消未成交订单。 | DELETE | 私有 | api_key, sign, id | msg |
| 2.14 | 取消批量订单 | `/v1/perpum/batchOrders` | 此接口允许用户批量取消多个未成交订单。 | DELETE | 私有 | api_key, sign, sourceIds | — |

### 查询订单

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 3.1 | 获取未成交订单 | `/v1/perpum/orders/open` | 此接口允许查询指定合约和订单类型的未成交订单详情，包括合约、方向、数量、数量单位、保证金、杠杆、maker费用、taker费用和时间戳。 | GET | 私有 | api_key, sign, instrument, positionType | id, userId, baseSize, createdDate, currentPiece, direction, frozenFee, indexPrice, instrument 等28项 |
| 3.2 | 获取待处理订单数量 | `/v1/perpum/orders/openQuantity` | 此接口允许查询未成交订单的总数，即待处理订单。 注意 :待处理订单数据只能通过 RESTful API 获取。 | GET | 私有 | api_key, sign | data |
| 3.3 | 获取止损/止盈信息 | `/v1/perpum/TPSL` | 此接口允许查询合约交易中已成交和未成交订单的止损(SL)和止盈(TP)信息。 注意 :待处理订单数据只能通过 RESTful API 获… | GET | 私有 | api_key, sign, openId, stopFrom | currentPiece, closePiece, direction, createdDate, updatedDate, id, indexPrice, instrument, leverage 等24项 |
| 3.4 | 获取追踪止损/止盈信息 | `/v1/perpum/moveTPSL` | 此接口允许查询合约交易的追踪止损和止盈详情。 | GET | 私有 | api_key, sign | baseSize, callbackRate, createdDate, currentPiece, direction, fee, instrument, leverage, margin 等31项 |
| 3.5 | 获取历史订单（7天） | `/v1/perpum/orders/history` | 此接口提供过去七天的历史订单记录。用户可以使用页码、页面大小、订单类型和交易合约等参数筛选结果。响应提供了全面的订单详情，包括状态、保证… | GET | 私有 | api_key, sign, page, pageSize, originType, instrument | userId, baseSize, completeUsdt, createdDate, currentPiece, direction, entrustUsdt, havShortfall, hedgeId 等37项 |
| 3.6 | 获取批量历史订单（7天） | `/v1/perpum/orders/batchInsHistory` | 该接口返回过去7天的历史订单记录，并支持通过“symbols”参数进行批量查询。用户可通过页码、每页数量和订单类型进行筛选。返回结果包含… | GET | 私有 | api_key, sign, page, pageSize, originType, symbols | userId, baseSize, completeUsdt, createdDate, currentPiece, direction, entrustUsdt, havShortfall, hedgeId 等37项 |
| 3.7 | 获取历史订单（3个月） | `/v1/perpum/orders/archive` | 此接口提供过去三个月的历史订单记录。用户可以使用页码、页面大小、订单类型和交易合约等参数筛选结果。响应提供了全面的订单详情，包括状态、保… | GET | 私有 | api_key, sign, page, pageSize, originType, instrument | userId, baseSize, completeUsdt, createdDate, currentPiece, direction, entrustUsdt, havShortfall, hedgeId 等37项 |
| 3.8 | 获取批量历史订单（3个月） | `/v1/perpum/orders/batchInsArchive` | 该接口返回过去三个月的历史订单记录，并支持通过“symbols”参数进行批量查询。用户可通过页码、每页数量和订单类型进行筛选。返回结果包… | GET | 私有 | api_key, sign, page, pageSize, originType, symbols | userId, baseSize, completeUsdt, createdDate, currentPiece, direction, entrustUsdt, havShortfall, hedgeId 等37项 |
| 3.9 | 获取订单信息 | `/v1/perpum/order` | 此接口提供当前未成交订单的信息。用户可以指定订单ID列表来查询特定订单。如果既未指定合约也未指定订单ID，响应将包括指定订单类型下的所有… | GET | 私有 | api_key, sign, positionType, sourceIds | id, baseSize, contractType, userId, fee, createdDate, currentPiece, direction, frozenFee 等35项 |

### 持仓信息

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 4.1 | 获取当前持仓信息 | `/v1/perpum/positions` | 此接口允许用户通过指定合约查询当前开仓持仓（已成交订单）的信息。用户还可以通过提供一个或多个持仓ID来检索特定持仓的详情。 | GET | 私有 | api_key, sign, instrument | id, base, baseSize, createdDate, currentPiece, direction, fee, fundingSettle, indexPrice 等37项 |
| 4.2 | 获取历史持仓信息 | `/v1/perpum/positions/history` | 此接口允许查询所有历史持仓（已成交订单）。用户可以指定交易品种基础货币和持仓保证金来检索更具体的详情。 | GET | 私有 | api_key, sign, instrument, positionModel | avgOpenPrice, completeUsdt, direction, entrustUsdt, fee, havShortfall, indexPrice, instrument, leverage 等22项 |
| 4.3 | 获取持仓保证金率 | `/v1/perpum/positions/marginRate` | 此接口允许通过指定持仓ID查询应用于持仓（已成交订单）的保证金率。 | GET | 私有 | api_key, sign, positionId | data |
| 4.4 | 获取最大合约规模 | `/v1/perpum/orders/maxSize` | 此接口允许根据指定的合约、杠杆率和持仓模式查询最大可用合约规模（对于做多和做空持仓）。用户可以选择指定开仓价格。 | GET | 私有 | api_key, sign, leverage, instrument, positionModel, orderPrice | maxBuy, maxSell |
| 4.5 | 获取所有当前持仓 | `/v1/perpum/positions/all` | 此接口允许用户查询所有当前开仓持仓（已成交订单）。 | GET | 私有 | api_key, sign | id, base, baseSize, createdDate, currentPiece, closedPiece, direction, fee, fundingSettle 等30项 |

### 账户与资产

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 5.1 | 获取最大可转账余额 | `/v1/perpum/account/available` | 查询合约账户最大可转出金额。 | GET | 私有 | api_key, sign | data.value |
| 5.2 | 获取交易详情（3天） | `/v1/perpum/orders/deals` | 近 3 日成交/资金类明细（详见 api-doc 注意事项）。 | GET | 私有 | api_key, sign, instrument, page, pageSize, originType, positionModel | data.rows, nextId, prevId, total |
| 5.3 | 获取交易详情（3个月） | `/v1/perpum/orders/deals/history` | 近 3 个月成交/资金类明细（字段同 5.2）。 | GET | 私有 | api_key, sign, instrument, page, pageSize, originType, positionModel | data.rows, nextId, prevId, total |
| 5.4 | 获取合约账户资产 | `/v1/perpum/account/getUserAssets` | 可用保证金、USDT、冻结、万能金等（注意 availableMargin 含意）。 | GET | 私有 | api_key, sign | availableMargin, availableUsdt, alMargin, alFreeze, almightyGold, userId, time |
| 5.5 | 获取合约账户费用 | `/v1/perpum/account/fees` | 查询 maker/taker 费率。 | GET | 私有 | api_key, sign | makerFee, takerFee, userId |
| 5.6 | 获取万能金余额 | `/v1/perpum/account/almightyGoldInfo` | 按状态等筛选万能金记录（GET）。 | GET | 私有 | api_key, sign, type, startTime?, endTime? | data[]（id、currentAmount、totalAmount、type 等） |
| 5.7 | 单位转换 | `/v1/perpum/pieceConvert` | 合约张数与币数量互转。 | POST | 私有 | api_key, sign, convertType, faceValue, dealPiece?/baseSize? | data.value |
| 5.8 | 获取持仓模式 | `/v1/perpum/positions/type` | 查询逐仓/全仓与合并/分开布局（GET）。 | GET | 私有 | api_key, sign | layout, positionModel |
| 5.9 | 设置持仓模式 | `/v1/perpum/positions/type` | 设置逐仓/全仓与布局（POST）；切换前需无挂单。 | POST | 私有 | api_key, sign, positionModel, layout | data（TRANSACTION_SUCCESS） |
| 5.10 | 启用/禁用万能金 | `/v1/perpum/account/almightyGoldInfo` | 开启或关闭万能金（POST，与 5.6 同路径不同方法）。 | POST | 私有 | api_key, sign, status | code, msg |
| 5.11 | 获取用户最大合约规模 | `/v1/perpum/orders/availSize` | 已成交维度下多/空方向可用规模（与 4.4 maxSize 场景不同）。 | GET | 私有 | api_key, sign, instrument | availBuy, availSell |

### 通用

| 编号 | name | Endpoint | Description | Method | Authentication | Input Parameters | Output Parameters |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 6.1 | 获取杠杆信息 | `/v1/perpum/positions/leverage` | 单笔已成交（positionId）或未成交（orderId）杠杆，二选一。 | GET | 私有 | api_key, sign, positionId 或 orderId | data.value |

## 公共参数描述和枚举值

### 鉴权与 URL
- Base URL：`https://api.coinw.com`。
- 公共行情多为 `GET https://api.coinw.com/v1/perpum/...` 或 `.../v1/perpumPublic/...`。
- 私有接口在 query/body 中附带 `api_key`、`sign`（MD5，详见 Reference）。
### 常见请求字段
- **instrument** / **name**：合约标识（文档中常混用，含义为标的或合约名）。
- **symbols**：批量接口中的逗号分隔列表。
- **layout**：`0` 合并持仓，`1` 分开持仓（与 **positionModel** 一起在 `/v1/perpum/positions/type` 上 GET/POST）。
- **convertType**：`1` 张→币，`2` 币→张（**pieceConvert**）。
- **万能金 type（GET almightyGoldInfo）**：`0` 待生效～`4` 发放失败；**status（POST）**：`1` 开 / `0` 关。
### 标准响应封装（多见于 REST）
- 常见顶层字段：`code`、`msg` / `message`、`success`、`failed`、`data`（具体以各节响应为准）。
### 常用枚举与取值
- **contractType**：常见取值 `1`：U 本位永续合约（详见 api-doc）
- **positionModel**：仓位模式：`0` 逐仓，`1` 全仓（表述以 api-doc 为准）
- **quantityUnit**：用于指定订单数量的计量单位：0：计价货币（如 USDT）；1：合约张数；2：基础货币（如 BTC）。
- **status**：合约当前状态：offline：不可交易。online：活跃可交易。pretest：预测试阶段。settlement：结算过程中。preOffline：预下线阶段。; 合约状态： offline（下线）、 online（交易中）、 pretest（预发布）、 settlement（结算中）、 preOffline（预下线）。
- **priceType**：止损和止盈触发价格类型：1：指数价格，2：最新价格，3：标记价格
- **processStatus**：匹配服务器处理状态：0：等待，1：处理中，2：成功，3：失败
- **selected**：默认选择状态（0：否，1：是）; 默认选择状态（0：否，1：是）。
- **stopType**：止损和止盈订单类型：1：限价单，2：市价单，3：计划单
- **triggerStatus**：止盈和止损触发状态：0：未触发，1：已触发，2：已取消
- **triggerType**：指定"triggerPrice"满足时的订单类型：0：限价单，1：市价单; 指定触发价格满足时的订单类型：0：限价单，1：市价单


## 示例
### GET（公共接口）
```bash
curl "https://api.coinw.com/v1/perpumPublic/tickers"
```
### 需要认证（私有接口）
```bash
params="api_key=$COINW_API_KEY"
sign_string="$params&secret_key=$COINW_SECRET_KEY"
sign=$(echo -n "$sign_string" | openssl md5 | cut -d' ' -f2 | tr '[:lower:]' '[:upper:]')
curl -X DELETE "https://api.coinw.com/v1/perpum/allpositions&$params&sign=$sign"
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

* Ask for account name,api_key,secret_key
* Store in `TOOLS.md` with masked display confirmation 

## Reference
- Authentication`./references/Authentication.md`
- errorcode: `./references/errorcode.md`
- notes: `./references/notes.md`
- api-key create steps: `./references/api-key-creation-steps.md`

