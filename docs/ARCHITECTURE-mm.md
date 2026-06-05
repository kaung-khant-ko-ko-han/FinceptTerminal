
# Fincept Terminal — ဗိသုကာ

**အခြေအနေ:** အသက်ရှင်နေသော မှတ်တမ်း။ ၂၀၂၆-၀၅ ရက်စွဲအထိ codebase နှင့် ကျွန်ုပ်တို့ ချဉ်းကပ်နေသော ပစ်မှတ်အခြေအနေကို ရောင်ပြန်ဟပ်သည်။
**ပရိသတ်:** ပါဝင်ကူညီသူများ (C++ နှင့် Python), ထိန်းသိမ်းသူများ၊ နှင့် codebase ကို ပြုပြင်မွမ်းမံသော AI အကူများ။
**တွဲဖက်မှတ်တမ်းများ:** [`REFACTOR_PLAN.md`](REFACTOR_PLAN.md) (အဆင့်လိုက် အကောင်အထည်ဖော်မှု), [`../fincept-qt/DATAHUB_ARCHITECTURE.md`](../fincept-qt/DATAHUB_ARCHITECTURE.md) (data plane သတ်မှတ်ချက်)။

> **ဤမှတ်တမ်းကို မည်သို့ဖတ်ရမည်။** **(လက်ရှိ)** အမှတ်အသားပါသော အပိုင်းများသည် ယနေ့ codebase ကို ဖော်ပြသည်။ **(ပစ်မှတ်)** အမှတ်အသားပါသော အပိုင်းများသည် ကျွန်ုပ်တို့ ရွေ့လျားနေသော ပုံသဏ္ဍန်ကို ဖော်ပြသည်။ ၎င်းတို့ ကွဲပြားနေပါက၊ refactor plan သည် ခြားနားချက်ကို ပိုင်ဆိုင်သည်။

---

## ၁။ ဤ codebase သည် ဘာလဲ

Fincept Terminal v4 သည် native C++20/Qt6 desktop application တစ်ခုဖြစ်သည် — Bloomberg ပုံစံ multi-window ငွေကြေးဆိုင်ရာ workstation တစ်ခုဖြစ်ပြီး embedded Python analytics, multi-broker trading, AI agents နှင့် in-process data plane တို့ ပါဝင်သည်။

| အတိုင်းအတာ | အရွယ်အစား |
|-----------|-------|
| C++ ရင်းမြစ်ဖိုင်များ | ~၁,၆၂၆ (`.cpp`/`.h`) |
| C++ စာကြောင်းများ | ~၃၄၂,၀၀၀ |
| Python scripts များ | ~၁,၄၂၃ |
| Screens များ | ၅၄ (lazy-instantiated) |
| ဝန်ဆောင်မှုများ | ~၅၀ (data, trading, AI, workflow) |
| Brokers များ | ၁၆ (equity/F&O) + crypto exchange ၂ ခု |
| Repositories များ | ၂၆ (typed `BaseRepository<T>` မှတဆင့်) |
| MCP tools များ | ၄၀+ |
| Build ပစ်မှတ် | OS တစ်ခုလျှင် Qt6 desktop binary တစ်ခု |

၎င်းသည် ရည်ရွယ်ချက်ရှိရှိ **modular monolith** တစ်ခုဖြစ်သည် — တစ်ခုတည်းသော deployable၊ အတွင်းပိုင်းတွင် bounded contexts များအဖြစ် ပိုင်းခြားထားပြီး dependency direction ရှင်းလင်းသည်။ Microservices များသည် desktop runtime အတွက် ဆန့်ကျင်သော ပန်းတိုင်ဖြစ်သည်။

---

## ၂။ တစ်ချက်ကြည့်လျှင် ဗိသုကာ

```
┌─────────────────────────────────────────────────────────────────────┐
│  PRESENTATION                                                       │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  Screens (54)        DashboardWidgets (13)    DockManager     │  │
│  │  via DockScreenRouter (lazy factory)                          │  │
│  │  state via IStatefulScreen + ScreenStateManager               │  │
│  └───────────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│  APPLICATION  (bounded contexts)                                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────┐ │
│  │ Markets  │  │  News    │  │ Trading  │  │  Agents  │  │ AI   │ │
│  │ Economics│  │ Geopol   │  │ Portfolio│  │ Workflow │  │ Chat │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────┘ │
├─────────────────────────────────────────────────────────────────────┤
│  DATA PLANE                                                         │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  DataHub  ──  topic အလိုက် one-fetch/many-subscribers pub/sub │  │
│  │  CacheManager (SQLite-backed TTL cache)                       │  │
│  └───────────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│  INTEGRATION ADAPTERS                                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────┐ │
│  │  Broker  │  │   MCP    │  │  Python  │  │   HTTP   │  │  WS  │ │
│  │ Adapter  │  │  Tools   │  │  Runner  │  │  Client  │  │ Feed │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────┘ │
├─────────────────────────────────────────────────────────────────────┤
│  INFRASTRUCTURE                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  Logger · AppConfig · EventBus · SessionManager · AuthManager │  │
│  │  Database (SQLite + migrations) · CacheDatabase               │  │
│  │  SecureStorage (SQLite + AES-256-GCM)                         │  │
│  │  Repositories (BaseRepository<T>, 26 implementations)         │  │
│  └───────────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│  PLATFORM  Qt6 abstraction — Windows (MSVC) / macOS (Clang) / Linux │
└─────────────────────────────────────────────────────────────────────┘
```

**Dependency direction အတွက် ခိုင်မာသော စည်းမျဉ်းများ:**

1. Presentation → Application → Data Plane → Adapters → Infrastructure → Platform။ ဘယ်တော့မှ ပြောင်းပြန်မလုပ်ရ။
2. Adapters များသည် အရွက်များ (leaves) ဖြစ်သည်။ ဝန်ဆောင်မှုနှစ်ခုသည် adapter တစ်ခုကို မျှဝေနိုင်သည်။ adapter တစ်ခုသည် ဝန်ဆောင်မှုတစ်ခုကို ခေါ်ဆိုခြင်း မပြုရ။
3. Context ဖြတ်ကျော်ခေါ်ဆိုမှုများ (ဥပမာ Markets → Trading) သည် **DataHub topics** သို့မဟုတ် **typed events** မှတဆင့် သွားရမည်၊ direct includes မသုံးရ။
4. Infrastructure တွင် စီးပွားရေးဆိုင်ရာ အသိပညာ မရှိပါ။ "watchlist" ဆိုတာကို ၎င်းက မသိပါ။

---

## ၃။ နည်းပညာ အစုအစဉ်

| အစိတ်အပိုင်း | နည်းပညာ | အခန်းကဏ္ဍ |
|-----------|------|-----------|
| ဘာသာစကား | C++20 | Core runtime |
| UI | Qt6 Widgets + Qt6 Charts | Retained-mode native UI |
| Async (ပစ်မှတ်) | QCoro (C++20 coroutines) | `co_await QNetworkReply`, `co_await QFuture` |
| Async (လက်ရှိ) | `QPointer` context ပါသော Callbacks, signals/slots | QCoro အပြည့်အစုံမပြောင်းမချင်း backward-compatible |
| Networking | Qt6 Network (HTTP/TLS), Qt6 WebSockets | I/O အားလုံး |
| Database | Qt6 Sql + SQLite | Local persistence၊ DB နှစ်ခု (`Database`, `CacheDatabase`) |
| Schema | `storage/sqlite/migrations/` အောက်ရှိ versioned migrations | ရှေ့သို့သာ |
| Crypto | SQLite-side encryption မှတဆင့် AES-256-GCM | `SecureStorage` အတွက်သာ |
| JSON | `QJsonDocument` | Wire formats အားလုံး |
| Logging | `Logger` custom ကို macros (`LOG_INFO`, `LOG_ERROR`) ဖြင့် | Async-safe; lazy stringification |
| Python | 3.11.9 via UV-managed bundled venv | Analytics, agents, data fetchers |
| Docking | ADS (Advanced Docking System) via `CDockManager` | Multi-window/multi-panel layouts |
| Build | CMake 3.20+ (ပစ်မှတ်: per-module subdirectories) | OS တစ်ခုလျှင် binary တစ်ခု |
| Packaging | `windeployqt` / `macdeployqt` + Python bundle | `fincept-qt/packaging/` ကိုကြည့်ပါ |

---

## ၄။ Bounded contexts

Context တစ်ခုစီသည် ၎င်း၏ screens, services, types နှင့် DataHub topics များကို ပိုင်ဆိုင်သည်။ Contexts များသည် ထုတ်ဝေသည် (publish)။ ၎င်းတို့သည် အချင်းချင်း ခေါ်ဆိုခြင်း မပြုပါ။

| Context | ပိုင်ဆိုင်မှု | Topic prefixes |
|---------|------|----------------|
| **Markets** | Equity/FX/commodity quotes, history, watchlists, sectors | `market:*`, `watchlist:*` |
| **News** | Aggregation, clustering, monitors, dedup, deviation | `news:*` |
| **Economics** | FRED, DBnomics, government indicators | `econ:*` |
| **Geopolitics** | HDX events, ACLED, maritime | `geopolitics:*` |
| **Trading** | Live brokers, paper trading, order matching, positions | `broker:<id>:*`, `paper:*` |
| **Portfolio** | Holdings aggregation, P&L, allocation | `portfolio:*` |
| **Crypto** | Hyperliquid, Kraken, on-chain, wallets | `ws:<exchange>:*`, `wallet:*` |
| **Derivatives** | Option chains, F&O, surface analytics | `derivatives:*` |
| **Predictions** | Polymarket, Kalshi, internal | `prediction:*` |
| **Agents** | Hedge fund, geopolitics, economics, trader, finagent | `agent:<kind>:run:<id>` |
| **AI Chat** | LLM provider routing, prompt context, history | (event-driven, DataHub မဟုတ်) |
| **Workflow** | Node editor, DAG executor, scheduled flows | `workflow:*` |
| **Identity** | Auth, sessions, profile, billing | (event-driven) |

အင်္ဂါရပ်အသစ်တစ်ခုသည် context တစ်ခုတည်းတွင် အပြည့်အဝနေထိုင်သည်၊ သို့မဟုတ် ရှင်းလင်းစွာ cross-cutting ဖြစ်သည် (refactor plan တွင် မှတ်တမ်းတင်ထားသည်)။

---

## ၅။ ရင်းမြစ်ဖွဲ့စည်းပုံ (လက်ရှိ၊ အကျဉ်းချုပ်)

```
fincept-qt/
├── CMakeLists.txt                  # Monolithic build (ပစ်မှတ်: per-module CMakes)
├── CMakePresets.json
├── DATAHUB_ARCHITECTURE.md         # Data plane အတွက် သတ်မှတ်ချက်
├── DATAHUB_PHASES.md
├── cmake/                          # Toolchain အကူများ
├── packaging/                      # Per-OS installer scripts
├── resources/                      # Icons, fonts, translations
├── scripts/                        # Python scripts ၁,၄၂၃ ခု (analytics, agents, data)
└── src/
    ├── app/                        # Entry point, WindowFrame, routers
    ├── core/                       # Infrastructure: events, logging, session, config…
    ├── ui/                         # Reusable Qt widgets (Obsidian design system)
    ├── network/                    # HTTP + WebSocket clients
    ├── storage/                    # Database, CacheManager, SecureStorage, repositories
    ├── auth/                       # AuthManager, AuthApi, SessionGuard
    ├── python/                     # PythonRunner (subprocess bridge)
    ├── datahub/                    # DataHub pub/sub data plane
    ├── mcp/                        # Model Context Protocol bridge + tool registry
    ├── trading/                    # BrokerInterface, brokers ၁၆ ခု, exchanges, matching
    ├── services/                   # bounded-context service ~၅၀
    └── screens/                    # Screen ၅၄ ခု (Presentation)
```

### ၅.၁ Subsystem မြေပုံ

#### `app/`
- `main.cpp` — bootstrap (QApplication, theme, Python setup, splash, first window)။
- `WindowFrame.{cpp,h}` — main window orchestrator (~1100 LOC); ADS dock manager, command bar, function-key bar, status bar ကိုထိန်းသည်။ **လေးလံသော်လည်း ရည်ရွယ်ချက်ရှိ**။
- `WindowFrame_Setup.cpp` — screen factory ၅၄ ခုကို `DockScreenRouter` သို့ lazy ဖြင့် မှတ်ပုံတင်သည်။
- `DockScreenRouter.{cpp,h}` — router အစစ်အမှန် (`ScreenRouter.{cpp,h}` ရှိသော်လည်း legacy/unused)။

#### `core/`
- `config/AppConfig` — global constants (URLs, versions)။ Singleton။
- `events/EventBus` — string-keyed pub/sub (`O(n)` linear scan; callsite ၃၉ ခု)။ **Typed event manifest ဖြင့် ထုပ်ပိုးမည်။**
- `logging/Logger` — structured logging macros။
- `result/Result<T>` — error type, network/DB ရလဒ်များတွင်သုံးသည်။
- `session/SessionManager` — frame/panel/last-screen persistence။
- `identity/`, `profile/` — user-side state။
- `screen/`, `layout/`, `panel/`, `window/` — frame layout, ADS integration။
- `keys/`, `actions/`, `components/` — keybindings, command palette, popularity tracking။
- `crash/`, `debug/`, `telemetry/` — diagnostics။
- `symbol/` — symbol ref / drag-drop MIME (အသစ် `Instrument` model နှင့် ပေါင်းစည်းရန် စီစဉ်ထား)။
- `net/` — bandwidth meter။
- `report/` — report-builder primitives။

#### `ui/`
- `theme/` — color tokens, font constants, stylesheets။
- `widgets/`, `components/`, `tables/`, `charts/`, `markdown/` — design-system components။
- `navigation/` — nav bar, F-key bar, status bar, toolbar။
- `command/` — command palette။
- `workspace/`, `pushpins/`, `notifications/`, `error/`, `debug/` — orthogonal UI surfaces။

#### `network/`
- `http/HttpClient` — `QNetworkAccessManager` wrapper with context-scoped callbacks (dangling pointers မရှိ)။
- `websocket/WebSocketClient` — Qt6 WebSocket wrapper။

#### `storage/`
- `sqlite/Database` — main DB (auth, watchlists, portfolios, agent tasks, settings…)။
- `sqlite/CacheDatabase` — ephemeral cache အတွက် သီးခြား DB။
- `sqlite/migrations/` — forward-only versioned schema။
- `cache/CacheManager` — TTL key/value store on `CacheDatabase`။ တရားဝင် caching API။
- `cache/TabSessionStore` — per-tab UI state။
- `secure/SecureStorage` — **SQLite-backed AES-256-GCM** (key from `machineUniqueId`)။ Platform keychains (Keychain/DPAPI/libsecret) ကို ရည်ရွယ်ချက်ရှိရှိ မသုံးပါ။ `Database` ကိုအရင်ဖွင့်ရန် လိုအပ်သည်။
- `repositories/` — 26 typed CRUD adapters over `BaseRepository<T>`။
- `workspace/` — workspace-level persistence (multi-window layouts)။

#### `auth/`
- `AuthManager` — login state, JWT, guest mode။ **`AuthManager::session()` သည် fincept credentials ၏ canonical source ဖြစ်သည်**; `SettingsRepository` သည် fallback persistence copy တစ်ခုသာဖြစ်သည်။ Credentials ကို အခြားနေရာတွင် ဘယ်တော့မှ cache မလုပ်ရ။
- `AuthApi`, `UserApi` — server-side calls။
- `SessionGuard` — 401 တွင် auto-logout ။
- `lock/` — app-lock screen။

#### `python/`
- `PythonRunner` — `QProcess`-based subprocess bridge။
  - Managed venv နှစ်ခု: `venv-numpy1` (legacy NumPy 1.x libs like vectorbt/gluonts) နှင့် `venv-numpy2` (default)။
  - Concurrency cap သည် 3 processes; surplus queued ။
  - Cold-start 0.5–1.5 s per call; result သည် stdout ပေါ်ရှိ JSON ဖြစ်သည်။
  - API key ၂၂ ခုကို `SecureStorage` မှ per call တွင် ထိုးသွင်းသည်။ unmanaged credential-shaped env vars များကို ယိုစိမ့်မှုမဖြစ်စေရန် ဖယ်ရှားသည်။
- (ပစ်မှတ်) `python/ScriptCatalog` — hardcoded path strings များကို အစားထိုးသော name-resolved scripts။
- (ပစ်မှတ်) `python/WorkerPool` — hot paths အတွက် long-lived stdin/stdout protocol ဖြင့် ချည်နှောင်ထားသော persistent worker ။

#### `datahub/`
- `DataHub` — in-process pub/sub။ Topic format `domain:subdomain:id[:modifier]`။
- `Producer` — service-side interface (patterns, refresh, rate limit, on-idle)။
- `TopicPolicy` — TTL, min interval, push-only flag။
- Contract အတွက် [`DATAHUB_ARCHITECTURE.md`](../fincept-qt/DATAHUB_ARCHITECTURE.md) ကိုကြည့်ပါ။

#### `mcp/`
- `McpService` — AI chat, agents, node editor အတွက် unified tool surface။
- `McpProvider` — internal C++ tool registry။
- `McpManager` — external MCP server lifecycle နှင့် RPC။
- `dispatch/ToolDispatcher` — multi-round tool orchestration state machine။
- `dispatch/ProviderAdapter` — OpenAI / Anthropic / Gemini protocol shims။
- `tools/` — `MarketDataService`, `NewsService`, `AgentService`, `WatchlistTools`, `NotesTools`, `SettingsTools` စသည်တို့ကို ပေါင်းကူးသော tool implementation ၄၀+။

#### `trading/`
- `BrokerInterface.h` — base contract (virtual method ၃၂ ခု)။ **ယနေ့တွင် ကျယ်ပြန့်သော်လည်း တိမ်သည် (shallow-but-wide); refactor ပစ်မှတ်မှာ shared OAuth/mapping/parsing infrastructure ပါသော နက်ရှိုင်းသော `BrokerAdapter` ဖြစ်သည်။**
- `BrokerRegistry` — broker discovery။
- `brokers/BrokerHttp` — shared synchronous-blocking HTTP helper (`QEventLoop` သုံးသည်; **worker thread မှ ခေါ်ဆိုရမည်**)။
- `brokers/<name>/` — implementation ၁၆ ခု (Zerodha, Fyers, Upstox, IBKR, Alpaca, Saxo, Kotak, Angel One, Dhan, AliceBlue, FivePaisa, Groww, IIFL, Motilal, Shoonya, Tradier)။
- `exchanges/` — Hyperliquid, Kraken (crypto WebSocket)။
- `instruments/`, `auth/` — symbol parsing, broker OAuth flows။
- `OrderMatcher`, `PaperTrading`, `UnifiedTrading` — live routing, paper simulation, matching/SL/TP triggers အတွက် ညီညွတ်သော engine သုံးခု။
- `TradingTypes.h` — တည်ငြိမ်သော shared vocabulary (`UnifiedOrder`, `BrokerOrderInfo`, `BrokerPosition`, enums)။

#### `services/`
bounded context ၁၃ ခုအနှံ့ service ~၅၀။ ပုံစံသုံးမျိုး:

1. **Data services (ပုံမှန်)** ။ DataHub topic pattern တစ်ခု သို့မဟုတ် တစ်ခုထက်ပိုကို ပိုင်ဆိုင်ပြီး subscription demand ပေါ်တွင် refresh လုပ်သည်။ widget ကို ဘယ်တော့မှ ပြန်မပေးပို့ပါ။ UI thread မှ synchronous ဖြင့် ဘယ်တော့မှ မဖတ်ပါ။ ဥပမာများ: `MarketDataService`, `NewsService`, `EconomicsService`။
2. **Imperative services** ။ HTTP သို့မဟုတ် Python မှတဆင့် one-shot request/response (topic မရှိ, cache မရှိ)။ Search-as-you-type, ad-hoc tests စသည်တို့အတွက်သုံးသည်။ ဥပမာများ: `MarketSearchService`, `DataMappingTestClient`။ ၎င်းတို့သည် screens များ မထုပ်ပိုးနိုင်စေရန် `HttpClient`/`PythonRunner` ကို ထုပ်ပိုးသည်။
3. **UI-coordinator services** ။ UI requester များကိုယ်စား modal dialog lifecycles ကို ရည်ရွယ်ချက်ရှိရှိ ပိုင်ဆိုင်သည် — အကြောင်းမှာ dialog သည် user ၏ authority surface (wallet connect, update install, sign transaction) ဖြစ်သောကြောင့်ဖြစ်သည်။ ၎င်းတို့သည် dialog parenting အတွက် `QWidget*` ကို လက်ခံနိုင်သည် — ၎င်းသည် ရည်ရွယ်ချက်ရှိ၊ အကန့်အသတ်ရှိသော ခြွင်းချက်ဖြစ်သည်၊ ယိုစိမ့်မှုမဟုတ်ပါ။ ဥပမာများ: `WalletService`, `UpdateService`။ စည်းကမ်း: business logic သည် producer များတွင် ဆက်လက်တည်ရှိသည်။ dialog သည် service ပိုင်ဆိုင်ပြီးသော state machine အပေါ် pure UI shell တစ်ခုမျှသာဖြစ်သည်။

ယနေ့ခေတ် သတိပြုစရာများ: data-service ~၁၃ ခုသည် Python-script wrapper ပါးပါးများဖြစ်သည်။ ~၅ ခုသည် injected access မှတဆင့်မဟုတ်ဘဲ `SettingsRepository::instance()` သို့ တိုက်ရိုက်ရောက်ရှိသည်။ ဤအရာများကို refactor plan တွင် အမှတ်အသားပြုထားသည်။

#### `screens/`
Screen ၅၄ ခု။ စာချုပ်မှာ:

> Screen သည် state ကို rendering လုပ်ပြီး user input ကို လက်ခံသော `QWidget` subclass တစ်ခုဖြစ်သည်။ ၎င်းသည် `HttpClient` ကို တိုက်ရိုက်မခေါ်ပါ၊ caches ကို မပိုင်ဆိုင်ပါ၊ business logic (deduplication, deviation detection, risk calculation စသည်) မပါဝင်ပါ။

ယနေ့ခေတ် သတိပြုစရာများ: screen ၆ ခုသည် no-HTTP စည်းမျဉ်းကို ချိုးဖောက်သည်၊ screen ~၃ ခု (News, Derivatives) သည် services များတွင် ရှိသင့်သော domain logic ကို သယ်ဆောင်သည်။ ၎င်းတို့ကို refactor plan တွင် အမှတ်အသားပြုထားသည်။

---

## ၆။ Cross-cutting ကိစ္စများ

### ၆.၁ Threading

Qt ၏ main thread သည် UI thread ဖြစ်သည်။ microsecond ထက်ပိုကြာသော အရာတိုင်းသည် အခြားနေရာတွင် လုပ်ဆောင်ရမည်။

| ဘယ်မှာ ဘာလုပ်ဆောင်သည် | စည်းမျဉ်း |
|---|---|
| `QWidget`/`QPainter`/UI state | **Main thread သာ** (Qt requirement)။ |
| HTTP (`HttpClient`) | Worker via `QNetworkAccessManager`; callbacks များကို Qt's event loop မှတဆင့် marshalled back ။ |
| SQL (`Database`, repositories) | **Background** for any non-trivial query။ UI thread မှ synchronous repository calls များသည် သေးငယ်သော lookups အတွက်သာ သည်းခံသည်။ refactor plan သည် UI-thread query sites များကို ဖယ်ရှားသည်။ |
| Python (`PythonRunner`) | `QProcess` worker; results များကို signals မှတဆင့် ဖမ်းယူသည်။ အမြဲ async ။ |
| Broker REST (`BrokerHttp`) | Worker; internal `QEventLoop` သုံးသည် — ခေါ်ဆိုသူများသည် UI thread ပေါ်တွင် **မရှိရ**။ |
| WebSocket feeds | Exchange တစ်ခုစီ / source တစ်ခုစီအတွက် dedicated thread ။ |
| `DataHub::publish` | မည်သည့် thread မှမဆို ဘေးကင်းသည် (queued connection to dispatch slot)။ |
| Slot dispatch | Subscriber ၏ thread ပေါ်တွင် အမြဲ (Qt::AutoConnection)။ |

**ပစ်မှတ်ပုံစံ (new code):** async I/O အတွက် `QCoro::Task<T>` returning coroutines — callers များသည် HTTP, DB queries, Python calls များကို `co_await` လုပ်သည်။ ရှိပြီးသား callback code များသည် migrated ဖြစ်သည်အထိ ဆက်လက်ထိန်းသိမ်းထားသည်။

### ၆.၂ Async ပုံစံများ

| ပုံစံ | ဘယ်မှာသုံးသည် | အခြေအနေ |
|---|---|---|
| `QPointer` context ပါသော Callback | current async APIs အများစု | back-compat အတွက် ထိန်းသိမ်းထား |
| Qt signals + request IDs | ဝန်ဆောင်မှုအဟောင်းများ (Markets, Watchlist, Equity) | ထိန်းသိမ်းထား |
| `QFuture` / `QtConcurrent::run` | ပြန့်ကျဲနေ | QCoro ဖြင့် အစားထိုးမည် |
| `co_await` (QCoro) | new code | **ရှေ့ဆက်သွားရန် ဦးစားပေး** |

Data plane က ၎င်းကို ပုံမှန်ဖြစ်စေသည်: services များသည် မည်သို့ fetch လုပ်သည်ဖြစ်စေ DataHub သို့ publish လုပ်သည်။ UI subscribers များသည် topic updates များကိုသာ မြင်ရသည်။

### ၆.၃ Error handling

- `Result<T>` for synchronous fallible operations (DB queries, parsing)။
- Signals for async failure (`xxx_failed(QString message)`)။
- DataHub သည် producer failure တွင် **last-known-good** ကို ပေးသည်။ `TopicPolicy` အရ backoff ဖြင့် retries ။
- `SessionGuard` သည် fincept servers မှ HTTP 401 ပေါ်တွင် auto-logout လုပ်သည်။ broker 401s များကို adapter တစ်ခုစီအလိုက် ကိုင်တွယ်သည်။
- Logging via `LOG_ERROR("tag", "msg with " + context)`။ module နယ်နိမိတ်များတစ်လျှောက် exceptions မရှိပါ။

### ၆.၄ လုံခြုံရေး

| မျက်နှာပြင် | ယန္တရား |
|---|---|
| Credentials (broker tokens, API keys, fincept session) | `SecureStorage` — SQLite + AES-256-GCM။ Key ကို `machineUniqueId` မှ ဆင်းသက်လာသည်။ `Database` ဖွင့်ရန် လိုအပ်သည်။ |
| Fincept session token | `AuthManager::session()` သည် canonical ဖြစ်သည်။ `SettingsRepository` သည် fallback သာဖြစ်သည်။ |
| TLS | Qt6 ၏ OS-trust-store path အားလုံး HTTPS အတွက်။ |
| 401 handling | fincept အတွက် `SessionGuard` auto-logout; brokers အတွက် per-adapter token-refresh ။ |
| Python env injection | API key ၂၂ ခုကို whitelist လုပ်ထားသည်။ အခြား `*_API_KEY`/`*_SECRET`/`*_PASSWORD`/`*_TOKEN` env vars အားလုံးကို `/proc/<pid>/environ` မှတဆင့် ယိုစိမ့်မှုမဖြစ်စေရန် subprocess env မှ ဖယ်ရှားသည်။ |
| Disk ပေါ်ရှိ သိုလှောင်မှု | SQLite DB on disk; encryption key ကို local မှ ဆင်းသက်လာသည်။ အသုံးပြုသူက ၎င်းတို့၏ disk ကို encrypt မလုပ်ပါက OS မှ at-rest encrypted မဟုတ်ပါ။ |
| Source secrets | Repo တွင် မရှိပါ။ Env vars / `SecureStorage` သာ။ |

### ၆.၅ Build

ယနေ့: `CMakeLists.txt` တစ်ခုတည်း (~၃,၃၀၀ LOC)။

ပစ်မှတ်: per-module library targets, subdirectory `CMakeLists.txt` ဖိုင်များတွင် ကြေညာထားပြီး top-level config မှ ချိတ်ဆက်ထားသည်:

```
fincept_core         (QtCore မရှိလျှင် avoidable အနေဖြင့် Qt dependencies မရှိ)
fincept_ui           (fincept_core ပေါ်မူတည်)
fincept_network      (fincept_core ပေါ်မူတည်)
fincept_storage      (fincept_core ပေါ်မူတည်)
fincept_auth         (fincept_network, fincept_storage ပေါ်မူတည်)
fincept_datahub      (fincept_core, fincept_storage ပေါ်မူတည်)
fincept_python       (fincept_core ပေါ်မူတည်)
fincept_trading      (fincept_network, fincept_storage, fincept_datahub ပေါ်မူတည်)
fincept_mcp          (fincept_datahub, fincept_python ပေါ်မူတည်)
fincept_services_*   (bounded context တစ်ခုစီအတွက် target တစ်ခု)
fincept_screens_*    (context တစ်ခုစီ၏ screens များအတွက် target တစ်ခု, fincept_ui သို့ချိတ်ဆက်)
fincept_app          (entry point; အရာအားလုံးကို ချိတ်ဆက်)
```

Dependency direction ကို CMake မှ အတည်ပြုသည်။ Circular dependencies များ မဖြစ်နိုင်တော့ပါ။

---

## ၇။ Data flow

### ၇.၁ Subscription-driven fetch (DataHub path — ဦးစားပေး)

```
User က Markets screen ဖွင့်သည်
       │
       ▼
Screen က DataHub မှတဆင့် "market:quote:AAPL" သို့ subscribe လုပ်သည်
       │
       ▼
DataHub က CacheManager ကိုစစ်ဆေး → fresh? ချက်ချင်းပေးပို့။
                              stale? MarketDataProducer ကို အကြောင်းကြား။
       │
       ▼
MarketDataProducer.refresh({"market:quote:AAPL"})
       │
       ├─── HttpClient → JSON parse  (async)
       └─── or PythonRunner          (async)
       │
       ▼
Producer က hub.publish("market:quote:AAPL", quote) ခေါ်သည်
       │
       ▼
Subscriber အားလုံး (Markets, Watchlist, Dashboard, AI Chat) update ရရှိသည်။
CacheManager က persistent လုပ်သည်။
```

ဂုဏ်သတ္တိများ: (topic, source) တစ်ခုလျှင် fetch တစ်ကြိမ်; subscribers များကို အခမဲ့ fan out; cache နှင့် live-feed ပေါင်းစည်းထားသည်။

### ၇.၂ Imperative command (MCP / agent path)

```
LLM က tool call ထုတ်သည်: place_order(symbol="AAPL", qty=10, side="buy")
       │
       ▼
ToolDispatcher → McpService::execute_openai_function_async(...)
       │
       ▼
Internal McpProvider tool → UnifiedTrading::place_order(account_id, order)
       │
       ├─── PaperTrading (paper mode ဆိုလျှင်)
       └─── BrokerAdapter::place_order  (live)
       │
       ▼
Result ကို dispatcher သို့ ပြန်ပေးသည်။ DataHub topic broker:<id>:orders update လုပ်သည်။
       │
       ▼
broker:<id>:orders သို့ subscribe လုပ်ထားသော UI screens များ အလိုအလျောက် refresh ဖြစ်သည်။
```

### ၇.၃ Agentic mode

```
User က hedge-fund agent task စတင်သည်
       │
       ▼
AgentService::start_task(task_def)
       │
       ▼
PythonRunner က streaming callback ဖြင့် scripts/agents/finagent_core/main.py ကို spawn လုပ်သည်
       │
       ▼
Agent က stdout ပေါ်တွင် per-step events (JSON lines) ထုတ်သည်
       │
       ├─── agent_tasks SQLite table သို့ persistent (durable)
       └─── DataHub topic agent:<kind>:run:<id> ပေါ်တွင် publish
       │
       ▼
AiChatScreen နှင့် AgentConfigScreen တို့သည် live progress အတွက် topic သို့ subscribe လုပ်သည်။
```

Crash-resume သည် durable ဖြစ်သည် — state သည် SQL တွင် ရှင်သန်သည်။

---

## ၈။ အများပြည်သူ စာချုပ်များ

ဤအရာများသည် ပြင်ပပါဝင်ကူညီသူများနှင့် AI အကူများ တည်ငြိမ်သည်ဟု သတ်မှတ်သင့်သော မျက်နှာပြင်များဖြစ်သည်:

| စာချုပ် | ဘယ်မှာ | မှတ်ချက် |
|---|---|---|
| DataHub topic format | `datahub/DataHub.h` + `DATAHUB_ARCHITECTURE.md` | Topic strings များသည် ၎င်းတို့၏ contents ဖြင့် versioned ဖြစ်သည် (ဥပမာ `:1d` vs `:1m`)။ |
| `Producer` interface | `datahub/Producer.h` | ဒေတာရင်းမြစ်အသစ်များက ၎င်းကို implement လုပ်သည်။ |
| `IBroker` (`BrokerInterface.h`) | `trading/BrokerInterface.h` | ပစ်မှတ်: `BrokerAdapter` base သို့ နက်ရှိုင်းအောင်။ |
| `BrokerEnumMap<T>` (typed wire-value tables) | `trading/adapter/BrokerEnumMap.h` | Per-broker `<broker>_order_type/side/product` switches များကို data table ဖြင့် အစားထိုးသည်။ Zerodha မှ ပထမဆုံး လက်ခံကျင့်သုံး။ အခြား brokers များကို broker-by-broker ပြောင်းရွှေ့မည်။ |
| `Instrument` (canonical symbol vocabulary) | `trading/instruments/InstrumentTypes.h` | Cross-broker code အားလုံးက ၎င်းကိုသုံးသည် — broker strings raw ဘယ်တော့မှမသုံးရ။ `Instrument::canonical_topic_id()` က DataHub keys များကို တည်ဆောက်သည်။ |
| `InstrumentSource` + `SymbolResolver` | `trading/instruments/InstrumentSource.h`, `SymbolResolver.h` | Brokers အသစ်များက source တစ်ခုကို မှတ်ပုံတင်သည်။ `InstrumentService` သည် if-chain မဟုတ်ဘဲ resolver မှတဆင့် ပို့ဆောင်သည်။ |
| `IStatefulScreen` | `screens/common/IStatefulScreen.h` | UI state ကို persistent လုပ်ရန် လိုအပ်သော Screens များ။ |
| `BaseRepository<T>` | `storage/repositories/BaseRepository.h` | typed repos အသစ်အတွက် ပုံစံ။ |
| `BaseWidget` | `screens/dashboard/widgets/BaseWidget.h` | Dashboard widgets အသစ်များက ၎င်းကို အမွေဆက်ခံပြီး override လုပ်သည်။ |
| MCP tool definition | `mcp/McpProvider.h` | Tools များသည် ၎င်းတို့၏ registration တွင် JSON-schema'd ဖြစ်သည်။ |
| `Result<T>` | `core/result/Result.h` | Synchronous fallible return type။ |
| Logging macros | `core/logging/Logger.h` | `LOG_INFO/WARN/ERROR("tag", "msg")`။ |
| Theme tokens | `ui/theme/Theme.h` | Colors များကို မဖုတ်ပါနှင့်။ tokens များကိုသုံးပါ။ |

---

## ၉။ ပုံစံများနှင့် ဆန့်ကျင်ပုံစံများ

### ပုံစံများ (ဤသို့လုပ်ပါ)

- ဒေတာရင်းမြစ်အသစ် → `Producer` implement လုပ်၊ topic patterns မှတ်ပုံတင်၊ refresh ပေါ်တွင် publish လုပ်။
- Screen အသစ် → `DockScreenRouter` သို့ **factory** တစ်ခု မှတ်ပုံတင် (lazy)။ state သည် restart ခံနိုင်ရန် လိုအပ်လျှင် `IStatefulScreen` implement လုပ်။
- Broker အသစ် → (ပစ်မှတ်) `BrokerAdapter` ကို တိုးချဲ့; endpoint table, field-name map, error mapper ကို ပေးအပ်။ OAuth/HTTP/error-handling ကို ပြန်လည် implement မလုပ်ပါနှင့်။
- MCP tool အသစ် → `McpProvider` တွင် schema + handler ကို သတ်မှတ်။ Dispatcher သည် routing ကို ကိုင်တွယ်သည်။
- Python script အသစ် → (ပစ်မှတ်) `ScriptCatalog` တွင် မှတ်ပုံတင်; input/output schema ကြေညာ; name ဖြင့် ရည်ညွှန်း။
- Async အလုပ် → QCoro မှတဆင့် `co_await` (new code) သို့မဟုတ် request IDs ပါသော signals (ရှိပြီးသား)။

### ဆန့်ကျင်ပုံစံများ (ဤသို့မလုပ်ပါနှင့်)

- **Screen က `HttpClient::instance()` ကို တိုက်ရိုက်ခေါ်ဆိုခြင်း** ။ service (data, imperative, or `MarketSearchService`-style search) မှတဆင့် သွားပါ။
- **Data service က `QWidget*` ကို ထုတ်ဖော်ခြင်း** ။ Signals သာ။ UI-coordinator services များသည် ရှင်းလင်းသော ခြွင်းချက်ဖြစ်သည်။
- **Screens များတွင် `if`-trees တွင် broker enums များကို hard-coding** ။ `UnifiedOrder` + `BrokerAdapter` ကိုသုံးပါ။
- **Screen တစ်ခုတွင် `QHash` မှတဆင့် caching** ။ DataHub မှတဆင့် `CacheManager` ကိုသုံးပါ။ Screen တွင် `QHash<QString, ...>` field တစ်ခုသည် (က) hide လုပ်သည့်အခါ ရှင်းလင်းသော live-feed dispatch table ဖြစ်လျှင်၊ သို့မဟုတ် (ခ) DataHub မှ ပိုင်ဆိုင်ပြီးသား data ပေါ်ရှိ view/index တစ်ခုဖြစ်လျှင်သာ ခွင့်ပြုသည်။
- **Widget ၏ ကိုယ်ပိုင် event handler အတွင်းမှ `qApp->setStyleSheet(...)`** ။ Wayland တွင် crash ဖြစ်မည်။ Theme changes များကို ပေါင်းစည်းပြီး `Qt::QueuedConnection` မှတဆင့် ပို့ဆောင်ပါ။
- **`Database::open()` မလုပ်မီ `SecureStorage` ကိုခေါ်ဆိုခြင်း** ။ SecureStorage သည် DB ပေါ်တွင် မူတည်သည်။
- **Fincept credentials ကို caching** ။ `AuthManager::session()` မှ live ဖတ်ပါ။ `SettingsRepository` သည် fallback ဖြစ်သည်။
- **UI thread ပေါ်တွင် synchronous DB query** ။ ၎င်းကို Marshal လုပ်ပါ။
- **Singleton အသစ်ထည့်ခြင်း** ။ မိတ်ဆက်နေသော dependency container ကိုသုံးပါ။

---

## ၁၀။ သိထားရမည့် အားနည်းချက်များ (နှင့် ၎င်းတို့၏ ပိုင်ရှင်များ)

ဤအရာများသည် လက်ရှိနှင့် ပစ်မှတ်အခြေအနေကြား ခြားနားချက်များဖြစ်သည်။ တစ်ခုစီတွင် [`REFACTOR_PLAN.md`](REFACTOR_PLAN.md) ၏ အဆင့်တစ်ခုရှိသည်။

| အားနည်းချက် | သက်ရောက်မှု | အဆင့် |
|---|---|---|
| broker ၁၆ ခု၏ implementation များတွင် ၆၀–၇၅% duplication — `BrokerEnumMap` migration **14/16 ပြီးစီး** (Alpaca + FivePaisa ဆိုင်းငံ့ထား; ၎င်းတို့တွင် named helpers ဘယ်တော့မှမရှိ)။ Envelope/parse helpers များလာရန် ကျန်ရှိ | လျော့ကျသွားသည်။ ပိုကြီးသော အမြတ်များက Phase 4.3+ တွင် စောင့်ကြိုနေဆဲ | 4.x |
| ~~Symbol/Instrument model သည် representation ၃ ခုတွင် ကွဲပြားနေသည်~~ — canonical `Instrument` ရှိပြီးသား; `SymbolResolver` seam ကို Phase 3 တွင် ထည့်သည် | per-broker migration သို့ ကျဆင်းသွားသည် (Phase 4) | 3 |
| ~~Screen ၄ ခုက `HttpClient` ကို တိုက်ရိုက်ခေါ်သည်~~ ✅ Phase 2 တွင် ဖြေရှင်းပြီး (via `MarketSearchService` + `DataMappingTestClient`) | — | 2 |
| ~~Wallet/Update services များက `QWidget*` ကို ထုတ်ဖော်သည်~~ — UI-coordinator services အဖြစ် ပြန်ခွဲခြားသည် (ရည်ရွယ်ချက်ရှိ) | — | 2 |
| `::instance()` ဖြင့် ဝင်ရောက်သော singleton ၄၀+ | Test မဖြစ်နိုင်ခြေ; static-init ordering စွန့်စားမှု | 9 |
| `EventBus` သည် stringly-typed, O(n) lookup, phantom events ဖြစ်နိုင် | တိတ်တဆိတ် ကျိုးခြင်း | 10 |
| Python-wrapper service ပါးပါး ၁၃–၁၅ ခု | အယူအဆဆိုင်ရာ overhead | 5 |
| CacheManager + ad-hoc QHash + service debouncers ဟူသော 3-way caching | Drift, double-fetch | 6 |
| Service ၁၀+ တွင် hardcoded Python script paths | Rename လုပ်ပါက build ကျိုးသည် | 11 |
| Python cold-start 0.5–1.5 s × per call | UX latency | 11 |
| Screens များ ဘယ်တော့မှ unload မလုပ် — window တစ်ခုလျှင် 100–250 MB resident | Multi-window users များအတွက် အဆမတွင်ပေးဆောင်ရသည် | 12 |
| ရှင်းလင်းသော single-concern မပါဘဲ screen ဖိုင် ၅ ခု > 1,000 LOC | ထိန်းသိမ်းနိုင်မှု | 13 |
| Monolithic 3,300-LOC `CMakeLists.txt` | Dependency direction အတည်မပြုနိုင်ခြင်း | 8 |
| `GenericBroker.h` သည် dead code; utility header ပါးပါး ~၁၂ ခု | သိမြင်မှုဆိုင်ရာ ဝန်ထုပ်ဝန်ပိုး | 1 |
| UI-thread SQL query sites အချို့ | Frame stutter | 7 |

---

## ၁၁။ လည်ပတ်မှုဆိုင်ရာ အသေးစိတ်များ

### Bundled Python

- UV က `python-3.11.9` ကို bootstrap လုပ်ပြီး `.aqt-venv/` အောက်တွင် venv နှစ်ခုကို အပြိုင်ဖန်တီးသည်။
- ပထမဆုံးအကြိမ် run ချိန်တွင် package ~၁၅၀ တပ်ဆင်သည်။ broadband ပေါ်တွင် ~၃–၅ မိနစ်ကြာသည်။
- နောက်ဆက်တွဲ run များအတွက် cached လုပ်ထားသည်။
- Venvs များသည် install dir မဟုတ်ဘဲ user data dir တွင် နေထိုင်သည်။

### အပ်ဒိတ်များ

- `UpdateService` က `fincept.in` မှ `updates.json` ကို စစ်ဆေးသည်။ in-app updater (Windows/macOS) ကို ပေးသည်။
- Linux builds များသည် system Qt packages ပေါ်တွင် မူတည်ပြီး auto-update မလုပ်ပါ။

### Telemetry

- `core/telemetry/` သည် opt-in crash and usage reporting ကို ပေးသည်။
- ပုံမှန်အားဖြင့် off ဖြစ်သည်။ `SettingsScreen` တွင် အသုံးပြုသူ၏ ရှင်းလင်းသော consent လိုအပ်သည်။

---

## ၁၂။ ဆက်သွယ်ရန်နှင့် လုပ်ငန်းစဉ်

- **Issues:** https://github.com/Fincept-Corporation/FinceptTerminal/issues
- **Contributing:** `CONTRIBUTING.md`, `CPP_CONTRIBUTOR_GUIDE.md`, `PYTHON_CONTRIBUTOR_GUIDE.md` ကိုကြည့်ပါ။
- **Architecture ဆုံးဖြတ်ချက်များ:** ဤနေရာ၏ အပိုင်းတစ်ခုကို ပြောင်းပြန်လှန်ခြင်း သို့မဟုတ် ပြန်လည်ပြုပြင်သည့်အခါ `docs/adr/` အောက်တွင် ADR တစ်ခုထည့်ပါ။
- **Discord:** https://discord.gg/ae87a8ygbN
- **Email:** support@fincept.in

---

**ဗားရှင်း:** 5.0.0-draft (ဗိသုကာ ပြန်လည်ဆန်းသစ်မှု)
**အစားထိုးသည်:** ယခင် 4.0.1 ဗိသုကာ မှတ်တမ်း (မတ်လ ၂၀၂၆)
**နောက်ဆုံးမွမ်းမံသည်:** 2026-05-15
