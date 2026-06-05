
# DataHub အကြောင်းအရာ မှတ်ပုံတင်

hub topic မိသားစုတိုင်း၊ ၎င်း၏ ပိုင်ဆိုင်သည့် ထုတ်လုပ်သူ (producer)၊ TTL နှင့် ပြန်လည်ဆန်းသစ်မှု မူဝါဒတို့၏ စံသတ်မှတ်စာရင်း။ အဆင့် ၁၀ တွင် အပြီးသတ်သည် — လက်ရှိပါဝင်မှုများသည် အဆင့်အလိုက် ထုတ်လုပ်သူများ ရောက်ရှိလာသည်နှင့်အမျှ အလုပ်လုပ်နေသော မှတ်ပုံတင်ခြင်း ဖြစ်သည်။

အကြောင်းအရာအပိုင်းများကို `:` ဖြင့် ပိုင်းခြားသည်။ ပထမအပိုင်းသည် ဒိုမိန်း (domain) ဖြစ်သည်။ နောက်ဆက်တွဲအပိုင်းများသည် ဒိုမိန်းအလိုက် သော့များ (သင်္ကေတ၊ ပံ့ပိုးသူ၊ စီးရီး ID၊ …) ဖြစ်သည်။ wildcard `*` သည် အပိုင်းတစ်ခုတည်းနှင့် ကိုက်ညီသည်။

## စျေးကွက်ဒေတာ (အဆင့် ၂ / ၃)

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `market:quote:<sym>` | `MarketDataService` | ၅ s | ၁ s | သင်္ကေတတစ်ခုတည်း ကိုးကားချက် |
| `market:sparkline:<sym>` | `MarketDataService` | ၆၀ s | ၅ s | တစ်နေ့တွင်း sparkline အမှတ်များ |
| `market:history:<sym>:<period>:<interval>` | `MarketDataService` | ၃၀၀ s | ၃၀ s | Bar သမိုင်း |

## WebSocket စီးကြောင်းများ (အဆင့် ၄)

ထုတ်လုပ်သူအား multi-broker refactor (အဆင့် ၂) ဖြင့် `ExchangeSessionManager` သို့ ပြောင်းလဲခဲ့သည်။
Session များ (exchange တစ်ခုလျှင် တစ်ခု၊ app သက်တမ်းတစ်လျှောက် နွေးထွေးစွာထားရှိ) သည် `SessionPublisher` မှတစ်ဆင့် ဒေတာ fan-out ကို manager သို့ ပေးအပ်သည်။ manager သည် `ws:<exchange>:*` အတွက် တစ်ဦးတည်းသော hub မှတ်ပုံတင်သူ ဖြစ်သည်။

| ပုံစံ | ထုတ်လုပ်သူ | TTL | မှတ်ချက်များ |
|---|---|---|---|
| `ws:kraken:ticker:<pair>` | `ExchangeSessionManager` | push-only | ၅၀ ms ပေါင်းစည်း |
| `ws:kraken:orderbook:<pair>` | `ExchangeSessionManager` | push-only | |
| `ws:kraken:trades:<pair>` | `ExchangeSessionManager` | push-only | |
| `ws:kraken:ohlc:<pair>:<interval>` | `ExchangeSessionManager` | push-only | |
| `ws:hyperliquid:*` | `ExchangeSessionManager` | push-only | Kraken နှင့် တူညီသော မိသားစုခွဲများ |
| `prediction:polymarket:price:<asset_id>` | `PolymarketWebSocket` | push-only | ကြိုတင်ခန့်မှန်းချက်စျေးကွက်များ refactor မလုပ်မီ `polymarket:price:*` ဖြစ်ခဲ့သည် |
| `prediction:polymarket:orderbook:<asset_id>` | `PolymarketWebSocket` | push-only | ကြိုတင်ခန့်မှန်းချက်စျေးကွက်များ refactor မလုပ်မီ `polymarket:orderbook:*` ဖြစ်ခဲ့သည် |
| `prediction:kalshi:price:<ticker>:<side>` | `KalshiWsClient` | push-only | `<side>` သည် `yes` (ဟုတ်) သို့မဟုတ် `no` (မဟုတ်) ဖြစ်သည်။ အထောက်အထားများ လိုအပ်သည် — streaming ကို အဆင့် ၇ တွင် အသက်သွင်းမည် |
| `prediction:kalshi:orderbook:<ticker>:<side>` | `KalshiWsClient` | push-only | Kalshi REST သည် yes+no လေလံများသာ ပြန်ပေးသည်။ မေးလေလံများကို client-side တွင် ပေါင်းစပ်ဖန်တီးသည် |

## သတင်း (အဆင့် ၅)

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `news:general` | `NewsService` | ၅ မိနစ် | ၃၀ s | ၂၅၀ ms ပေါင်းစည်း (တိုးတက်သော ထုတ်ဝေမှု) |
| `news:symbol:<sym>` | `NewsService` | ၅ မိနစ် | ၃၀ s | news:general ၏ စစ်ထုတ်ထားသော အပိုင်းအစ |
| `news:category:<cat>` | `NewsService` | ၅ မိနစ် | ၃၀ s | NewsArticle မှ အမျိုးအစား စာကြောင်းများ |
| `news:cluster:<id>` | `NewsService` | push-only | — | ဆာဗာက သတ်မှတ်ပေးသော အစုအဝေးများ |

## စီးပွားရေး (အဆင့် ၆)

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `econ:<source>:<request_id>` | `EconomicsService` | ၁ နာရီ | ၆၀ s | `source` = fred, worldbank, imf, oecd, စသည် |
| `econ:fincept:upcoming_events` | `MacroCalendarService` | ၅ မိနစ် | ၆၀ s | HTTP-back (`api.fincept.in/macro/upcoming-events?limit=25`)။ payload: `QJsonArray` of `{event, country, date, time, importance, actual, forecast, previous}`။ dashboard ၏ `EconomicCalendarWidget` မှ စားသုံးသည်။ |
| `dbnomics:<provider>:<dataset>:<series>` | `DBnomicsService` | ၁ နာရီ | ၆၀ s | စူးစမ်းလေ့လာမှု (observations) endpoint သာ |
| `govdata:<provider>:<request_id>` | `GovDataService` | ၁ နာရီ | ၆၀ s | အစိုးရ open-data APIs |

## Broker အကောင့် စီးကြောင်းများ (အဆင့် ၇)

အကြောင်းအရာပုံစံ: `broker:<broker_id>:<account_id>:<channel>[:<sym>]`။
ခေါ်ဆိုသူတွင် ရှင်းလင်းသော အကောင့် ID မရှိသောအခါ၊ ၎င်းသည် `default` ကို ပေးသည် — single-account နှင့် multi-account code path များသည် ဖော်မတ်တစ်ခုတည်းကို မျှဝေသည်။ `src/trading/BrokerTopic.h` ရှိ `fincept::trading::broker_topic()` မှတစ်ဆင့် အကြောင်းအရာများကို တည်ဆောက်ပါ။

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `broker:*:*:positions` | `DataStreamManager` | ၅ s | ၃ s | အကောင့်တစ်ခုအတွက် အဖွင့်အနေအထားများ |
| `broker:*:*:orders` | `DataStreamManager` | ၅ s | ၃ s | အကောင့်တစ်ခုအတွက် တိုက်ရိုက်အမိန့်စာအုပ်စာအုပ် |
| `broker:*:*:balance` | `DataStreamManager` | ၃၀ s | ၁၀ s | ငွေသား + အာမခံ (`BrokerFunds`) |
| `broker:*:*:holdings` | `DataStreamManager` | ၃၀ s | ၁၀ s | ရေရှည်ကိုင်ဆောင်မှုများ |
| `broker:*:*:quote:<sym>` | `DataStreamManager` | ၅ s | ၁ s | သင်္ကေတအလိုက် ကိုးကားချက် လျှပ်တစ်ပြက် |
| `broker:*:*:ticks:<sym>` | `DataStreamManager` | push-only | — | ၁၀၀ ms ပေါင်းစည်း; broker WS feed |

`DataStreamManager` သည် လက်ရှိတွင် တစ်ခုတည်းသော broker ထုတ်လုပ်သူဖြစ်သည်။ ၎င်းသည် ရှိပြီးသား per-account signal များနှင့်အတူ hub ထုတ်ဝေမှုများကို dual-fire လုပ်သည်။ Per-broker tick feed များသည် နောက်ဆက်တွဲ PR များတွင် ရွှေ့ပြောင်းမည် (အဆင့် ၇ အစီအစဉ်၏ အန္တရာယ်လျှော့ချရေးအရ ချိန်ညှိချက်အတိုင်း PR တစ်ခုလျှင် broker တစ်ခု)။

## ပထဝီနိုင်ငံရေး / ရေကြောင်း / ကော်ပိုရိတ် (အဆင့် ၈)

### ပထဝီနိုင်ငံရေး

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `geopolitics:events` | `GeopoliticsService` | ၂ မိနစ် | ၃၀ s | ပဋိပက္ခစောင့်ကြည့်ရေး သတင်းဖြစ်ရပ်များ (ပုံသေ params)။ payload: `EventsPage` (အသစ်ဆုံးအရင်စီထားသော ဖြစ်ရပ်များ + စာမျက်နှာခွဲခြား + credits တိုင်းတာမှု)။ |
| `geopolitics:countries` | `GeopoliticsService` | ၁၀ မိနစ် | ၆၀ s | ထူးခြားသော နိုင်ငံစာရင်း + ဖြစ်ရပ်အရေအတွက်များ |
| `geopolitics:categories` | `GeopoliticsService` | ၁၀ မိနစ် | ၆၀ s | ထူးခြားသော ဖြစ်ရပ်အမျိုးအစားစာရင်း |
| `geopolitics:cities` | `GeopoliticsService` | ၁၀ မိနစ် | ၆၀ s | ထုတ်ယူထားသော ကိုဩဒိနိတ်များပါရှိသော မြို့များ |
| `geopolitics:hdx:<context>` | `GeopoliticsService` | ၁ နာရီ | ၆၀ s | `<context>` = conflicts, humanitarian, country:<iso>, topic:<slug>, search:<q> |
| `geopolitics:trade:<kind>` | `GeopoliticsService` | ၁၅ မိနစ် (push-only) | — | `<kind>` = benefits, restrictions |
| `geopolitics:geolocation` | `GeopoliticsService` | ၁၅ မိနစ် (push-only) | — | ခေါင်းစဉ်အစုအဝေးမှ ထုတ်ယူထားသော coords |
| `geopolitics:relationship_graph:<ticker>` | `RelationshipMapService` | ၁၀ မိနစ် | ၂ မိနစ် | yfinance-backed ကော်ပိုရိတ်ဆက်ဆံရေး လျှပ်တစ်ပြက် |

### ရေကြောင်း

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `maritime:vessel:<imo>` | `MaritimeService` | ၁ မိနစ် | ၃၀ s | သင်္ဘောတစ်စင်း၏ အနေအထား။ payload: `VesselData`။ |
| `maritime:vessels:multi` | `MaritimeService` | ၁ မိနစ် | ၃၀ s | multi-vessel အစုအဝေး (ခေါ်ဆိုသူက IMOs များ ထောက်ပံ့သည်)။ payload: `VesselsPage` (vessels + found_count + not_found စာရင်း + credits metering)။ user-invoked — hub က auto-refresh မလုပ်ပါ။ |
| `maritime:vessels:area` | `MaritimeService` | ၁ မိနစ် | ၃၀ s | ဧရိယာရှာဖွေရေး အကွက်ကွပ်ခြင်း။ payload: `VesselsPage` (သင်္ဘောများ အသစ်ဆုံးအရင်စီထား, total_count, credits metering)။ user-invoked။ |
| `maritime:history:<imo>` | `MaritimeService` | ၅ မိနစ် | ၆၀ s | သင်္ဘောလမ်းကြောင်းသမိုင်း။ payload: `VesselHistoryPage` (သမိုင်း အသစ်ဆုံးအရင်စီထား + total_records + credits metering)။ |
| `maritime:health` | `MaritimeService` | ၅ မိနစ် | ၆၀ s | Marine API ကျန်းမာရေး စစ်ဆေးချက်။ |

### M&A ပိုင်းခြားစိတ်ဖြာမှု

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `ma:<context>` | `MAAnalyticsService` | ၂ မိနစ် (push-only) | — | `<context>` သည် service method ကို လိုက်နာသည် (ဥပမာ `ma:dcf`, `ma:merger_model`, `ma:lbo_returns`)။ analytics အားလုံးသည် ခေါ်ဆိုသူထောက်ပံ့သော params များ ယူသည် — hub က ၎င်းတို့ကို ပြန်လည်လုပ်ဆောင်၍မရသောကြောင့် အကြောင်းအရာများသည် push-only ဖြစ်သည်။ ခေါ်ဆိုသူများသည် ရှိပြီးသား method API မှတစ်ဆင့် refresh ကို မောင်းနှင်ရမည်။

## AI / Agents / LLM (အဆင့် ၉)

`AgentService` နှင့် `LlmService` မှ ထုတ်ဝေသော push-only အကြောင်းအရာ မိသားစုများ။ ဤအကြောင်းအရာများသည် **per-run disposable** ဖြစ်သည် — ထုတ်လုပ်သူသည် ပြီးစီးချိန်တွင် `DataHub::retire_topic(...)` ကို ခေါ်ဆိုသောကြောင့် cached state ကို hub သို့ ပြန်လွှတ်ပေးသည်။ `subscribe(owner, ...)` မှတစ်ဆင့် တွဲထားသော subscribers များသည် အနားယူခြင်း (retirement) တစ်လျှောက်တွင် တွဲဆက်လက်ရှိနေပြီး နောက်ထုတ်ဝေမှုကို ပုံမှန်အတိုင်း လက်ခံရရှိမည်။

### Agent လုပ်ဆောင်မှု

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `agent:output:<run_id>` | `AgentService` | ၁၀ မိနစ် (push-only) | — | run တစ်ခုအတွက် နောက်ဆုံးရလဒ် payload။ ပြီးစီးချိန်တွင် အနားယူသည်။ ပုံစံ: `{request_id, success, response, error, execution_time_ms, final}`။ |
| `agent:stream:<run_id>` | `AgentService` | ၅ မိနစ် (push-only, coalesce 50 ms) | — | streaming run များမှ token ရေပိုက်လိုင်း။ ပုံစံ: `{request_id, token}`။ |
| `agent:status:<run_id>` | `AgentService` | ၅ မိနစ် (push-only, coalesce 100 ms) | — | တွေးခေါ်ခြင်း/tool-call ဇာတ်ကြောင်း။ ပုံစံ: `{request_id, status}`။ |
| `agent:routing:<run_id>` | `AgentService` | ၁၀ မိနစ် (push-only) | — | one-shot routing ဆုံးဖြတ်ချက်။ ပုံစံ: `{request_id, success, agent_id, intent, confidence}`။ |
| `agent:error:<context>` | `AgentService` | ၂ မိနစ် (push-only) | — | context (discover_agents, create_plan, စသည်) ဖြင့် သော့ခတ်ထားသော error stream။ ပုံစံ: `{context, message}`။ |

### LLM session stream

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `llm:session:<session_id>:stream` | `LlmService` | ၅ မိနစ် (push-only, coalesce 50 ms) | — | `chat_streaming()` မှ streaming အပိုင်းအစတိုင်း၏ အရိပ်ထုတ်ဝေမှု (shadow publish)။ ပုံစံ: `{session_id, chunk, done}`။ Session id ကို call တစ်ခုစီအတွက် ထုတ်ပေးသည်။ `done=true` တွင် အကြောင်းအရာ အနားယူသည်။ |

### Generic DataHub MCP tools

MCP module `DataHubTools` သည် မည်သည့် LLM tool ခေါ်ဆိုသူကိုမဆို ယေဘုယျ စူးစမ်းလေ့လာရေး ကိရိယာ လေးခုကို ထုတ်ဖော်ပြသသည် (`docs/agents/datahub-guide.md` ကိုကြည့်ပါ):

- `datahub_list_topics` — active topic တိုင်း + subscriber အရေအတွက် + နောက်ဆုံးထုတ်ဝေပြီး အသက်
- `datahub_peek` — topic တစ်ခုအတွက် လက်ရှိ cached တန်ဖိုး (`{value, age_ms}`) — refresh မစေပါ
- `datahub_request` — topic တစ်ခုကို refresh လုပ်ရန် hub အား တောင်းဆိုခြင်း (policy + `force` flag ကို လိုက်နာသည်)
- `datahub_subscribe_briefly` — သတ်မှတ်ကြာချိန် (100-30000 ms) အတွင်း topic တစ်ခုပေါ်တွင် ထုတ်ဝေသော တန်ဖိုးအားလုံးကို စုဆောင်းခြင်း

## Crypto / on-chain (အဆင့် ၁၀)

Crypto Center သည် အသုံးပြုသူ၏ ချိတ်ဆက်ထားသော Solana ပိုက်ဆံအိတ်အတွက် အကြောင်းအရာအစုံအနည်းငယ်ကို ပိုင်ဆိုင်သည်။ Read-only ဒေတာသည် hub မှတစ်ဆင့် စီးဆင်းသည်။ အရောင်းအဝယ်လက်မှတ်ထိုးခြင်းလမ်းကြောင်း (အဆင့် ၂ swap) သည် hub မှတစ်ဆင့် **မသွားပါ** — ၎င်းတို့သည် `WalletService` + `WalletTxBridge` မှ တိုက်ရိုက်ကိုင်တွယ်သော one-shot user လုပ်ဆောင်ချက်များဖြစ်သည်။

### ပိုက်ဆံအိတ် လက်ကျန်များ

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `wallet:balance:<pubkey>` | `WalletBalanceProducer` | ၃၀ s (poll) / push-only (stream) | ၁၀ s (poll) / — (stream) | SecureStorage `wallet.balance_mode` (`poll` / `stream`) မှတစ်ဆင့် runtime တွင် ပြောင်းနိုင်သော refresh mode နှစ်ခု။ **အဆင့် ၂ §2A.5 သည် ပိုက်ဆံအိတ်တွင်ရှိသော SPL token တိုင်းကို သယ်ဆောင်သည်** — $FNCPT တစ်ခုတည်းမဟုတ်ပါ။ Polling သည် Solana RPC `getBalance` + `getTokenAccountsByOwner` (programId-filtered, mint filter မပါ) + `TokenMetadataService::lookup` အတွက် symbol/name/icon ကို အသုံးပြုသည်။ Streaming သည် RPC `wss://` endpoint သို့ per-pubkey WebSocket တစ်ခုဖွင့်ပြီး SOL အပြောင်းအလဲများအတွက် wallet pubkey ကို `accountSubscribe` လုပ်သည်။ SOL မဟုတ်သော token များကို REST မှတစ်ဆင့် စတင်ပြီး 30 s heartbeat တွင် ပြန်လည်စေ့စပ်သည် (re-seed)။ Stream policy: `push_only=true`, `coalesce_within_ms=250`။ ပုံစံ: `WalletBalance{pubkey_b58, sol_lamports, tokens: QVector<TokenHolding{mint, symbol, name, amount_raw, decimals, verified, icon_url}>, ts_ms}`။ FNCPT ကို အထူးလိုချင်သေးသော ခေါ်ဆိုသူများအတွက် legacy back-compat accessor `fncpt_holding()` / `fncpt_ui()` / `fncpt_decimals()` ကို `WalletBalance` တွင် ထုတ်ဖော်ပြသထားသည်။ Endpoint ဦးစားပေး: SecureStorage override `solana.rpc_url` → Helius (`solana.helius_api_key`) → public mainnet RPC (`https`/`wss` မှဆင်းသက်လာ)။ |

### Token စျေးနှုန်း

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `market:price:token:<mint>` | `TokenPriceProducer` | ၆၀ s | ၃၀ s | Jupiter Lite Price API (`lite-api.jup.ag/price/v3`)။ refresh တစ်ခုလျှင် subscriber အနည်းဆုံးတစ်ဦးရှိသော mint တိုင်းကို လွှမ်းခြုံသော ခေါ်ဆိုမှုတစ်ခု: `?ids=<m1>,<m2>,…`။ လျင်မြန်သော subscribe ဖြစ်ရပ်များတစ်လျှောက် 250 ms ပေါင်းစည်းသည် (panel သည် balance update တိုင်းတွင် ပြန်လည် subscribe လုပ်သည်)။ `<mint>` သည် wrapped-SOL (`So111…1112`) ကို native SOL စျေးနှုန်းသတ်မှတ်ရန်အတွက် လက်ခံသည်။ ပုံစံ: `TokenPrice{mint, usd, sol, ts_ms, valid}`။ `sol` အကွက်သည် FNCPT-priced-in-SOL ဖြစ်ပြီး SOL mint သည် batch တစ်ခုတည်း၏ အစိတ်အပိုင်းဖြစ်ပါက၊ မဟုတ်ပါက 0 ဖြစ်သည်။ |
| `market:price:fncpt` | `TokenPriceProducer` | ၆၀ s | ၃၀ s | **Deprecated alias** for `market:price:token:9LUqJ5aQTjQiUCL93gi33LZcscUoSBJNhVCYpPzEpump`။ ရှိပြီးသား subscribers (အဆင့် 1 ၏ HoldingsBar, BalancePanel, SwapPanel) တို့ အနှောင့်အယှက်မရှိ ဆက်လက်အလုပ်လုပ်စေရန် အဆင့်တစ်ခုကြာ ထားရှိခြင်းဖြစ်သည်။ အဆင့် 3 cleanup တွင် ဖယ်ရှားမည်။ new subscribers များသည် `market:price:token:<mint>` ကို တိုက်ရိုက်သုံးသင့်သည်။ ပုံစံ: `TokenPrice` (legacy `FncptPrice` သည် typedef alias တစ်ခုဖြစ်သည်)။ |

### ပိုက်ဆံအိတ် လုပ်ဆောင်ချက် (အဆင့် ၂)

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `wallet:activity:<pubkey>` | `WalletActivityProducer` | ၃၀ s | ၁၀ s | နောက်ဆုံး ၅၀ ပါးဆား (parsed) ပိုက်ဆံအိတ် လုပ်ဆောင်ချက်များ။ Helius parsed-transactions endpoint (`api.helius.xyz/v0/addresses/<pk>/transactions?limit=50`) ကို အသုံးပြုပြီး user တွင် `solana.helius_api_key` ရှိပါက၊ မရှိပါက raw `getSignaturesForAddress` (signatures များသာ) သို့ ပြန်ကျသည်။ ပုံစံ: `vector<ParsedActivity{ts, kind, asset, amount, signature, status}>` — `kind` ∈ `{SWAP, RECEIVE, SEND, OTHER}`။ |

### အခကြေးငွေ လျှော့ချခြင်း အရည်အချင်းပြည့်မီမှု (အဆင့် ၂)

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `billing:fncpt_discount:<pubkey>` | `FeeDiscountService` | `wallet:balance:<pubkey>` မှ ဆင်းသက်လာ (သီးခြား fetch မရှိ) | — | service သည် user ၏ balance topic သို့ internally subscribe လုပ်ပြီး အရည်အချင်းပြည့်မီမှုကို ပြန်လည်ထုတ်ဝေသည်။ ပုံစံ: `FncptDiscount{eligible, threshold_raw, threshold_decimals, applied_skus}`။ Threshold + applied SKUs များသည် `services/billing/FeeDiscountConfig.h` မှ လာသည်။ ပုံသေမှာ **1,000 $FNCPT → 30% off** for AI reports, deep backtests, premium screens။ |

> **အဆင့် ၂ swap path သည် hub topic မဟုတ်ပါ။** `PumpFunSwapService::build_swap()` သည် `pumpportal.fun/api/trade-local` သို့ one-shot, user-initiated HTTP POST တစ်ခုဖြစ်ပြီး unsigned versioned-tx body ကို ပြန်ပေးသည်။ ၎င်းကို DataHub မှတစ်ဆင့် တွန်းပို့ရန် debounce-coalesce သို့မဟုတ် cache-coherence တန်ဖိုး မရှိပါ။ ရလဒ်ကို `WalletService::sign_and_send()` သို့ တိုက်ရိုက် ကျွေးသည်။ `plans/crypto-center-phase-2.md` D1 ကိုကြည့်ပါ။

> **မီးရှို့ခြင်း (Burn) ကို အဆင့် ၅ သို့ ရွှေ့ဆိုင်းထားသည်။** အဆင့် 2 သည် BUY နှင့် SELL ကိုသာ ပို့ဆောင်သည်။ on-chain receipt ပါရှိသော အစစ်အမှန် မီးရှို့ခြင်းများကို buyback-worker ဒီဇိုင်းနှင့်အတူ အဆင့် 5 သို့ ရွှေ့ပြောင်းမည်။ အဆင့် 2 တွင် `wallet:burn_receipt:*` topic မရှိပါ။

### Buyback & burn dashboard (အဆင့် ၅)

Terminal-wide topics — အသုံးပြုသူတိုင်းအား တူညီသော နံပါတ်များကို ပြသသည်၊ `<pubkey>` အပိုင်း မရှိပါ။ Fincept-operated buyback worker (`services/buyback-worker/`) မှ မောင်းနှင်ပြီး၊ ဝင်ငွေကို စုစည်း၊ Jupiter ဝယ်ယူမှုများကို လုပ်ဆောင်၊ ဝယ်ယူထားသော $FNCPT ကို treasury အကောင့်မှ SPL Token `burn_checked` မှတစ်ဆင့် မီးရှို့ကာ၊ ထို့နောက် per-epoch အကျဉ်းချုပ်များကို SecureStorage `fincept.treasury_endpoint` မှတစ်ဆင့် သတ်မှတ်ထားသော Fincept HTTP endpoint သို့ ထုတ်ဝေသည်။ endpoint ကို မသတ်မှတ်မချင်း၊ producer များသည် built-in mock payload ကို ပို့ဆောင်သည် (POD တစ်ခုစီတွင် `is_mock=true` ပါရှိ)။ dashboard သည် ခေါင်းစဉ်ဆေးတောင့်တွင် "DEMO" ကို ဖတ်ရှုသည်။

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `treasury:buyback_epoch` | `BuybackBurnService` | ၆၀ s | ၃၀ s | လက်ရှိ epoch အကျဉ်းချုပ်။ ပုံစံ: `BuybackEpoch{epoch_no, start/end_ts_ms, revenue_total/subs/predmkt/misc_usd, buyback_usd, staker_yield_usd, treasury_topup_usd, fncpt_bought/burned_raw, fncpt_decimals, avg_buy_price_usd, burn_signature, is_mock}`။ USD splits သုံးခု (`buyback`, `staker_yield`, `treasury_topup`) သည် အစီအစဉ် §5.4 50/25/25 ဖြန့်ဖြူးမှုကို အကောင်အထည်ဖော်သည်။ worker သည် epoch တစ်ခုစီအတွက် အမှန်တကယ် ရာခိုင်နှုန်းများကို ရွေးချယ်သည်။ `burn_signature` သည် base58 ဖြစ်သည် — `BuybackBurnPanel` သည် Solscan တွင် ၎င်းကို ဖွင့်သည်။ |
| `treasury:burn_total` | `BuybackBurnService` | ၅ မိနစ် | ၆၀ s | အချိန်တိုင်းအရေအတွက်များ။ ပုံစံ: `BurnTotal{total_burned_raw, supply_remaining_raw, decimals, spent_on_buyback_usd, is_mock}`။ |
| `treasury:supply_history` | `BuybackBurnService` | ၁ နာရီ | ၅ မိနစ် | supply chart အတွက် 12 လ အချိန်စီးရီး။ ပုံစံ: `QVector<SupplyHistoryPoint{ts_ms, total_raw, circulating_raw, burned_raw, decimals}>`။ Producer သည် refresh တစ်ခုစီတွင် vector တစ်ခုလုံးကို ထုတ်ဝေသည်။ subscribers (`SupplyChartPanel`) သည် series ကို အစုလိုက်အပြုံလိုက် အစားထိုးသည်။ |
| `treasury:reserves` | `TreasuryService` | ၅ မိနစ် | ၆၀ s | treasury multisig ၏ လက်ရှိ SOL + USDC ကိုင်ဆောင်မှုများ။ အရင်းအမြစ်: `SolanaRpcClient::get_sol_balance` + SecureStorage `fincept.treasury_pubkey` ရှိ pubkey ကို ဆန့်ကျင်၍ `get_token_balance(USDC mint)`။ SOL→USD စျေးနှုန်းကို `market:price:token:<wSOL>` မှ ခိုးကြည့်သည် (သို့မှသာ double-fetch မဖြစ်စေရ)။ ပုံစံ: `TreasuryReserves{pubkey_b58, sol_lamports, usdc_amount, sol_usd_price, total_usd, multisig_label, multisig_url, is_mock}`။ |
| `treasury:runway` | `TreasuryService` | ၅ မိနစ် | ၆၀ s | လက်ရှိ burn နှုန်းဖြင့် လပေါင်းများစွာ လည်ပတ်နိုင်မှု။ `total_usd / monthly_opex_usd` အဖြစ် တွက်ချက်သည်။ opex သည် SecureStorage `fincept.treasury_monthly_opex_usd` မှ လာသည် (ပုံသေ $100k)။ ပုံစံ: `TreasuryRunway{total_usd, monthly_opex_usd, months, is_mock}`။ `treasury:reserves` နှင့် တွဲလျက် ပြန်ဆင်းသက်လာသည်။ |

### STAKE / veFNCPT / အဆင့်စနစ် (အဆင့် ၃)

STAKE tab အတွက် veFNCPT lock မျက်နှာပြင်။ producer လေးခုစလုံးသည် SecureStorage တွင် `fincept.lock_program_id` (Anchor program) နှင့် `fincept.yield_endpoint` ကို မသတ်မှတ်မချင်း **mock mode** တွင် ပို့ဆောင်သည်။ payload တစ်ခုစီတွင် `is_mock=true` ပါရှိသောကြောင့် panels များသည် state ကို ရှင်းလင်းစွာ ဖော်ပြနိုင်သည်။

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `wallet:locks:<pubkey>` | `StakingService` | ၆၀ s | ၃၀ s | `LockPosition{position_id, amount_raw, decimals, lock_start_ts, unlock_ts, duration_secs, weight_raw, lifetime_yield_usdc, is_mock}` ၏ vector။ Real path: `fincept.lock_program_id` ကို ဆန့်ကျင်၍ `getProgramAccounts`၊ owner ဖြင့် filter လုပ်။ Mock path: အစီအစဉ် §3.2 အရ သရုပ်ပြအနေအထား 3 ခု (2,000 @ 4yr / 1,000 @ 1yr / 500 @ 6mo)။ |
| `wallet:vefncpt:<pubkey>` | `StakingService` | ၆၀ s | ၃၀ s | စုစုပေါင်း weight + နောက်ကာလအတွက် ခန့်မှန်း yield။ ပုံစံ: `VeFncptAggregate{pubkey_b58, total_weight_raw, decimals, position_count, projected_next_period_yield_usdc, is_mock}`။ `LockPosition.weight_raw` ကို ပေါင်းခြင်းနှင့် §3.4 25%-of-revenue staker-share ကို အသုံးပြု၍ တွက်ချက်သည်။ |
| `wallet:yield:<pubkey>` | `RealYieldService` | ၅ မိနစ် | ၆၀ s | ရရှိပြီးသား USDC yield။ ပုံစံ: `YieldSnapshot{pubkey_b58, lifetime_usdc, last_period_usdc, last_period_end_ts, is_mock}`။ Real path: `<endpoint>/yield/<pubkey>`။ Mock path: `treasury:revenue × 25% / weight share` မှ နံပါတ်များကို ဆင်းသက်လာစေသောကြောင့် သရုပ်ပြနံပါတ်များသည် buyback dashboard နှင့် အတွင်းပိုင်း ညီညွတ်နေသည်။ |
| `treasury:revenue` | `RealYieldService` | ၁ နာရီ | ၅ မိနစ် | Terminal-wide အပတ်စဉ် ဝင်ငွေပုံး။ ပုံစံ: `TreasuryRevenue{period_start_ts, period_end_ts, total_usd, is_mock}`။ lock-creation မလုပ်မီ "EST. YIELD" အတွက် `LockPanel` မှ အသုံးပြုသည်။ အဆင့် 5 dashboard ၏ ဝင်ငွေခွဲခြမ်းစိတ်ဖြာမှုကိုလည်း ကျွေးမွေးသည်။ buyback worker ၏ epoch ချိန်ညှိချက်နှင့် ကိုက်ညီရန် အပတ်စဉ် ပုံးသွင်းထားသည်။ |
| `billing:tier:<pubkey>` | `TierService` | ၆၀ s | ၁၅ s | `wallet:vefncpt:<pubkey>` မှ ဆင်းသက်လာ။ service သည် vefncpt သို့ internally subscribe လုပ်ပြီး weight ပြောင်းလဲတိုင်း ပြန်လည်ထုတ်ဝေသည်။ ပုံစံ: `TierStatus{pubkey_b58, tier (Free/Bronze/Silver/Gold), weight_raw, next_threshold_raw, decimals, is_mock}`။ `services/billing/TierConfig.h` တွင် သတ်မှတ်ချက်များ (100 / 1k / 10k veFNCPT)။ `tier_changed` Qt signal မှတစ်ဆင့် cross-screen gating (AI Quant Lab, Alpha Arena) ကို မောင်းနှင်သည်။ |

### အတွင်းပိုင်း ကြိုတင်ခန့်မှန်းချက်စျေးကွက်များ (အဆင့် ၄)

`FinceptInternalAdapter` matching engine အတွက် သီးသန့်ထားသော အကြောင်းအရာမိသားစု။ adapter startup တွင် topics များကို policy ဖြင့် မှတ်ပုံတင်သော်လည်း **မည်သည့် producer မှ ၎င်းတို့ထံ မထုတ်ဝေသေး**။ `fincept.markets_endpoint` *နှင့်* `fincept_market` Anchor program (`solana/programs/fincept_market/`, သီးခြား repo) ကို အသုံးပြုသည်အထိ adapter သည် **demo mode** (Qt signals မှတစ်ဆင့် စီစဉ်ထားသော 3-market dataset ကိုသာ ထုတ်လွှတ်သည်) တွင် ပို့ဆောင်သည်။

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `prediction:fincept:markets` | `FinceptInternalAdapter` (planned) | ၃၀ s | ၁၀ s | စီစဉ်ထား/t တိုက်ရိုက် စျေးကွက်စာရင်း။ ပုံစံ: `QVector<PredictionMarket>` (`prediction:polymarket:markets` နှင့် တူညီသော ပုံစံ)။ အကွက်များ: `key`, `question`, `category`, `volume`, `outcomes` (စျေးနှုန်း [0,1] ပါရှိသော binary YES/NO), `end_date_iso`။ သရုပ်ပြ dataset ကို `markets_ready` signal မှတစ်ဆင့် ထုတ်လွှတ်သည်။ topic publishing သည် matching engine ကို စောင့်ဆိုင်းနေသည်။ |
| `prediction:fincept:orderbook:<asset_id>` | `FinceptInternalAdapter` (planned) | ၅ s | ၁ s | per-asset order book။ ပုံစံ: `PredictionOrderBook{asset_id, bids[], asks[]}` — Polymarket/Kalshi ပုံစံနှင့် ကိုက်ညီသည်။ တိုက်ရိုက်ဖြစ်ပါက WebSocket-driven ဖြစ်သည်။ |
| `prediction:fincept:price:<asset_id>` | `FinceptInternalAdapter` (planned) | ၅ s | ၁ s | နောက်ဆုံးအရောင်းအဝယ်စျေးနှုန်း scalar။ `prediction:polymarket:price:*` နှင့် တူညီသော ပုံစံ။ |

## F&O / အော်ပရှင်များ (အဆင့် ၁၁ — Sensibull ပုံစံ tab)

F&O screen သည် ၎င်း၏ကိုယ်ပိုင် producer မိသားစုကို ပိုင်ဆိုင်သည်။ `OptionChainService` သည် `option:*` နှင့် ဆင်းသက်လာသော `fno:pcr:*` / `fno:max_pain:*` အကြောင်းအရာများအတွက် တစ်ဦးတည်းသော hub မှတ်ပုံတင်သူ ဖြစ်သည်။ အဆင့် 1 တွင် polled REST refresh ကို ပို့ဆောင်ခဲ့သည်။ **အဆင့် 3** တွင် Greeks/IV ကို `option_greeks_daemon.py` worker မှတစ်ဆင့် ထည့်သွင်းခဲ့သည်၊ ATM IV publishing၊ per-leg `option:tick:*` fan-out၊ နှင့် `OISnapshotter` history producer ကို ထည့်သွင်းခဲ့သည်။ WebSocket OI push (broker-driven) သည် ဆိုင်းငံ့ထားဆဲဖြစ်ပြီး subscribers များကို မပြောင်းလဲဘဲ chain-derived `option:tick` အရင်းအမြစ်ကို အစားထိုးမည်။

### Chain နှင့် per-leg စီးကြောင်းများ

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `option:chain:<broker>:<underlying>:<expiry>` | `OptionChainService` | ၅ s | ၃ s | 250 ms ပေါင်းစည်း။ `pause_when_inactive=true`။ payload: `OptionChain` (အတန်းများ strike ascending အလိုက်စီထား, spot, ATM, PCR, max_pain, total OI)။ Producer သည် CE+PE+underlying quotes များကို `IBroker::get_quotes` မှတစ်ဆင့် batch လုပ်ပြီး စုစည်းသည်။ Chain refresh သည် UI ကို မပိတ်ဆို့စေရန် worker thread တွင် လည်ပတ်သည်။ ပထမထုတ်ဝေမှုပြီးနောက်၊ producer သည် `OptionGreeksWorker` မှတစ်ဆင့် `option_greeks_batch` တောင်းဆိုချက်ကို စတင်ပြီး Greeks/IV များဖြည့်စွက်ထားသော chain ကို ပြန်လည်ထုတ်ဝေသည်။ hub coalesce window သည် ပုံမှန်အားဖြင့် ထုတ်ဝေမှုနှစ်ခုကို တစ်ခုတည်းသော ပေးပို့မှုအဖြစ် ပြိုကျစေသည်။ |
| `option:tick:<broker>:<token>` | `OptionChainService` (chain-derived; broker-WS အစားထိုးရန် စီစဉ်ထား) | push-only | — | per-leg `BrokerQuote` လျှပ်တစ်ပြက်။ 100 ms ပေါင်းစည်း။ အဆင့် 3 သည် chain refresh တိုင်းတွင် ခြေထောက်တစ်ခုစီအတွက် ထုတ်ဝေမှုတစ်ခုကို fan-out လုပ်သည် — ၎င်းသည် full chain သို့ subscribe မလုပ်ဘဲ ခြေထောက်တစ်ခု သို့မဟုတ် နှစ်ခုကို စောင့်ကြည့်သည့် strategy panels များအတွက် အသုံးဝင်သည်။ broker WS ရောက်ရှိလာသောအခါ အရင်းအမြစ်သည် subscribers များကို မချိုးဖဲ့ဘဲ push သို့ ပြောင်းလဲမည်။ |
| `option:atm_iv:<broker>:<underlying>` | `OptionChainService` | ၅ s | ၃ s | ATM implied volatility scalar (decimal, 0.142 = 14.2%)။ Push-only — chain Greeks ကြွယ်ဝမှု၏ ဘေးထွက်ဆိုးကျိုးအဖြစ် refresh တိုင်းတွင် ပြန်လည်တွက်ချက်ပြီး ထုတ်ဝေသည်။ တန်ဖိုး = ATM CE IV နှင့် ATM PE IV တို့ နှစ်ခုလုံးရှိပါက ၎င်းတို့၏ ပျမ်းမျှ၊ မဟုတ်ပါက ရှိသောဘက်မှ ဖြစ်သည်။ |

### ဆင်းသက်လာသော ပိုင်းခြားစိတ်ဖြာမှုများ

| ပုံစံ | ထုတ်လုပ်သူ | TTL | အနိမ့်ဆုံးကြားကာလ | မှတ်ချက်များ |
|---|---|---|---|---|
| `fno:pcr:<broker>:<underlying>:<expiry>` | `OptionChainService` | push-only | — | Put/Call Ratio = sum(PE OI) / sum(CE OI)။ chain publish တိုင်းတွင် ပြန်လည်ထုတ်ဝေသည် (250 ms ပေါင်းစည်း)။ payload: `double`။ |
| `fno:max_pain:<broker>:<underlying>:<expiry>` | `OptionChainService` | push-only | — | သက်တမ်းကုန်ချိန်တွင် စုစုပေါင်း option-writer ဝေဒနာကို အနည်းဆုံးဖြစ်စေသော strike။ payload: `double`။ |
| `fno:fii_dii:daily` | `FiiDiiService` | ၁ နာရီ | ၃၀ မိနစ် | NSE မှ `scripts/fii_dii_scraper.py` (session-cookie auth, browser User-Agent) မှတစ်ဆင့် ခြစ်ယူထားသော နေ့စဉ် အဖွဲ့အစည်းဆိုင်ရာ ငွေကြေးစီးဆင်းမှုများ။ တစ်နေ့လျှင် 30 မိနစ်တစ်ကြိမ် အများဆုံး refresh လုပ်သည် — NSE သည် ကုန်သွယ်မှုတစ်ရက်လျှင် ညနေ 6 နာရီ IST နောက်ပိုင်းတွင် အရင်းအမြစ်နံပါတ်များကို တစ်ကြိမ်သာ အပ်ဒိတ်လုပ်သည်။ payload: `QVector<FiiDiiDay>` ascending by date — နောက်ဆုံး 30 ရက်ကို `fii_dii_daily` SQLite table မှ ဝန်ဆောင်မှုပေးသည်။ fresh DB တွင် စျေးကွက်ပိတ်ချိန်မတိုင်မီ empty payload။ |
| `oi:history:<broker>:<token>:<window>` | `OISnapshotter` | ၆၀ s | ၃၀ s | OI Analytics sub-tab အတွက် တစ်နေ့တွင်း OI စီးရီး။ `<window>` = `1d` / `5d` / `7d`။ Snapshotter သည် `option:chain:*` သို့ subscribe လုပ်ပြီး token တစ်ခုစီအတွက် နောက်ဆုံးရ CE/PE quote ကို buffer လုပ်ကာ၊ minute-aligned အတန်းများကို SQLite (`oi_snapshots`, schema v025) သို့ 60 s တိုင်း flush လုပ်သည်။ payload: `QVector<OISample>` အလိုက် `ts_minute` ascending အလိုက်စီထား။ retention 7 days rolling — အဟောင်းအတန်းများကို တစ်နာရီတိုင်း ဖြတ်တောက်သည်။ |

> **Greeks worker:** `OptionGreeksWorker` သည် `PythonWorker` ၏ အစ်ကိုဝန်ဆောင်မှု (sibling daemon) ဖြစ်ပြီး `venv-numpy2` (တွင် `py_vollib` ရှိသည်) ကို ဆန့်ကျင်၍ `scripts/option_greeks_daemon.py --daemon` ကို လုပ်ဆောင်သည်။ တစ်ခုတည်းသော ထောက်ခံရသော action: `option_greeks_batch`။ Inputs များသည် per-contract (`token, S, K, t, r, q, flag, market_price, model="bsm"`) ဖြစ်သည်။ outputs များသည် per-contract IV (decimal) + Greeks ဖြစ်သည်။ အတိုင်းအတာ: vega နှင့် rho ကို 1.00 σ / 1.00 r တိုင်းအတွက် ပြန်ပေးသည် (py_vollib ၏ per-1% တန်ဖိုးများကို 100 ဖြင့် မြှောက်ပါ); theta သည် ပြက္ခဒိန်တစ်ရက်လျှင် ဖြစ်သည်။ Risk-free rate `r` ကို session တစ်ခုလျှင် တစ်ကြိမ် `settings.fno.risk_free_rate` မှ ဖတ်သည် (ပုံသေ 0.067, RBI 91-day T-bill ballpark)။ Per-strike Greeks ပြန်တွက်ချက်ခြင်းကို 500 ms သို့ throttle လုပ်ထားသည်။

> **IV percentile pill (အဆင့် 10 သန့်စင်မှု):** ATM IV publish တိုင်းသည် `(underlying, today)` ကို `iv_history_daily` SQLite table (schema v028, `WITHOUT ROWID` keyed by `(underlying, date_iso)`) သို့ UPSERT လုပ်သည်။ FnoHeaderBar သည် trailing 90-day window ကိုဖတ်ပြီး လက်ရှိ IV ၏ percentile rank ကိုပြသည်။ hub topic အသစ်မရှိပါ — ၎င်းသည် repo-only feature တစ်ခုဖြစ်သည်။ Pill သည် အနည်းဆုံး 30 ရက်သမိုင်းမစုမိမချင်း "—" ကိုပြသသည်။

> **နောက်ခံအခြေခံ လက်ငင်းစျေးနှုန်း (Underlying spot):** chain producer သည် option quotes များနှင့်အတူ underlying quote ကို `get_quotes` batch တစ်ခုတည်းတွင် အမြဲပြန်လည်ရယူသည်၊ ထို့ကြောင့် subscribers များသည် `market:quote:<sym>` သို့ cross-subscribe လုပ်ရန် မလိုအပ်ပါ။ Index symbols များသည် `NSE_INDEX:<NAME>` (NIFTY/BANKNIFTY/FINNIFTY/MIDCPNIFTY) ကိုသုံးသည်။ stocks များသည် `NSE:<SYM>` ကိုသုံးသည်။

> **Broker လိုအပ်ချက်:** F&O topics များသည် ချိတ်ဆက်ထားပြီး၊ instruments-loaded ဖြစ်သော broker တစ်ခု လိုအပ်သည်။ InstrumentService cache ဗလာဖြစ်သောအခါ producer သည် `publish_error("no instruments cached for …")` ကို ထုတ်ဝေသည်။ ထိုသို့ဖြစ်လျှင် consumers များသည် "connect a broker" prompt တစ်ခုကို ဖော်ပြသင့်သည်။

## အတင်းပြန်လည်ဆန်းသစ်ခြင်း (Force refresh)

`DataHub::request(topic, force=true)` သည် `min_interval_ms` ကို ကျော်လွှားသည် (ထို့ကြောင့် user-driven refresh ခလုတ်များသည် interval gate အတွင်းတွင် အလုပ်လုပ်သည်)။ per-producer `max_requests_per_sec()` ကို ဆက်လက်ရိုသေသည် — rage-clicking သည် upstream ကို ထုတ်မပို့နိုင်စေရန် ကာကွယ်သည်။
