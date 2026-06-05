
# C++ ပါဝင်ကူညီသူ လမ်းညွှန်

ဤလမ်းညွှန်ချက်သည် Fincept Terminal အတွက် C++ ဖွံ့ဖြိုးတိုးတက်မှုကို လွှမ်းခြုံထားသည် — screen ၄၀+, core infrastructure, trading engine, နှင့် Qt6 UI ။

> **လိုအပ်ချက်များ**: တပ်ဆင်ခြင်းနှင့် အလုပ်အသွယ်သွယ်အတွက် [ပါဝင်ကူညီသူ လမ်းညွှန်](./CONTRIBUTING.md) ကို ဦးစွာဖတ်ပါ။

---

## သတ်မှတ်ထားသော Toolchain (ညှိနှိုင်း၍မရ)

CMake သည် ဤဗားရှင်းများကို `FATAL_ERROR` ဖြင့် အတည်ပြုသည်။ ဖွံ့ဖြိုးတိုးတက်မှု၊ ပြန်လည်သုံးသပ်ခြင်း သို့မဟုတ် patch များတင်သွင်းသည့်အခါ ဤဗားရှင်းအတိအကျကိုသာ သုံးပါ-

| Tool | ဗားရှင်း |
|------|---------|
| C++ စံ | C++20 |
| MSVC (Windows) | 19.38 — VS 2022 17.8 |
| GCC (Linux) | 12.3 |
| Apple Clang (macOS) | 15.0 — Xcode 15.2 |
| CMake | 3.27.7 |
| Ninja | 1.11.1 |
| Qt | 6.7.2 (LTS) — `find_package` က `EXACT` သုံးသည် |
| Python | 3.11.9 |

**Configure + build** ကို CMake presets မှတဆင့် ပြုလုပ်ပါ:
```bash
cmake --preset <win-release|linux-release|macos-release>
cmake --build --preset <same-preset>
```

> **စက်အဟောင်း သို့မဟုတ် RAM အကန့်အသတ်ရှိသူများ:** `--parallel 4` (သို့မဟုတ် ဂဏန်းသေးသေးတစ်ခု) ထည့်ပါ။ ပုံမှန်အားဖြင့် core အားလုံးကို ပြည့်ဝစွာသုံးသည်၊ ၎င်းသည် စက်အဟောင်းများကို အပူလွန်စေပြီး သင့်စနစ်၏ကျန်အပိုင်းကို နှေးစေနိုင်သည်။ ဥပမာ: `cmake --build --preset macos-release --parallel 4`.

Debug variants: `win-debug`, `linux-debug`, `macos-debug`။ `fincept-qt/CMakePresets.json` ကိုကြည့်ပါ။

**အရေးပေါ် အစားထိုးချက်သာ** — ဒေသခံစမ်းသပ်မှုများအတွက် Qt `EXACT` စစ်ဆေးမှုကို ကျော်လွှားရန် `-DFINCEPT_ALLOW_QT_DRIFT=ON` ကို ပေးပို့ပါ။ ဤနည်းဖြင့် တည်ဆောက်ထားသော အလုပ်ကို ဘယ်တော့မှ မတင်ပါနှင့်။ CI က ငြင်းပယ်လိမ့်မည်။

---

## ခြုံငုံသုံးသပ်ချက်

C++ codebase သည် အောက်ပါတို့ကို ကိုင်တွယ်သည်-
- **Screen ၄၀+** — ငွေကြေးဆိုင်ရာ ကိရိယာများ၊ ပိုင်းခြားစိတ်ဖြာမှု၊ အရောင်းအဝယ်ဆိုင်ရာ မျက်နှာပြင်များ
- **Core Infrastructure** — HTTP, database, logging, event bus
- **Trading Engine** — Broker ပေါင်းစည်းမှုများ၊ order matching, WebSocket streams
- **Python Bridge** — ပိုင်းခြားစိတ်ဖြာမှု script ၁၀၀+ ကို လုပ်ဆောင်သည်
- **MCP Integration** — AI tools များအတွက် Model Context Protocol

**ဆက်စပ်လမ်းညွှန်ချက်များ:**
- [Python လမ်းညွှန်](./PYTHON_CONTRIBUTOR_GUIDE.md) — C++ မှ လုပ်ဆောင်သော ပိုင်းခြားစိတ်ဖြာမှု scripts များ

---

## စီမံကိန်းဖွဲ့စည်းပုံ

```
fincept-qt/src/
├── app/
│   ├── main.cpp                    # Entry point, QApplication setup
│   ├── MainWindow.cpp/h            # Main window, layout, screen hosting
│   └── ScreenRouter.cpp/h          # QStackedWidget-based navigation
│
├── core/                           # Shared infrastructure
│   ├── config/AppConfig.cpp/h      # App-wide constants (URLs, versions)
│   ├── events/EventBus.cpp/h       # Pub/sub messaging
│   ├── logging/Logger.cpp/h        # Structured logging (LOG_INFO, LOG_ERROR)
│   ├── result/Result.h             # Result<T> error handling
│   └── session/SessionManager.cpp/h
│
├── ui/                             # Reusable Qt widgets (Obsidian design system)
│   ├── theme/                      # StyleSheets, color tokens, fonts
│   ├── widgets/                    # Card, SearchBar, StatusBadge, TabHeader, etc.
│   ├── tables/DataTable.cpp/h      # Reusable data table
│   ├── charts/ChartFactory.cpp/h   # Qt6 Charts factory
│   └── navigation/                 # NavigationBar, StatusBar, FKeyBar, ToolBar
│
├── network/
│   ├── http/HttpClient.cpp/h       # QNetworkAccessManager wrapper
│   └── websocket/WebSocketClient.cpp/h
│
├── storage/
│   ├── sqlite/                     # Database + migrations
│   ├── cache/                      # CacheManager, TabSessionStore
│   ├── secure/SecureStorage.cpp/h  # Encrypted credential storage
│   └── repositories/              # Data access object ၁၃ ခု
│
├── auth/                           # AuthManager, AuthApi, SessionGuard
├── python/PythonRunner.cpp/h       # Python scripts များကို လုပ်ဆောင်သည်
├── trading/                        # Trading engine + broker adapter ၂၀+
├── services/                       # MarketDataService, NewsService
│
└── screens/                        # Terminal screens
    ├── dashboard/                  # Main dashboard + widget ၁၃ ခု
    ├── markets/
    ├── news/
    ├── watchlist/
    ├── crypto_trading/
    ├── report_builder/
    ├── auth/                       # Login, Register, ForgotPassword, Pricing
    ├── profile/
    ├── settings/
    ├── support/
    ├── about/
    └── ComingSoonScreen.cpp/h      # လာမည့် screens များအတွက် နေရာထိန်း
```

---

## အဓိက စည်းမျဉ်းများ

### ၁. ခွဲခြားမှု: Screens နှင့် Services
- **Screens** (`*Screen.cpp`) သည် UI ကိုသာ ပြန်ဆိုသည် — HTTP calls မရှိ၊ business logic မရှိ
- **Services** (`*Service.cpp`) သည် fetching, caching, processing ကို ကိုင်တွယ်သည်
- Screens များသည် services များသို့ Qt signals/slots မှတဆင့် ချိတ်ဆက်သည်၊ `HttpClient` ကို တိုက်ရိုက်ဘယ်တော့မှ မခေါ်ဆိုရ

### ၂. Core Infrastructure ကိုသုံးပါ
- raw error codes အစား error handling အတွက် `Result<T>` ကိုသုံးပါ
- logging အတွက် `LOG_INFO("tag", "message")` / `LOG_ERROR("tag", "message")` ကိုသုံးပါ
- cross-module communication အတွက် `EventBus::instance().publish("event.type", data)` ကိုသုံးပါ
- constants များအတွက် `AppConfig::instance().api_base_url()` ကိုသုံးပါ — magic strings မသုံးရ

### ၃. UI Widgets ကိုသုံးပါ
- panel containers အတွက် `fincept::ui::Card`
- ဇယားဒေတာအတွက် `fincept::ui::DataTable`
- ရှာဖွေရမှုအတွက် `fincept::ui::SearchBar`
- အခြေအနေညွှန်းများအတွက် `fincept::ui::StatusBadge`
- `fincept::ui::apply_global_stylesheet()` မှတဆင့် stylesheets များကို အသုံးပြုပါ
- `DESIGN_SYSTEM.md` (Obsidian) ကို လိုက်နာပါ — color tokens, spacing, typography

### ၄. Namespace ညီညွတ်မှု
```cpp
namespace fincept {         // အထက်ဆုံးအဆင့်
namespace fincept::ui {     // UI အစိတ်အပိုင်းများ
namespace fincept::auth {   // auth module
// စသည်
}
}
```

### ၅. Qt Signals & Slots
- pointer-to-member syntax အသစ်ကိုသုံးပါ: `connect(src, &Src::signal, dst, &Dst::slot)`
- အဟောင်း string-based `SIGNAL()`/`SLOT()` macros များကို ဘယ်တော့မှ မသုံးပါနှင့်
- QObject subclasses အားလုံးတွင် class body ထဲ၌ `Q_OBJECT` ပါရမည်

### ၆. Threading
- UI code သည် main thread ပေါ်တွင်သာ လည်ပတ်သည် (Qt requirement)
- Background work အတွက် `QThread` သို့မဟုတ် `QtConcurrent::run` ကိုသုံးပါ
- threads များကြား signals မှတဆင့် ရလဒ်များကို UI thread သို့ ပြန်ပို့ပါ (Qt သည် marshalling ကို အလိုအလျောက် ကိုင်တွယ်သည်)
- shared non-Qt state ကို `QMutex` ဖြင့် ကာကွယ်ပါ

---

## Screen အသစ်ထည့်ခြင်း

1. Folder ဖန်တီးပါ: `src/screens/your_feature/`
2. ဖိုင်များဖန်တီးပါ:
   - `YourScreen.h/.cpp` — `QWidget` subclass, `Q_OBJECT` ထည့်ပါ
   - `YourService.h/.cpp` — data fetching/processing (လိုအပ်ပါက)
   - `YourTypes.h` — shared data types (လိုအပ်ပါက)
3. `.cpp` ဖိုင်များကို `CMakeLists.txt` ၏ `SCREEN_SOURCES` စာရင်းသို့ ထည့်ပါ
4. `src/app/MainWindow.cpp` တွင် `ScreenRouter` မှတဆင့် screen ကို မှတ်ပုံတင်ပါ
5. `src/ui/navigation/NavigationBar.cpp` တွင် navigation entry ထည့်ပါ
6. Build လုပ်ပြီး စမ်းသပ်ပါ

### အနည်းဆုံး Screen နမူနာ

```cpp
// YourScreen.h
#pragma once
#include <QWidget>

namespace fincept {

class YourScreen : public QWidget {
    Q_OBJECT
public:
    explicit YourScreen(QWidget* parent = nullptr);
};

} // namespace fincept
```

```cpp
// YourScreen.cpp
#include "YourScreen.h"
#include "ui/theme/StyleSheets.h"
#include <QVBoxLayout>
#include <QLabel>

namespace fincept {

YourScreen::YourScreen(QWidget* parent) : QWidget(parent) {
    auto* layout = new QVBoxLayout(this);
    layout->addWidget(new QLabel("Your Screen", this));
}

} // namespace fincept
```

---

## Code ပုံစံ

- 4-space indent, 120 column ကန့်သတ်ချက်
- functions/variables အတွက် `snake_case`, classes/types အတွက် `PascalCase`
- member variables များအတွက် နောက်ဆုံးတွင် underscore: `data_`, `loading_`
- `using namespace std;` မသုံးရ — ရှင်းလင်းသော `std::` prefix ကိုသုံးပါ
- `using namespace Qt` မသုံးရ — လိုအပ်သည့်နေရာတွင် ရှင်းလင်းသော `Qt::` prefix ကိုသုံးပါ

---

## Commit မက်ဆေ့ချ်များ

```
feat: add crypto trading screen
fix: resolve WebSocket reconnection crash
refactor: extract market data service from dashboard
docs: update CONTRIBUTING.md with new architecture
perf: optimize order book rendering
```

---

## Pull Request စစ်ဆေးရန်စာရင်း

- [ ] Code သည် သတိပေးချက်များမပါဘဲ compile ဖြစ်သည် (`-Wall -Wextra`)
- [ ] duplicated code မရှိ — `core/` နှင့် `ui/widgets/` ကိုသုံးပါ
- [ ] Screens များသည် HTTP ကို တိုက်ရိုက်မခေါ်ပါ
- [ ] `.cpp` ဖိုင်အသစ်များကို `CMakeLists.txt` သို့ ထည့်ထားသည်
- [ ] QObject subclasses အားလုံးတွင် `Q_OBJECT` ပါရှိသည်
- [ ] Signals/slots များသည် pointer-to-member syntax ကိုသုံးသည်
- [ ] Obsidian design system (`DESIGN_SYSTEM.md`) ကို လိုက်နာသည်

---

**မေးခွန်းများ** [GitHub](https://github.com/Fincept-Corporation/FinceptTerminal) တွင် issue ဖွင့်ပါ။
