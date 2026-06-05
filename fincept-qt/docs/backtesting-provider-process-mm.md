
# Backtesting Provider ပေါင်းစည်းခြင်း လုပ်ငန်းစဉ်

ဤစစ်ဆေးရန်စာရင်းကို provider တစ်ခုစီအတွက် **အတိအကျ** လိုက်နာပါ။

## Provider အစီအစဉ်
1. ~~BT~~ ✅
2. ~~VectorBT~~ ✅
3. ~~Backtesting.py~~ ✅
4. ~~FastTrade~~ ✅
5. ~~Zipline~~ ✅
6. ~~Fincept~~ ✅

> **ဗိသုကာ မှတ်ချက် (provider ၁-၃ ပြီးနောက် အသုံးပြုသည်):**
> Python သည် ယခုအခါ single source of truth ဖြစ်သည်။ `default_strategies()` နှင့် `all_indicators()` တို့က `{}` ပြန်ပေးသည်။
> Provider ပြောင်းသည့်အခါ၊ C++ က `load_strategies()`, `get_indicators`, နှင့် `load_command_options()` ကို ခေါ်သည်။
> ပေါင်းစပ်မှုအားလုံးကို ဒိုင်နမစ်ဖြင့် ဖြည့်သွင်းသည်။ `strategies_from_json()` သည် BT နှင့် VectorBT ပုံစံများကို ကိုင်တွယ်သည်။

---

## အဆင့်ဆင့် လုပ်ငန်းစဉ်

### အဆင့် ၁ — Python ဖိုင်များကို စစ်ဆေးခြင်း
- `*_provider.py` + ဆက်စပ် strategy/indicator ဖိုင်များအားလုံးကို ဖတ်ပါ
- ထုတ်ဖော်ပြသထားသော command တိုင်းကို စာရင်းပြုစုပါ (provider မှ မည်သည့် CLI commands များကို ကိုင်တွယ်သည်)
- strategy ID တိုင်း + အမျိုးအစား + parameters (အမည်၊ အမျိုးအစား၊ ရွေးချယ်ပါက options) ကို စာရင်းပြုစုပါ
- indicator ID တိုင်း + parameters ကို စာရင်းပြုစုပါ
- ပျောက်နေသော library အကျုံးဝင်မှုကို မှတ်သားပါ (library docs/pip နှင့် နှိုင်းယှဉ်ပါ)

### အဆင့် ၂ — Python အကျုံးဝင်မှုကို ချဲ့ထွင်ခြင်း
- Library တွင် အဓိပ္ပာယ်ရှိသော algos များ မှတ်ပုံတင်မထားသေးပါက ၎င်းတို့ကို ထည့်ပါ
- Strategy ဖိုင်အသစ်များသည် `{provider}_{topic}.py` ပုံစံအတိုင်း ဖြစ်သည်
- ဖိုင်အသစ်များကို `{provider}_provider.py` နှင့် `__init__.py` တွင် import လုပ်ပါ
- import/path ပဋိပက္ခများကို ဖြေရှင်းပါ (လိုအပ်ပါက aliased imports သုံးပါ)
- စမ်းသပ်ပါ: `python {provider}_provider.py get_strategies '{}'` သည် valid JSON ကို ပြန်ပေးရမည်

### အဆင့် ၃ — C++ နှင့် Python အကြား အပြန်အလှန် စစ်ဆေးခြင်း (BacktestingTypes.h)
အောက်ပါအချက် ၅ ချက်ကို တင်းကျပ်စွာ စစ်ဆေးပါ-

| စစ်ဆေးမှု | ဘာကို အတည်ပြုရမည် |
|-------|---------------|
| Commands | `all_providers()` ရှိ Provider ၏ command စာရင်းသည် Python က အမှန်တကယ် ကိုင်တွယ်သည့်အရာနှင့် ကိုက်ညီသည် |
| Strategy IDs | `default_strategies()` fallback ရှိ ID တိုင်းသည် တကယ့် Python strategy ID တစ်ခုနှင့် ကိုက်ညီသည် |
| Strategy အမျိုးအစားများ | `strategy_categories()` တွင် Python အမျိုးအစားများ အားလုံး ပါဝင်သည် (case/spelling အတိအကျ) |
| Indicator IDs | `all_indicators()` fallback ရှိ ID တိုင်းသည် Python INDICATOR_CATALOG နှင့် ကိုက်ညီသည် |
| Param အမည်များ | C++ structs ရှိ Param `name` အကွက်များသည် Python က JSON မှ ဖတ်သော သော့များနှင့် အတိအကျ ကိုက်ညီသည် |

### အဆင့် ၄ — မကိုက်ညီမှုအားလုံးကို ပြင်ဆင်ခြင်း
- `BacktestingTypes.h` တွင် IDs၊ အမျိုးအစားများ၊ param အမည်များကို ပြင်ပါ
- Python နှင့် ညီမျှခြင်းမရှိသော C++ strategies/indicators များကို ဖယ်ရှားပါ
- fallback မှ ပျောက်နေသော Python strategies များအတွက် C++ fallback entries များ ထည့်ပါ
- `default_strategies()` ကို တိကျသော subset တစ်ခုအဖြစ် ထားပါ (ကျယ်ကျယ်ပြန့်ပြန့် မဟုတ်ပါ — dynamic load က ကျန်တာကို ကိုင်တွယ်သည်)

### အဆင့် ၅ — dead code ကို စစ်ဆေးခြင်း
- `BacktestingTypes.h` ရှိ `inline` function တိုင်းကို grep လုပ်ပါ — တစ်ခုစီကို `BacktestingScreen.cpp` တွင် ခေါ်ဆိုကြောင်း အတည်ပြုပါ
- မည်သည့်နေရာတွင်မှ ရည်ညွှန်းခြင်းမခံရသော မည်သည့် function ကိုမဆို ဖယ်ရှားပါ

### အဆင့် ၆ — Build & အတည်ပြုခြင်း
- Build: `cmake --build build --config Debug`
- အမှားအသစ် သုည (ရှိပြီးသား သတိပေးချက်များသည် လက်ခံနိုင်သည်)
- App ကို စတင်ပါ၊ Backtesting tab သို့ သွားပါ၊ provider ကို ရွေးပါ
- Strategy စာရင်းသည် Python မှ ဒိုင်နမစ်ဖြင့် ပြည့်သွင်းကြောင်း အတည်ပြုပါ (fallback တစ်ခုတည်းမဟုတ်ပါ)
- Indicators များ မှန်ကန်စွာ ပြည့်သွင်းကြောင်း အတည်ပြုပါ
- Backtest တစ်ခုကို အဆုံးမှအဆုံး run ပါ

---

## စည်းမျဉ်းများ
- Python က ၎င်းတို့ကို ကိုင်တွယ်ကြောင်း မအတည်ပြုဘဲ `all_providers()` command စာရင်းများကို ဘယ်တော့မှ မပြောင်းပါနှင့်
- အမျိုးအစား စာကြောင်းများသည် Python နှင့် အတိအကျ ကိုက်ညီရမည် (Python က camelCase သုံးပါက camelCase)
- Fallback စာရင်းများသည် offline/error state အတွက် ရှိသည် — ၎င်းတို့ကို တိကျသော်လည်း အနည်းဆုံးထားပါ
- တစ်ကြိမ်လျှင် provider တစ်ခု — နောက်တစ်ခုသို့ မရွှေ့မီ အဆင့် ၆ ဆင့်လုံး ပြီးအောင်လုပ်ပါ
