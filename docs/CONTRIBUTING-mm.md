
# Fincept Terminal သို့ ပါဝင်ကူညီခြင်း

Fincept Terminal သည် open-source native C++20/Qt6 ငွေကြေးဆိုင်ရာ ထောက်လှမ်းမှု ပလက်ဖောင်းတစ်ခုဖြစ်ပြီး screen ၅၀+, embedded Python analytics, နှင့် data connector ၁၀၀+ ပါဝင်သည်။ ဤလမ်းညွှန်ချက်သည် ပါဝင်ကူညီသူများအတွက် စံသတ်မှတ်ထားသော **မည်သို့လုပ်ဆောင်ရမည်** ကို ဖော်ပြသည် — build, architecture, conventions.

> **PR မဖွင့်မီ**၊ ပါဝင်ကူညီမှုဆိုင်ရာ **မူဝါဒ** ကိုလည်း ဖတ်ပါ: [`.github/CONTRIBUTING.md`](../.github/CONTRIBUTING.md)။ မူဝါဒက မည်သည့် PR များကို လက်ခံသည်ကို သတ်မှတ်သည် (အတည်ပြုထားသော issue, အနိမ့်ဆုံးနယ်ပယ်, auto-formatter အပြောင်းအလဲမပါ, စသည်); ဤဖိုင်သည် မည်သို့ build လုပ်ရန်နှင့် code မည်သည့်နေရာတွင်ရှိသည်ကို ဖုံးအုပ်ထားသည်။

---

## အကြောင်းအရာများ

- [ပါဝင်ကူညီနိုင်သော နည်းလမ်းများ](#ပါဝင်ကူညီနိုင်သော-နည်းလမ်းများ)
- [နည်းပညာအစုအစဉ်](#နည်းပညာအစုအစဉ်)
- [လိုအပ်ချက်များ](#လိုအပ်ချက်များ)
- [Build လုပ်ခြင်း](#build-လုပ်ခြင်း)
- [Run လုပ်ခြင်း](#run-လုပ်ခြင်း)
- [စီမံကိန်းဗိသုကာ](#စီမံကိန်းဗိသုကာ)
- [Code ညီညွတ်မှုများ](#code-ညီညွတ်မှုများ)
- [ဖွံ့ဖြိုးတိုးတက်ရေး အလုပ်အသွယ်သွယ်](#ဖွံ့ဖြိုးတိုးတက်ရေး-အလုပ်အသွယ်သွယ်)
- [ဘာသာစကားအလိုက် လမ်းညွှန်ချက်များ](#ဘာသာစကားအလိုက်-လမ်းညွှန်ချက်များ)
- [အကူအညီရယူခြင်း](#အကူအညီရယူခြင်း)

---

## ပါဝင်ကူညီနိုင်သော နည်းလမ်းများ

| နယ်ပယ်            | ဥပမာများ                                                          |
|-------------------|-------------------------------------------------------------------|
| C++ / Qt          | Screens အသစ်, ဝန်ဆောင်မှုများ, core infrastructure, စွမ်းဆောင်ရည် ပြင်ဆင်မှုများ     |
| Python            | Analytics scripts, AI agents, ဒေတာရယူသူများ                       |
| Data sources      | Broker ပေါင်းစည်းမှုများ, အစိုးရ/စျေးကွက် ဒေတာ connector များ          |
| Documentation     | ကျိုးနေသော/ခေတ်မမီတော့သော စာရွက်စာတမ်းများကို ပြင်ဆင်ခြင်း (မူဝါဒအတွင်း နယ်ပယ်ကိုကြည့်ပါ) |
| Testing           | Bugs များကို ပြန်ထုတ်ခြင်း, PR များကို သုံးသပ်ခြင်း, tests များရေးသားခြင်း |

---

## နည်းပညာအစုအစဉ်

| အလွှာ          | နည်းပညာ                                                                           |
|----------------|--------------------------------------------------------------------------------------|
| ဘာသာစကား      | **C++20** — MSVC 19.38 (VS 2022 17.8) / GCC 12.3 / Apple Clang 15.0                  |
| UI             | **Qt 6.8.3 EXACT** Widgets (သတ်မှတ်ထား)                                                  |
| Charts         | Qt6 Charts                                                                           |
| Networking     | Qt6 Network + Qt6 WebSockets                                                         |
| Database       | Qt6 Sql (SQLite)                                                                     |
| Analytics      | Embedded **Python 3.11.9** (script 4000+)                                           |
| Excel I/O      | QXlsx v1.4.9 (FetchContent, သတ်မှတ် commit)                                           |
| Mapping        | QGeoView (သတ်မှတ် commit)                                                             |
| Build          | **CMake 3.27.7 + Ninja 1.11.1** (သတ်မှတ်ထား, unity build)                                |

---

## လိုအပ်ချက်များ

သတ်မှတ်ထားသော toolchain ဗားရှင်းများ — CMake မှ အတည်ပြုသည်။ ကွဲပြားပါက ရှင်းလင်းသော fail-fast အမှားကို ထုတ်ပေးသည်။

| Tool          | ဗားရှင်း                                                                                 |
|---------------|-----------------------------------------------------------------------------------------|
| C++ compiler  | MSVC 19.38 (VS 2022 17.8) / GCC 12.3 / Apple Clang 15.0 (Xcode 15.2)                    |
| CMake         | **3.27.7** — [cmake.org](https://cmake.org/download/)                                   |
| Ninja         | **1.11.1** — [releases](https://github.com/ninja-build/ninja/releases)                  |
| Qt            | **6.8.3** — [Qt Online Installer](https://www.qt.io/download-qt-installer)              |
| Python        | **3.11.9** — [python.org](https://www.python.org/downloads/release/python-3119/)        |
| Git           | နောက်ဆုံးထွက် — [git-scm.com](https://git-scm.com)                                             |

Optional (ပြန် build မြန်ဆန်စေသည်): Windows တွင် **ccache 4.13.4** ကို auto-detected လုပ်သည်။

---

## Build လုပ်ခြင်း

### အမြန်ဆုံး — automated setup script

```bash
git clone https://github.com/Fincept-Corporation/FinceptTerminal.git
cd FinceptTerminal
./setup.sh      # Linux / macOS — aqtinstall မှတဆင့် toolchain + Qt တပ်ဆင်ပြီး build လုပ်သည်
setup.bat       # Windows — VS 2022 Developer Command Prompt မှ run ပါ
```

### Manual — CMake presets

နေ့စဉ် ဖွံ့ဖြိုးတိုးတက်ရေးအားလုံးသည် `fincept-qt/CMakePresets.json` မှ presets များကို သုံးသည်။

```bash
cd fincept-qt

# Configure (တစ်ကြိမ်သာ၊ သို့မဟုတ် CMakeLists.txt ပြောင်းလဲပြီးနောက်)
cmake --preset win-release        # Windows
cmake --preset linux-release      # Linux
cmake --preset macos-release      # macOS

# Build (code အပြောင်းအလဲတိုင်းပြီးနောက် run ပါ — incremental)
cmake --build --preset win-release        # Windows
cmake --build --preset linux-release      # Linux
cmake --build --preset macos-release      # macOS
```

> **စက်အဟောင်း သို့မဟုတ် RAM အကန့်အသတ်ရှိသူများ:** concurrent compile jobs ကို ကန့်သတ်ရန် `--parallel 4` (သို့မဟုတ် ဂဏန်းသေးသေးတစ်ခု) ထည့်ပါ။ ပုံမှန်အားဖြင့် core အားလုံးကို ပြည့်ဝစွာသုံးသည်၊ ၎င်းသည် စက်အဟောင်းများကို အပူလွန်စေပြီး သင့်စနစ်၏ကျန်အပိုင်းကို နှေးစေနိုင်သည်။ ဥပမာ: `cmake --build --preset macos-release --parallel 4`.

Debug builds: command နှစ်ခုလုံးတွင် `release` ကို `debug` ဖြင့် အစားထိုးပါ။

Presets များက Qt ကို ရှာမတွေ့ပါက manual configure:

```powershell
cmake -B build/win-release -G Ninja -DCMAKE_BUILD_TYPE=Release `
  -DCMAKE_PREFIX_PATH="C:/Qt/6.8.3/msvc2022_64"
cmake --build build/win-release
```

---

## Run လုပ်ခြင်း

```bash
.\build\win-release\FinceptTerminal.exe                                           # Windows
./build/linux-release/FinceptTerminal                                             # Linux
./build/macos-release/FinceptTerminal.app/Contents/MacOS/FinceptTerminal          # macOS
```

---

## စီမံကိန်းဗိသုကာ

### Repository အပြင်အဆင်

```
finceptTerminal/
├── fincept-qt/                 # အဓိက C++ application (ဖွံ့ဖြိုးတိုးတက်မှုအားလုံး ဤနေရာတွင်)
│   ├── src/                    # C++ ရင်းမြစ်
│   ├── scripts/                # Embedded Python analytics (ဖိုင် 4000+)
│   ├── resources/              # Icons, assets, Qt resources
│   ├── CMakeLists.txt
│   ├── CMakePresets.json
│   ├── CLAUDE.md               # စွမ်းဆောင်ရည်/ဗိသုကာ စည်းမျဉ်းများ (P1–P15, D1–D5)
│   └── DESIGN_SYSTEM.md        # Obsidian UI/UX spec
├── docs/                       # Repository-wide စာရွက်စာတမ်း (ဤဖိုင် ဤနေရာတွင်)
├── .github/                    # Issue/PR templates, workflows, contribution policy
└── README.md
```

### C++ ရင်းမြစ် အပြင်အဆင် (`fincept-qt/src/`)

```
src/
├── app/              # Entry point, MainWindow, routing, splash
├── core/             # Config, events, logging, Result<T>, session
├── ui/               # Theme, reusable widgets, tables, charts, navigation
├── network/          # http/ နှင့် websocket/ clients
├── storage/          # SQLite, cache, secure storage, repository ၁၆ ခု
├── auth/             # Guest + registered auth, JWT
├── python/           # Embedded Python bridge, PythonRunner
├── datahub/          # DataHub producers/consumers (DATAHUB_ARCHITECTURE.md ကိုကြည့်ပါ)
├── services/         # service domain ၁၈ ခု — market data, news, agents, workflow, စသည်
├── trading/          # Trading core + broker integrations ၁၈ ခု
├── mcp/              # Model Context Protocol infrastructure (tool module ၂၄ ခု)
├── ai_chat/          # AI chat UI + LlmService
└── screens/          # Terminal screen ၅၀+, တစ်ခုချင်းစီ subdirectory တစ်ခု
```

### Python scripts (`fincept-qt/scripts/`)

```
scripts/
├── Analytics/        # Analytics modules — equity, portfolio, derivatives,
│                     #   fixed income, economics, corporate finance
├── agents/           # AI agent frameworks (finagent_core, Geopolitics, HedgeFund, …)
├── ai_quant_lab/     # ML, factor discovery, HFT, RL trading, vision quant
├── agno_trading/     # Agno-based trading agents
└── *.py              # top-level data fetcher ၁၀၀+ (market, gov, economic, alt)
```

အသေးစိတ် service catalog နှင့် ဗိသုကာ စည်းမျဉ်းများအတွက် `fincept-qt/CLAUDE.md` ကိုကြည့်ပါ။

---

## Code ညီညွတ်မှုများ

### C++

- **စံ:** C++20
- **အမည်သတ်မှတ်ခြင်း:** functions/variables အတွက် `snake_case`, types/classes အတွက် `PascalCase`
- **Namespaces:** `namespace fincept {}`, `namespace fincept::ui {}`; `using namespace std;` ကို ဘယ်တော့မှ မသုံးရ
- **Qt:** QObject subclasses အားလုံးတွင် `Q_OBJECT`; pointer-to-member signal/slot syntax သာ (string macros `SIGNAL()`/`SLOT()` ကို ဘယ်တော့မှ မသုံးရ)
- **Error handling:** `Result<T>` — module နယ်နိမိတ်များတစ်လျှောက် raw exception-based APIs မရှိရ
- **Logging:** `LOG_INFO` / `LOG_WARN` / `LOG_ERROR` / `LOG_DEBUG` ကို context tag ဖြင့် သုံးပါ၊ ဥပမာ `LOG_INFO("MarketData", "Fetched 12 quotes")`။ API keys သို့မဟုတ် credentials များကို ဘယ်တော့မှ log မလုပ်ပါနှင့်။

### Python

- **ပုံစံ:** ဝန်းကျင်ရှိ code နှင့် ကိုက်ညီပါ။ ရှိပြီးသား ဖိုင်များတွင် **Black / autopep8 / isort ကို မလုပ်ဆောင်ပါနှင့်။**
- **Logging:** `logging` module ကိုသုံးပါ (`logger = logging.getLogger(__name__)`, `logger.info(...)`), `print()` ကို ဘယ်တော့မှ မသုံးရ။ Scripts များသည် embedded Qt runtime အတွင်းတွင် လည်ပတ်သည်။ `print()` output သည် user ထံ ဘယ်တော့မှ မရောက်ရှိပါ။
- **Entry points:** C++ သည် scripts များကို `PythonRunner::instance().run(...)` မှတဆင့် ခေါ်ဆိုသည် — သင်၏ script သည် stdin ပေါ်တွင် JSON payload ကို လက်ခံရရှိပြီး stdout ပေါ်တွင် JSON ကို ပြန်ပေးရမည်။ `scripts/` ရှိ ရှိပြီးသား scripts များတွင် ပုံစံကိုကြည့်ပါ။

### UI / design

`fincept-qt/DESIGN_SYSTEM.md` (Obsidian design system) ကို လိုက်နာပါ။ ad-hoc အရောင်တန်ဖိုးများ၊ စာလုံးအချိုးအစား၊ အကွာအဝေးများကို မထည့်ပါနှင့် — tokens များကိုသုံးပါ။

### မဖြစ်မနေ ဗိသုကာ စည်းမျဉ်းများ (အကျဉ်းချုပ်)

အသေးစိတ်အပြည့်အစုံကို `fincept-qt/CLAUDE.md` တွင် ဖော်ပြထားသည်။ ဤအရာများသည် **ညှိနှိုင်း၍မရ** — ၎င်းတို့ကို ချိုးဖောက်သော PR များကို ပြန်ပို့မည်-

- **P1.** UI thread ကို ဘယ်တော့မှ မပိတ်ဆို့ရ — main thread တွင် `waitForFinished()` မသုံးရ။
- **P2.** Lazy screen တည်ဆောက်ခြင်း — ဒေတာရယူသည့် screen အတွက် `register_factory()` ကိုသုံးပါ။
- **P3.** Timers များကို constructor များတွင် မဟုတ်ဘဲ `showEvent()` / `hideEvent()` တွင် start/stop လုပ်ရမည်။
- **P4.** Python subprocesses များသည် `PythonRunner` (အပြိုင် အများဆုံး 3) မှတဆင့် သွားရမည်။
- **P6.** Screens များသည် UI ကိုသာ ပြန်ဆိုသည်။ services များက fetching, caching, processing အားလုံးကို လုပ်ဆောင်သည်။
- **P14.** Logs များသည် C++ တွင် `LOG_*` macros သို့မဟုတ် Python တွင် `logger` ကို သုံးရမည်။ `printf` / `print` / `std::cout` ကို ဘယ်တော့မှ မသုံးရ။
- **D1–D5.** Streaming data သည် DataHub မှတဆင့် စီးဆင်းရမည် — screen မှ Python ကို တိုက်ရိုက် ဘယ်တော့မှ spawn မလုပ်ရ။ `fincept-qt/DATAHUB_ARCHITECTURE.md` ကိုကြည့်ပါ။

---

## ဖွံ့ဖြိုးတိုးတက်ရေး အလုပ်အသွယ်သွယ်

### Branch အမည်သတ်မှတ်ခြင်း

```
feat/add-options-screen
fix/chart-render-crash
docs/update-python-guide
perf/market-data-coalesce
refactor/broker-http
```

သင့် fork ၏ `main` မှ PR ကို ဘယ်တော့မှ မဖွင့်ပါနှင့် — အမြဲတမ်း topic branch တစ်ခုဖြစ်ရမည်။

### Commit ပုံစံ

```
type: short imperative subject line

Optional body explaining why, not what. Wrap at ~72 cols.

Types: feat, fix, docs, refactor, test, chore, perf
```

### PR မဖွင့်မီ

1. သင်ဖြေရှင်းနေသော issue တွင် အောက်ပါတစ်ခုခု ရှိကြောင်း အတည်ပြုပါ- `good-first-issue`, `help-wanted`, `scope:approved` ([`.github/CONTRIBUTING.md`](../.github/CONTRIBUTING.md) ကိုကြည့်ပါ)။
2. Local တွင် build လုပ်ပြီး app ကို run ပါ — ပြင်ဆင်ချက်/အင်္ဂါရပ်သည် end-to-end အလုပ်လုပ်ကြောင်း အတည်ပြုပါ။
3. Diff ကို အနည်းဆုံးထားပါ။ auto-formatter အပြောင်းအလဲ မပါရ။
4. PR တမ်းပလိတ်ကို ရိုးသားစွာ ဖြည့်ပါ။ သင်အတည်မပြုရသေးသော အကွက်များကို အမှတ်ခြစ်မထားပါနှင့်။

ပြန်လည်သုံးသပ်ခြင်း လုပ်ငန်းစဉ်၊ နယ်ပယ်အပေါက်၊ နှင့် close-on-sight စာရင်းတို့ကို [`.github/CONTRIBUTING.md`](../.github/CONTRIBUTING.md) တွင် ဖော်ပြထားသည်။

---

## ဘာသာစကားအလိုက် လမ်းညွှန်ချက်များ

| လမ်းညွှန်                                                   | အကျုံးဝင်မှု                                                  |
|---------------------------------------------------------|-----------------------------------------------------------|
| [C++ လမ်းညွှန်](./CPP_CONTRIBUTOR_GUIDE.md)                 | Screens, services, core infrastructure, widgets, Qt ပုံစံများ |
| [Python လမ်းညွှန်](./PYTHON_CONTRIBUTOR_GUIDE.md)           | Analytics modules, data fetchers, AI agents, PythonRunner စာချုပ် |
| [ဗိသုကာ](./ARCHITECTURE.md)                       | စနစ်ဒီဇိုင်း, module နယ်နိမိတ်များ, ဒေတာစီးဆင်းမှု               |

---

## အကူအညီရယူခြင်း

| Channel      | Link                                                                          |
|--------------|-------------------------------------------------------------------------------|
| Issues       | [GitHub Issues](https://github.com/Fincept-Corporation/FinceptTerminal/issues) |
| Discussions  | [GitHub Discussions](https://github.com/Fincept-Corporation/FinceptTerminal/discussions) |
| Discord      | [discord.gg/ae87a8ygbN](https://discord.gg/ae87a8ygbN)                         |
| Email        | support@fincept.in                                                             |

ကောင်းမွန်သော ပထမဆုံး issue များသည် `good-first-issue` အမှတ်အသားပါရှိသည် — ၎င်းတို့သည် ပါဝင်ကူညီသူအသစ်များအတွက် မှန်ကန်သော စတင်မှတ်ဖြစ်သည်။

---

**Repository:** https://github.com/Fincept-Corporation/FinceptTerminal
**လိုင်စင်:** AGPL-3.0-or-later
