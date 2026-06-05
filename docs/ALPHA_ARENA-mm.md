
# Alpha Arena

Fincept Terminal နှင့် ပေါင်းစပ်ထားသော၊ replay-faithful Nof1 ပုံစံ ပြိုင်ဆိုင်မှု runtime ကို Native C++ ဖြင့် ရေးသားထားသည်။ LLM အေးဂျင့်များစွာကို crypto-perp စကြာဝဠာတစ်ခုတည်းတွင် tick-by-tick ဖြင့် ယှဉ်ပြိုင်စေပြီး၊ ကုဒ်ဝှက်ဖြင့် စစ်ဆေးနိုင်သော replay ပါဝင်သည်။

အကောင်အထည်ဖော်မှုတစ်ခုလုံးသည် C++ engine တွင် တည်ရှိသည်။ Python ပါဝင်မှုသည် business logic မပါသော စာကြောင်း ၂၀၀ ရှည် LLM အခွဲလုပ်ငန်းစဉ် (subprocess) တစ်ခုမျှသာဖြစ်သည်။

## ဗိသုကာ

```
┌──────────────────────────────────────────────────────────┐
│ AlphaArenaScreen (Qt UI)                                 │
│  • ခေါင်းစီး (နေရာ၊ tick နောက်ပြန်အရေအတွက်၊ FORCE TICK၊ KILL ALL) │
│  • ဦးဆောင်သူဇယား (ဘယ်ဘက်)                                    │
│  • ညာဘက် tabs: MODEL CHAT │ POSITIONS │ HITL │ RISK │ AUDIT
└──────────────────────────────────────────────────────────┘
              │  signals/slots သာ
              ▼
┌──────────────────────────────────────────────────────────┐
│ AlphaArenaEngine  (singleton service, persistent)         │
│  ├─ TickClock (QTimer, monotonic)                         │
│  ├─ ContextBuilder (deterministic prompt, byte-equal)     │
│  ├─ Indicators (EMA/MACD/RSI/ATR — pure C++)              │
│  ├─ ModelDispatcher (fan tick → N python subprocesses)    │
│  ├─ RiskEngine (pure fn: action → accept|reject|amend)    │
│  ├─ OrderRouter (route to IExchangeVenue)                 │
│  └─ HitlGate (modal/tray approvals, 60s timeout)          │
└──────────────────────────────────────────────────────────┘
              │
              ├──► IExchangeVenue (interface)
              │      ├─ HyperliquidVenue (live, REST+WS, EIP-712 sign)
              │      └─ PaperVenue (HL fees/funding/liq math ကို ထင်ဟပ်ဖော်ပြသည်)
              │
              ├──► AlphaArenaRepo (Qt SQL, WAL mode)
              │      Tables: aa_competitions, aa_agents, aa_ticks,
              │              aa_prompts, aa_decisions, aa_orders,
              │              aa_fills, aa_positions, aa_pnl_snapshots,
              │              aa_events, aa_hitl_approvals
              │
              └──► PythonRunner → scripts/alpha_arena/llm_call.py
                     (model တစ်ခုလျှင် tick တစ်ခုအတွက် subprocess တစ်ခု;
                      input: stdin မှ prompt+model+secret_handle;
                      output: stdout မှ response_text+usage)
```

## Nof1 S1 နှင့် replication တိကျမှု

| ကဏ္ဍ | Nof1 S1 | Fincept Alpha Arena |
|---|---|---|
| Action grammar | အချက်ပြ ၄ မျိုး | အချက်ပြ ၄ မျိုး (`buy_to_enter` / `sell_to_enter` / `hold` / `close`) |
| Modes | Baseline / Monk / Situational | ထပ်တူ |
| Cadence | ၆၀–၆၀၀ s | သတ်မှတ်နိုင်၊ ပုံမှန် ၁၈၀ s |
| Universe | perp ၆ မျိုး (BTC/ETH/SOL/BNB/DOGE/XRP) | ထပ်တူ (`kPerpUniverse()`) |
| Leverage cap | ၂၀× | ၂၀× (server-side တွင် အတည်ပြုသည်) |
| Risk per trade | equity ၏ ၂% | equity ၏ ၂% (server မှ ပြန်တွက်သည်) |
| Liquidation buffer | mark ၏ ၁၅% | mark ၏ ၁၅% |
| ဒင်္ဂါးတစ်မျိုးလျှင် အနေအထားတစ်ခု | ဟုတ်ကဲ့ | ဟုတ်ကဲ့ |
| အခကြေးငွေ (live & paper) | Hyperliquid taker 0.045% / maker 0.015% | ထပ်တူ |
| Funding | Hyperliquid ၈ နာရီအချိန်ဇယား | ထပ်တူ (real funding rate) |
| Slippage (paper) | မရှိ | တစ်ဖက်လျှင် 5 bps၊ latency 200 ms |

## Paper နှင့် live ကွာခြားချက်များ

Paper mode သည် ယုတ်ညံ့သော အစမ်းကြည့်မှုမဟုတ်ဘဲ တိကျမြင့်မားသည့် simulator တစ်ခုအဖြစ် တည်ရှိသည်။ mark prices များသည် Hyperliquid အများပြည်သူ WebSocket မှ အစစ်အမှန်ဖြစ်ပြီး အခကြေးငွေ / funding / liquidation သင်္ချာသည် HL ကို အတိအကျ ထင်ဟပ်ဖော်ပြသည်။ ကွာခြားချက်များ:

* **Slippage** — paper တွင် တစ်ဖက်လျှင် 5 bps ဟု ယူဆသည်။ Real HL slippage သည် agent order ၏ အရွယ်အစားနှင့် live order book ပေါ်တွင် မူတည်သည်။
* **Latency** — paper တွင် 200 ms artificial latency သုံးသည်။ Live latency သည် network + HL match-engine ပေါ်တွင် မူတည်သည်။
* **Settlement** — paper သည် local book တွင် ချက်ချင်းအခြေချသည်။ Live သည် Hyperliquid ၏ L1 ပေါ်တွင် အခြေချသည်။

## Replay နှင့် audit

tick တိုင်းကို database မှ ပြန်လည်ထုတ်လုပ်နိုင်သည်။ agent တစ်ခု၏ သမိုင်းကို replay လုပ်ရန်:

```sql
SELECT t.seq, p.text, d.raw_response, d.parsed_actions_json, d.risk_verdict_json
FROM aa_ticks t
JOIN aa_decisions d ON d.tick_id = t.id
JOIN aa_prompts p ON p.sha256 = d.user_prompt_sha256
WHERE t.competition_id = ?
  AND d.agent_id = ?
ORDER BY t.seq ASC;
```

`aa_events` သည် append-only ဖြစ်ပြီး တင်းကျပ်စွာ တိုးပွားနေသော `seq` ပါရှိသည်။ AUDIT tab သည် ဤ stream ကို အသက်ရှင်လျက် အမြီးလိုက်ပြသည်။ replay ကိရိယာသည် `aa_ticks` + `aa_prompts` + `aa_decisions` + `aa_orders` + `aa_fills` မှ မည်သည့် tick ၏ ပုံရိပ်အပြည့်အစုံကိုမဆို ပြန်လည်တည်ဆောက်နိုင်သည်။

## Kill switches

* **KILL ALL** — agent အားလုံး၏ အဖွင့်အနေအထားများကို ပိတ်ပြီး (market IOC) TickClock ကို ရပ်တန့်သည်။ ပြိုင်ဆိုင်မှုကို `halted_by_user` အဖြစ် မှတ်သားသည်။
* **Per-agent halt** — ဦးဆောင်သူဇယား အတန်းတစ်ခုကို နှစ်ချက်နှိပ်၍ "Halt" ကိုရွေးပါ။
* **Auto kill switch (per agent)** — parse မအောင်မြင် ၃ ကြိမ်ဆက်တိုက်၊ risk reject ၃ ကြိမ်ဆက်တိုက်၊ သို့မဟုတ် drawdown ၅၀% ပြီးနောက် ဖွင့်သည်။
* **Crash recovery** — app စတင်ချိန်တွင် `running` အခြေအနေရှိ မည်သည့်ပြိုင်ဆိုင်မှုကိုမဆို `crash_recovery_pending` မှတဆင့် ပြသသည်။ အသုံးပြုသူက Resume / Halt / Reconcile ကိုရွေးချယ်သည်။

## Live mode

Live mode ကို အောက်ပါတို့ဖြင့် ခွင့်ပြုသည်-

1. `I UNDERSTAND THIS IS REAL MONEY` ဟု စာရိုက် အသိအမှတ်ပြုခြင်း။
2. မဖြစ်မနေ checkbox နှစ်ခု (အသက် ≥ ၁၈၊ ပိတ်ဆို့ထားသော တရားစီရင်ပိုင်ခွင့်တွင် မရှိ)။
3. `QLocale::system().territory()` ဖြင့် US, NK, IR ကို ပိတ်ဆို့သည့် Geofence။
4. ပြိုင်ဆိုင်မှုတစ်ခုစီအတွက် agent-wallet private key, `SecureStorage` တွင်သာ သိမ်းဆည်းသည်။

Geofence သည် development-time အကာအကွယ်တစ်ခုဖြစ်သည် — လိုက်နာရန် နောက်ဆုံးတာဝန်မှာ အသုံးပြုသူ၌တည်သည်။ အလံတင်ထားသော locale တွင် development အတွက် ကျော်လွှားရန် `-DFINCEPT_UNSAFE_DISABLE_GEOFENCE=ON` ဖြင့် build လုပ်ပါ။ release builds များတွင် ၎င်းကို OFF ထားရမည်။

## စည်းကမ်းဆိုင်ရာ ငြင်းဆိုချက်များ

* **AGPL-3.0-or-later** — `LICENSE` ကိုကြည့်ပါ။
* **ဘဏ္ဍာရေးအကြံပေးချက်မဟုတ်ပါ** — Alpha Arena သည် သုတေသနနှင့် စံသတ်မှတ်ရေးကိရိယာတစ်ခုဖြစ်သည်။ အတိတ်စွမ်းဆောင်ရည်သည် အနာဂတ်ရလဒ်များကို အာမမခံနိုင်ပါ။ Model များသည် အားနည်းသော သို့မဟုတ် အန္တရာယ်ရှိသော အရောင်းအဝယ်များကို ထုတ်လုပ်နိုင်သည်။ Live mode သည် real orders များကို real venue သို့ ပို့ဆောင်ပြီး အစစ်အမှန် ဆုံးရှုံးမှုများ ဖြစ်ပေါ်နိုင်သည်။
* **ပွဲစားအဖြစ် စည်းကမ်းမထိန်းချုပ်ပါ** — Fincept Terminal သည် ရန်ပုံငွေများကို ထိန်းသိမ်းမထားပါ။ Live mode သည် အသုံးပြုသူထိန်းချုပ်သော private key ဖြင့် အရောင်းအဝယ်များကို လက်မှတ်ရေးထိုးသည်။

## Dependencies

* Qt 6.8.3 (Widgets, Network, WebSockets, Sql)
* Python 3.11.9 (subprocess သာ — engine တွင် Python မရှိ)
* Hyperliquid live mode အတွက် libsecp256k1 + keccak256 primitive လိုအပ်သည် (Phase 5c follow-up တွင် FetchContent ဖြင့် ထည့်သွင်းရန် ဆိုင်းငံ့ထား)။

## စမ်းသပ်ခြင်း

```powershell
cmake --preset win-release -DFINCEPT_BUILD_TESTS=ON
cmake --build --preset win-release
ctest --test-dir build/win-release --output-on-failure -R aa_
```

စမ်းသပ်မှု အပိုင်းများ:

* `aa_schema_roundtrip` — JSON parse / serialize
* `aa_schema_python_mirror` — C++ schema header နှင့် `schema.json` အကြား ကွဲလွဲမှု
* `aa_indicators` — EMA / RSI / MACD / ATR အက္ခရာသင်္ချာ invariants
* `aa_context_builder` — baseline mode တွင် agent အားလုံးကြား byte-equal
* `aa_repo` — replay determinism + WAL concurrency tolerance + monotonic events
* `aa_risk_engine` — လက်ခံ/ငြင်းပယ်/ပြင်ဆင် ဖြစ်ရပ် ၃၀+ + circuit-breaker
* `aa_paper_venue` — အခကြေးငွေ၊ liquidation၊ funding
* `aa_tick_clock` — skip-on-in-flight invariant
* `aa_hyperliquid_signer` — secp256k1 + keccak မရောက်မီ gated stub
* `aa_panels_lifecycle` — panel construct / refresh smoke
* `aa_ops` — crash recovery + state transitions
* `aa_50tick_replay` — repo replay across 50 ticks × 4 agents

## ကိုးကားချက်များ

* `.grill-me/alpha-arena-grill.md` — ဒီဇိုင်းဆွေးနွေးချက် (decision tree)
* `.grill-me/alpha-arena-production-refactor.md` — အကောင်အထည်ဖော်မှုအစီအစဉ်
* `src/services/alpha_arena/AlphaArenaSchema.h` — single-source-of-truth grammar
* `scripts/alpha_arena/schema.json` — Python mirror (CI တွင် drift-checked)
