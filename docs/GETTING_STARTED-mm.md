
# Fincept Terminal ဖွံ့ဖြိုးတိုးတက်ရေး စတင်ရန် လမ်းညွှန်
## သင်၏ ပြီးပြည့်စုံသော အဆင့်မြှင့်တင်ရေးလမ်းညွှန်

Fincept Terminal သို့ ကြိုဆိုပါသည်။ ဤလမ်းညွှန်ချက်သည် သင်ကို သုညမှ သင်၏ ပထမဆုံး ပါဝင်ကူညီမှုအထိ ပို့ဆောင်ပေးပါလိမ့်မည်။

---

## Fincept Terminal ဆိုတာ ဘာလဲ။

Fincept Terminal သည် **open-source ငွေကြေးဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာမှု ပလက်ဖောင်း** တစ်ခုဖြစ်သည် — ခေတ်မမီတော့သော ပရော်ဖက်ရှင်နယ် ငွေကြေး terminal များအတွက် အခမဲ့၊ open-source အစားထိုးတစ်ခုဖြစ်သည်။ ဗားရှင်း 4 သည် Qt6 ဖြင့် တည်ဆောက်ထားသော native C++20 application တစ်ခုဖြစ်သည်။

### ခြုံငုံသုံးသပ်ချက်

**လက်ရှိအဖြစ်မှန်-**
- ခေတ်မမီတော့သော ပရော်ဖက်ရှင်နယ် terminal များ- အသုံးပြုသူတစ်ဦးလျှင် $၂၀,၀၀၀+/နှစ်
- လုပ်ငန်းဆိုင်ရာ စံသတ်မှတ်ထားသော စျေးကွက်ဒေတာ subscription များ- $၂၀,၀၀၀+/နှစ်
- ပရော်ဖက်ရှင်နယ်ကိရိယာများသည် ငွေပေးချေမှုနောက်ကွယ်တွင် သော့ခတ်ခံထားရသည်။

**ကျွန်ုပ်တို့၏ မျှော်မှန်းချက်-**
- ၁၀၀% အခမဲ့နှင့် open source
- ဒေတာရင်းမြစ် ၁၀၀+ ကို ပေါင်းစည်းခြင်း (စတော့များ၊ cryptocurrency, forex, စီးပွားရေးဒေတာ၊ သတင်းများ စသည်)
- AI အခြေပြု ပိုင်းခြားစိတ်ဖြာမှုနှင့် ထိုးထွင်းသိမြင်မှုများ
- Native C++ စွမ်းဆောင်ရည် — browser/JS overhead မရှိ
- အသိုင်းအဝိုင်းက တည်ဆောက်သည်၊ အသိုင်းအဝိုင်းအတွက်ဖြစ်သည်။

### နည်းပညာ ဆုံးဖြတ်ချက်များ (နှင့် အကြောင်းရင်း)

- **C++20 + Qt6** — Native စွမ်းဆောင်ရည်၊ ပြေပြစ်သော UI၊ binary တစ်ခုတည်း
- **Qt6 Network + WebSockets** — Platform ဖြတ်ကျော် networking၊ TLS ပါဝင်ပြီးသား
- **Qt6 Charts** — အပိုထပ်ဆောင်း dependencies မပါဘဲ ငွေကြေးဆိုင်ရာ ဇယားဆွဲခြင်း
- **Qt6 Sql (SQLite)** — မြန်ဆန်မှုအတွက် ဒေတာ local caching
- **Embedded Python** — ကျယ်ပြန့်သော ငွေကြေးဆိုင်ရာ library များ၏ ဂေဟစနစ်သို့ ဝင်ရောက်နိုင်မှု (yfinance, pandas စသည်)

---

## အမြန် တပ်ဆင်ခြင်း

### လိုအပ်ချက်များ (သတ်မှတ်ဗားရှင်းများ — လိုအပ်သည်)

> **ဤဗားရှင်းအတိအကျကိုသာ သုံးပါ။** Fincept Terminal ၏ CMake build သည် ၎င်းတို့ကို `FATAL_ERROR` စစ်ဆေးမှုများဖြင့် အတည်ပြုသည်။ အသစ်/အဟောင်း ဗားရှင်းများသည် configure လုပ်ရန် ငြင်းဆန်လိမ့်မည်။

| Tool | သတ်မှတ်ဗားရှင်း |
|------|----------------|
| **C++ compiler** | MSVC 19.38 (VS 2022 17.8) / GCC 12.3 / Apple Clang 15.0 (Xcode 15.2) |
| **CMake** | 3.27.7 |
| **Ninja** | 1.11.1 |
| **Qt** | 6.8.3 |
| **Python** | 3.11.9 |
| **Platform SDK** | Win10 SDK 10.0.22621.0 / macOS SDK 14.0 (deploy target 11.0) / glibc 2.31+ |

#### 1. C++20 compiler တပ်ဆင်ပါ

- **Windows**: [Visual Studio 2022 17.8+](https://visualstudio.microsoft.com/) "Desktop development with C++" ဖြင့်
- **Linux (Ubuntu 22.04+)**: `sudo apt install -y g++-12`
- **macOS**: Xcode 15.2 — `xcode-select --install`

#### 2. CMake 3.27.7 + Ninja 1.11.1 တပ်ဆင်ပါ

- **Windows**: `winget install Kitware.CMake Ninja-build.Ninja`
- **Linux**: `sudo apt install -y cmake ninja-build` (`cmake --version` ≥ 3.27.7 ကို စစ်ဆေးပါ။ မဟုတ်ပါက [cmake.org](https://cmake.org/download/) မှ ဒေါင်းလုဒ်လုပ်ပါ)
- **macOS**: `brew install cmake ninja`

#### 3. Qt 6.8.3 တပ်ဆင်ပါ

**Windows / Linux / macOS** — Qt online installer (အကြံပြုသည်):
- https://www.q​t.io/download-qt-installer မှ ဒေါင်းလုဒ်လုပ်ပါ
- အတိအကျ **Qt 6.8.3** → platform အလိုက် သင့်လျော်သော kit (MSVC 2022 64-bit / Desktop gcc 64-bit / macOS) ကို ရွေးပါ
- ပုံမှန် တပ်ဆင်လမ်းကြောင်းများ: `C:/Qt/6.8.3/msvc2022_64`, `~/Qt/6.8.3/gcc_64`, `~/Qt/6.8.3/macos`

**Linux system packages (အခြားနည်းလမ်း — 6.8.3 အတိအကျ မဟုတ်နိုင်ပါ):**
```bash
sudo apt install -y \
  qt6-base-dev qt6-charts-dev qt6-tools-dev qt6-base-private-dev \
  libqt6sql6-sqlite libqt6websockets6-dev \
  libgl1-mesa-dev libglu1-mesa-dev
```

#### 4. Python 3.11.9 တပ်ဆင်ပါ

- **Windows**: [python.org 3.11.9](https://www.python.org/downloads/release/python-3119/) ("Add to PATH" ကို စစ်ဆေးပါ)
- **Linux**: `sudo apt install python3.11` (သို့မဟုတ် သင့် distro က 3.10 ပို့ဆောင်ပါက source မှ build လုပ်ပါ)
- **macOS**: `brew install python@3.11`

### အဆင့် ၃ ဆင့်ဖြင့် တပ်ဆင်ခြင်း (CMake presets သုံးခြင်း)

```bash
# 1. Clone
git clone https://github.com/Fincept-Corporation/FinceptTerminal.git
cd FinceptTerminal/fincept-qt

# 2. Configure + Build (သင်၏ platform ကိုရွေးပါ)
cmake --preset win-release     && cmake --build --preset win-release     # Windows (Dev Cmd for VS 2022)
cmake --preset linux-release   && cmake --build --preset linux-release   # Linux
cmake --preset macos-release   && cmake --build --preset macos-release   # macOS

# စက်အဟောင်း သို့မဟုတ် RAM အကန့်အသတ်ရှိသူများ: core အားလုံးကို မပြည့်ဝစေရန် concurrent compile jobs ကို ကန့်သတ်ပါ (ပုံမှန်အပြုအမူ)။
# ဥပမာ:
#   cmake --build --preset macos-release --parallel 4

# 3. Run
./build/linux-release/FinceptTerminal              # Linux
./build/macos-release/FinceptTerminal.app/Contents/MacOS/FinceptTerminal   # macOS
.\build\win-release\FinceptTerminal.exe            # Windows
```

**CMake presets များက သင့် Qt path ကို ရှာမတွေ့ပါက**၊ manual configure ကိုသုံးပါ:
```bash
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_PREFIX_PATH="<path/to/Qt/6.8.3/<kit>>"
cmake --build build
```

**မျှော်မှန်းရလဒ်:** Fincept Terminal window ပွင့်လာပြီး login screen ကိုပြသသင့်သည်။

### အရာအားလုံး အလုပ်လုပ်ကြောင်း စစ်ဆေးပါ

App ပွင့်လာသည်နှင့်:

1. **"Continue as Guest" ကို နှိပ်ပါ** — ဖွံ့ဖြိုးတိုးတက်မှုအတွက် မှတ်ပုံတင်ရန် မလိုအပ်ပါ
2. **Markets tab သို့ သွားပါ** — စျေးကွက်ဒေတာကို မြင်သင့်သည်
3. **Tabs များပြောင်းကြည့်ပါ** — Dashboard, Markets, News စသည်

---

## Codebase ကို နားလည်ခြင်း

### စီမံကိန်းဖွဲ့စည်းပုံ

```
FinceptTerminal/
│
├── fincept-qt/                     ← အဓိက application (သင်ဤနေရာတွင် အလုပ်လုပ်မည်)
│   ├── src/                        ← C++ ရင်းမြစ်ကုဒ်
│   │   ├── app/                    ← Entry point, MainWindow, ScreenRouter
│   │   ├── core/                   ← မျှဝေထားသော infrastructure
│   │   │   ├── config/             ← App-wide constants
│   │   │   ├── events/             ← Pub/sub messaging (EventBus)
│   │   │   ├── logging/            ← Structured logging (Logger)
│   │   │   ├── result/             ← Result<T> error handling
│   │   │   └── session/            ← Session management
│   │   ├── ui/                     ← Reusable Qt widgets
│   │   │   ├── theme/              ← Obsidian design system (StyleSheets)
│   │   │   ├── widgets/            ← Card, SearchBar, StatusBadge, etc.
│   │   │   ├── charts/             ← ChartFactory (Qt6 Charts)
│   │   │   ├── tables/             ← DataTable
│   │   │   └── navigation/         ← NavigationBar, StatusBar, FKeyBar
│   │   ├── network/                ← HTTP client, WebSocket client
│   │   ├── storage/                ← SQLite databases + repositories
│   │   ├── auth/                   ← Authentication (JWT, guest mode)
│   │   ├── python/                 ← Python runtime bridge
│   │   ├── trading/                ← Trading engine + 20+ brokers
│   │   ├── services/               ← Market data, news services
│   │   └── screens/                ← Terminal screens
│   │       ├── dashboard/
│   │       ├── markets/
│   │       ├── crypto_trading/
│   │       ├── news/
│   │       ├── watchlist/
│   │       ├── report_builder/
│   │       └── ...
│   │
│   ├── scripts/                    ← Python analytics scripts
│   │   ├── Analytics/              ← Analytics modules
│   │   ├── agents/                 ← AI agent frameworks
│   │   └── *.py                    ← 100+ data fetchers
│   │
│   ├── CMakeLists.txt              ← Build configuration
│   └── DESIGN_SYSTEM.md           ← Obsidian UI/UX spec
│
└── docs/                           ← Documentation
```

### ဒေတာ စီးဆင်းပုံ

```
User က "Get AAPL quote" ကိုနှိပ်သည်
        │
        ▼
Screen (MarketsScreen.cpp)          ← UI rendering သာ
        │
        ▼
Data Service (MarketDataService)    ← Fetching + caching
        │
        ├─── HTTP API call (QNetworkAccessManager)
        │         │
        │         ▼
        │    Parse JSON (QJsonDocument)
        │
        └─── Python Script (ရွေးချယ်နိုင်)
                  │
                  ▼
             PythonRunner က script ကို execute လုပ်သည်
                  │
                  ▼
             C++ သို့ JSON ပြန်ပေးသည်
        │
        ▼
UI က ဒေတာဖြင့် အပ်ဒိတ်လုပ်သည်
```

### အဓိက ပုံစံများ

| သင်ဘာလုပ်ချင်လျှင်... | ဤအရာကို ကြည့်ပါ |
|--------------------|-------------|
| Screen အသစ်ထည့်ရန် | `src/screens/dashboard/` (နမူနာအဖြစ်) |
| Data fetcher အသစ်ထည့်ရန် | `scripts/yfinance_data.py` (နမူနာအဖြစ်) |
| UI widgets သုံးရန် | `src/ui/widgets/` |
| HTTP calls လုပ်ရန် | `src/network/http/HttpClient.cpp` |
| ဒေတာကို local တွင် သိမ်းရန် | `src/storage/sqlite/Database.cpp` |
| Python analytics module ထည့်ရန် | `scripts/Analytics/` |
| Broker တစ်ခုထည့်ရန် | `src/trading/brokers/` |
| Screen တစ်ခုကို style လုပ်ရန် | `src/ui/theme/StyleSheets.cpp` + `DESIGN_SYSTEM.md` |

---

## သင်၏ ပထမဆုံး ပါဝင်ကူညီမှု

### Path A: လွယ်ကူသည် (၁၅ မိနစ်)
**စာရွက်စာတမ်းကို မြှင့်တင်ပါ** — `.md` ဖိုင်တစ်ခုခုတွင် စာလုံးပေါင်းအမှား သို့မဟုတ် မရှင်းလင်းသော ရှင်းလင်းချက်ကို ပြင်ပါ။

### Path B: လွယ်ကူသည် (မိနစ် ၃၀)
**Python Data Fetcher တစ်ခုထည့်ပါ** — `scripts/yfinance_data.py` ရှိ ပုံစံကို လိုက်နာ၍ အခမဲ့ API အသစ်တစ်ခုအတွက် wrapper တစ်ခုဖန်တီးပါ။

### Path C: အလယ်အလတ် (၁-၂ နာရီ)
**Screen အသစ်တစ်ခုထည့်ပါ** — `src/screens/` ရှိ ပုံစံများကို လိုက်နာ၍ Qt6 screen အသစ်တစ်ခုထည့်ပါ။

### Path D: အဆင့်မြင့် (၂-၄ နာရီ)
**Broker Integration တစ်ခုထည့်ပါ** — `src/trading/brokers/` ရှိ `BrokerInterface` ပုံစံကို လိုက်နာပါ။

---

## အသုံးများသော ဖွံ့ဖြိုးတိုးတက်ရေး အလုပ်အသွယ်သွယ်များ

### အလုပ်အသွယ်သွယ် 1: Python Script အသစ်ထည့်ခြင်း

```bash
cd fincept-qt/scripts

# Script ကိုဖန်တီးပါ
# yfinance_data.py ပုံစံကို လိုက်နာပါ: CLI args → JSON stdout

# Standalone စမ်းသပ်ပါ
python my_script.py command arg1 arg2

# Resources တွင်ထည့်သွင်းရန် ပြန်တည်ဆောက်ပါ
cd ..
cmake --build build --config Release
```

### အလုပ်အသွယ်သွယ် 2: Screen အသစ်ထည့်ခြင်း

1. Folder ဖန်တီးပါ: `src/screens/your_feature/`
2. ဖိုင်များဖန်တီးပါ: `YourScreen.h/.cpp` (`QWidget` subclass)
3. `.cpp` ဖိုင်ကို `CMakeLists.txt` ၏ `SCREEN_SOURCES` စာရင်းသို့ ထည့်ပါ
4. `src/app/MainWindow.cpp` တွင် `ScreenRouter` မှတဆင့် screen ကို မှတ်ပုံတင်ပါ
5. `NavigationBar` တွင် navigation entry ထည့်ပါ
6. Build လုပ်ပြီး စမ်းသပ်ပါ

### အလုပ်အသွယ်သွယ် 3: Bug ဖြေရှင်းခြင်း

```bash
git checkout -b fix/issue-123-description
# Issue ကိုဖြေရှင်းပါ
cmake --build build --config Release
# ပြင်ဆင်ချက်ကို စမ်းသပ်ပါ
git add <files>
git commit -m "fix: resolve market data loading crash"
git push origin fix/issue-123-description
```

---

## Code ပုံစံ

- 4-space indent, 120 column ကန့်သတ်ချက်
- Functions/variables အတွက် `snake_case`, types/classes အတွက် `PascalCase`
- Member variables အတွက် နောက်ဆုံးတွင် underscore: `data_`, `loading_`
- `using namespace std;` မသုံးရ — ရှင်းလင်းသော `std::` prefix ကိုသုံးပါ
- QObject subclasses အားလုံးတွင် `Q_OBJECT` macro ကိုသုံးပါ
- Signals/slots များကို pointer-to-member syntax အသစ်ဖြင့် ချိတ်ဆက်ပါ

---

## ပြဿနာဖြေရှင်းခြင်း

### CMake က Qt6 ကိုရှာမတွေ့ပါ
```bash
# CMAKE_PREFIX_PATH ကို သင့် Qt တပ်ဆင်မှုသို့ သတ်မှတ်ပါ
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH="/path/to/Qt/6.x.x/platform"

# Linux: Qt6 dev packages များ တပ်ဆင်ထားကြောင်း စစ်ဆေးပါ
dpkg -l | grep qt6
```

### Python script ကိုရှာမတွေ့ပါ
```bash
# Python သည် PATH တွင်ရှိကြောင်း စစ်ဆေးပါ
python --version

# Script ကို တိုက်ရိုက်စမ်းသပ်ပါ
python scripts/yfinance_data.py quote AAPL
```

### Linux တွင် OpenGL အမှားများ
```bash
# OpenGL dev packages များ တပ်ဆင်ပါ (Qt6 rendering အတွက် လိုအပ်သည်)
sudo apt install libgl1-mesa-dev libglu1-mesa-dev
```

---

## အကူအညီရယူခြင်း

| Channel | Link |
|---------|------|
| GitHub Issues | https://github.com/Fincept-Corporation/FinceptTerminal/issues |
| GitHub Discussions | https://github.com/Fincept-Corporation/FinceptTerminal/discussions |
| Discord | https://discord.gg/ae87a8ygbN |
| Email | support@fincept.in |

---

**ပါဝင်ကူညီရန် အသင့်ဖြစ်ပြီလား။**
- Issue တစ်ခုရွေးပါ: https://github.com/Fincept-Corporation/FinceptTerminal/issues
- C++ လမ်းညွှန်ကိုဖတ်ပါ: [fincept-qt/CONTRIBUTING.md](../fincept-qt/CONTRIBUTING.md)
- Python လမ်းညွှန်ကိုဖတ်ပါ: [PYTHON_CONTRIBUTOR_GUIDE.md](./PYTHON_CONTRIBUTOR_GUIDE.md)
