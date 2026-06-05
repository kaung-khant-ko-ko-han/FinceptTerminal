
# AngelOne ပြန်လည်စစ်ဆေးခြင်း (OpenAlgo နှင့် Fincept QT)

ထုတ်ပေးသည့်ရက်: 2026-03-26
ပြင်ဆင်မှုများပြီးနောက် အပ်ဒိတ်လုပ်သည့်ရက်: 2026-03-26
နောက်ဆုံးသန့်ရှင်းရေး: 2026-03-26
အခြေခံ စစ်ဆေးရန်စာရင်း: `C:\projects\openalgo\docs\audit\ANGEL_ONE_IMPLEMENTATION_USAGE_TRACE.md`
ပစ်မှတ် repository: `C:\windowsdisk\finceptTerminal\fincept-qt`

## အလုံးစုံ လျှပ်တစ်ပြက်

- Baseline OpenAlgo broker အရေအတွက်: **၃၁** (baseline ဖိုင်မှ)
- Fincept QT တွင် မှတ်ပုံတင်ထားသော broker အရေအတွက်: **၁၆**
  - အထောက်အထား: `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\BrokerRegistry.cpp:108-127`, `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\BrokerRegistry.cpp:129`
- Fincept QT ရှိ AngelOne အခြေအနေ: **auth, orders, funds/positions, quotes/history, margins, instrument pipeline, နှင့် broker WS runtime path အတွက် ပေါင်းစည်းပြီးစီး**

## အမျိုးအစားအလိုက် ပြန်လည်စစ်ဆေးခြင်း (baseline အပိုင်းများနှင့် ဆန့်ကျင်)

### ၁) Plugin + core implementation
အခြေအနေ: **ပြီးစီး**

- AngelOne broker class/profile:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.h:6`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.h:15`
- Broker registry wiring:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\BrokerRegistry.cpp:110`
- Broker ID mapping:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\TradingTypes.h:413-414`, `450-451`
- Core endpoints အကောင်အထည်ဖော်ထားသည်:
  - Login: `...\AngelOneBroker.cpp:289`
  - Place/modify/cancel: `...\AngelOneBroker.cpp:347`, `376`, `395`
  - Orders/trades/positions/holdings/funds: `...\AngelOneBroker.cpp:422`, `460`, `476`, `514`, `552`
  - Quotes/history: `...\AngelOneBroker.cpp:610`, `705`

### ၂) Auth/login flow အသုံးပြုမှု
အခြေအနေ: **ပြီးစီး**

- Angel-specific credential အကွက်များနှင့် ထုပ်ပိုးခြင်း:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\equity_trading\EquityCredentials.h:49`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\equity_trading\EquityCredentials.cpp:214-223`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\equity_trading\EquityCredentials.cpp:274-279`
- Login + save flow:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\equity_trading\EquityTradingScreen.cpp:976`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\equity_trading\EquityTradingScreen.cpp:993`

### ၃) Runtime API dispatch (broker id မှတစ်ဆင့် Angel ရွေးချယ်ခြင်း)
အခြေအနေ: **ပြီးစီး**

- Screen မှ Quote/history/positions/holdings/orders/funds dispatch:
  - `...\EquityTradingScreen.cpp:406`, `475`, `503`, `529`, `555`, `581`, `608`
- Live order/cancel ကို UnifiedTrading မှတစ်ဆင့် dispatch:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\UnifiedTrading.cpp:121`, `131`, `151`, `156`

### ၄) WebSocket ပေါင်းစည်းမှု
အခြေအနေ: **ပြီးစီး (runtime path ထည့်သွင်းပြီး)**

- ExchangeService သည် broker stream ID များအတွက် broker bridge သို့ ကူးပြောင်းခြင်း (AngelOne အပါအဝင်):
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\ExchangeService.cpp:526`
- Broker bridge script reference ကို runtime တွင် ထည့်သွင်းထားသည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\ExchangeService.cpp:526-528`
- Broker bridge tick format (`type: "tick"`) ကို parser တွင် ပံ့ပိုးထားသည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\ExchangeService.cpp:651`

### ၅) Frontend/config အသုံးပြုမှု ညီမျှခြင်း
အခြေအနေ: **QT app နယ်ပယ်အတွက် ပြီးစီး**

- AngelOne data mapping template:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\screens\data_mapping\DataMappingScreen.cpp:315-321`
- AngelOne instrument cache ကို app စတင်ချိန်တွင် warm-load လုပ်ခြင်း (symbol search/completer အပြုအမူကို ပိုမိုကောင်းမွန်စေသည်):
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\app\main.cpp:67`

### ၆) Instrument refresh pipeline ညီမျှခြင်း
အခြေအနေ: **ပြီးစီး**

- AngelOne download support ထည့်သွင်းပြီး:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\instruments\InstrumentService.h:106`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\instruments\InstrumentService.cpp:392`
- AngelOne parser ကို service တွင် ထည့်သွင်းပြီး:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\instruments\InstrumentService.cpp:81`
- Refresh routing သည် `angelone` ကို ယခုအခါ ပံ့ပိုးသည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\instruments\InstrumentService.cpp:328`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\instruments\InstrumentService.cpp:347`

### ၇) Margin API ညီမျှခြင်း
အခြေအနေ: **ပြီးစီး**

- AngelOne broker သည် margin API နှစ်ခုလုံးကို ယခုအခါ override လုပ်သည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.h:52-55`
- Implementation နှင့် endpoint wiring:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.cpp:801`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.cpp:830`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\angelone\AngelOneBroker.cpp:821`, `856`

## ကျန်ရှိသည့်အရာများ

- **AngelOne ပေါင်းစည်းမှုနယ်ပယ်အတွက် မရှိတော့ပါ။**
- ပုံတူဖြစ်နေသော legacy ဖိုင်များကို ဖယ်ရှားပြီးဖြစ်သည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\AngelOneBroker.cpp`
  - `C:\windowsdisk\finceptTerminal\fincept-qt\src\trading\brokers\AngelOneBroker.h`
- Active compiled source သည် CMake တွင် lowercase path ၌ ကျန်ရှိသည်:
  - `C:\windowsdisk\finceptTerminal\fincept-qt\CMakeLists.txt:219`, `814`

## ပြီးစီးမှု စီရင်ချက်

- Core auth + order + portfolio + quotes/history: **ပြီးစီး**
- Instrument refresh ညီမျှခြင်း: **ပြီးစီး**
- Margin calculator ညီမျှခြင်း: **ပြီးစီး**
- Broker websocket runtime ညီမျှခြင်း: **ပြီးစီး**
- QT frontend mapping/startup integration သည် AngelOne အတွက် လိုအပ်သည်: **ပြီးစီး**
- Optional code hygiene cleanup (ပုံတူ legacy ဖိုင်များ): **ပြီးစီး**
