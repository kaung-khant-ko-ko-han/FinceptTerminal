
# Python ပါဝင်ကူညီသူ လမ်းညွှန်

ဤလမ်းညွှန်ချက်သည် Fincept Terminal အတွက် Python ဖွံ့ဖြိုးတိုးတက်မှုကို လွှမ်းခြုံထားသည် — ပိုင်းခြားစိတ်ဖြာမှု module ၃၄ ခုနှင့် ဒေတာရယူသူ ၈၀+ အပါအဝင် script ၁၀၀+ ရှိသည်။

> **လိုအပ်ချက်များ**: တပ်ဆင်ခြင်းနှင့် အလုပ်အသွယ်သွယ်အတွက် [ပါဝင်ကူညီသူ လမ်းညွှန်](./CONTRIBUTING.md) ကို ဦးစွာဖတ်ပါ။

---

## ခြုံငုံသုံးသပ်ချက်

Python သည် Fincept Terminal ၏ ပိုင်းခြားစိတ်ဖြာမှုနှင့် ဒေတာစွမ်းရည်များကို မောင်းနှင်သည်-
- **ပိုင်းခြားစိတ်ဖြာမှု module ၃၄ ခု** — ငွေကြေးဆိုင်ရာ တွက်ချက်မှုများ၊ အစုစု ပိုမိုကောင်းမွန်အောင်ပြုလုပ်ခြင်း၊ ML မော်ဒယ်များ
- **ဒေတာရယူသူ ၈၀+** — စျေးကွက်ဒေတာ၊ စီးပွားရေး၊ အစိုးရရင်းမြစ်များအတွက် API များ
- **AI အေးဂျင့်များ** — ပထဝီနိုင်ငံရေးဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာမှု၊ အရောင်းအဝယ်ဗျူဟာများ
- **နည်းပညာဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာမှု** — အညွှန်းကိန်းများနှင့် ဇယားပုံစံများ

Python script များကို C++ application မှ `python_runner.cpp` မှတဆင့် လုပ်ဆောင်ပြီး JSON ဖြင့် stdout ပေါ်တွင် ဆက်သွယ်သည်။

**ဆက်စပ်လမ်းညွှန်ချက်များ:**
- [C++ လမ်းညွှန်](../fincept-cpp/CONTRIBUTING.md) — C++ က Python ကို မည်သို့လုပ်ဆောင်ပြီး ရလဒ်များကို မည်သို့ပြန်ဆိုသည်ကို ရှင်းပြထားသည်။

---

## စီမံကိန်းဖွဲ့စည်းပုံ

```
fincept-cpp/scripts/                   # Python script ၁၀၀+
│
├── Analytics/                         # module ၃၄ ခု
│   ├── equityInvestment/              # စတော့တန်ဖိုးဖြတ်ခြင်း၊ DCF
│   ├── portfolioManagement/           # အစုစု ပိုမိုကောင်းမွန်အောင်ပြုလုပ်ခြင်း
│   ├── derivatives/                   # အော်ပရှင်စျေးနှုန်းသတ်မှတ်ခြင်း၊ Greeks
│   ├── fixedIncome/                   # ငွေချေးစာချုပ် ပိုင်းခြားစိတ်ဖြာခြင်း
│   ├── corporateFinance/              # ပေါင်းစည်းခြင်း/ဝယ်ယူခြင်း၊ တန်ဖိုးဖြတ်ခြင်း
│   ├── economics/                     # စီးပွားရေးမော်ဒယ်များ
│   ├── quant/                         # အရေအတွက်ဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာခြင်း
│   ├── alternateInvestment/           # အခြားသော ပိုင်ဆိုင်မှုများ
│   ├── backtesting/                   # ဗျူဟာနောက်ကြောင်းပြန်စမ်းသပ်ခြင်း
│   ├── finanicalanalysis/             # ဘဏ္ဍာရေးရှင်းတမ်းများ
│   ├── technical_analysis/            # နည်းပညာဆိုင်ရာ အညွှန်းကိန်းများ
│   │
│   ├── pyportfolioopt_wrapper/        # အစုစု ပိုမိုကောင်းမွန်အောင်ပြုလုပ်ခြင်း
│   ├── quantstats_analytics.py        # အစုစုဆိုင်ရာ မက်ထရစ်များ
│   ├── skfolio_wrapper.py             # Scikit-portfolio
│   ├── riskfoliolib_wrapper.py        # Risk-folio lib
│   ├── talipp_wrapper/                # နည်းပညာဆိုင်ရာ အညွှန်းကိန်းများ
│   ├── gs_quant_wrapper/              # Goldman Sachs Quant
│   ├── gluonts_wrapper/               # အချိန်စီးရီး ကြိုတင်ခန့်မှန်းခြင်း
│   ├── statsmodels_wrapper/           # စာရင်းအင်းမော်ဒယ်များ
│   ├── finrl/                         # အားဖြည့်သင်ယူမှု
│   └── vnpy_wrapper/                  # VN.PY အရောင်းအဝယ်
│
├── agents/                            # AI အေးဂျင့်များ
│   ├── GeopoliticsAgents/             # ပထဝီနိုင်ငံရေးဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာမှု
│   ├── finagent_core/                 # အဓိက အေးဂျင့်မူဘောင်
│   └── ...
│
├── agno_trading/                      # အရောင်းအဝယ်အေးဂျင့်များ
├── ai_quant_lab/                      # AI/ML ပိုင်းခြားစိတ်ဖြာမှု
├── strategies/                        # အရောင်းအဝယ်ဗျူဟာများ
├── technicals/                        # နည်းပညာဆိုင်ရာ ပိုင်းခြားစိတ်ဖြာမှု
│
├── yfinance_data.py                   # Yahoo Finance
├── fred_data.py                       # Federal Reserve
├── imf_data.py                        # IMF ဒေတာ
├── worldbank_data.py                  # ကမ္ဘာ့ဘဏ်
├── oecd_data.py                       # OECD ဒေတာ
├── ecb_data.py                        # ဥရောပဗဟိုဘဏ်
├── bis_data.py                        # နိုင်ငံတကာအခြေချမှုများအတွက် ဘဏ်
├── nasdaq_data.py                     # NASDAQ ဒေတာ
├── sec_data.py                        # SEC ဖိုင်များ
├── edgar_tools.py                     # EDGAR ဒေတာဘေ့စ်
├── fmp_data.py                        # Financial Modeling Prep
├── databento_provider.py              # Databento စျေးကွက်ဒေတာ
│
├── akshare_*.py                       # တရုတ်စျေးကွက် script ၂၀+
├── *_gov_api.py                       # အစိုးရဒေတာ API များ
└── ...                                # ဒေတာရယူသူ ၄၀+ ထပ်ရှိသည်
```

---

## C++ နှင့် ပေါင်းစည်းခြင်း

Python script များကို `python_runner.cpp` မှတဆင့် C++ မှ ခေါ်ဆိုသည်:

```
C++ Screen (ဥပမာ၊ research_screen.cpp)
    ↓ ဒေတာဝန်ဆောင်မှုကို ခေါ်သည်
C++ Data Service (research_data.cpp)
    ↓ PythonRunner::run() ကို ခေါ်သည်
python_runner.cpp
    ↓ စတင်သည်: python script.py <args>
Python Script
    ↓ stdout သို့ JSON ကို ထုတ်သည်
C++ က JSON ကို ပြန်ဆိုသည်
    ↓ ဒေတာကို screen သို့ ပြန်ပေးသည်
Screen က ရလဒ်ကို ပြန်ဆိုသည်
```

### Script လုပ်ဆောင်ခြင်း

```bash
# Script များကို အောက်ပါအတိုင်း ခေါ်ဆိုသည်:
python Analytics/portfolioManagement/optimize.py '{"symbols":["AAPL","MSFT"]}'

# မျှော်မှန်းရလဒ်:
{"success": true, "data": {"weights": [0.6, 0.4], "sharpe": 1.23}}
```

---

## Script စံနှုန်းများ

### Input/Output ပုံစံ

Script အားလုံးသည် ဆက်သွယ်ရန်အတွက် JSON ကိုသုံးသည်:

```python
import sys
import json

def main():
    if len(sys.argv) < 2:
        print(json.dumps({"success": False, "error": "Usage: script.py <command>"}))
        sys.exit(1)

    command = sys.argv[1]

    try:
        result = process_command(command)
        print(json.dumps({"success": True, "data": result}))
    except Exception as e:
        print(json.dumps({"success": False, "error": str(e)}))
        sys.exit(1)

if __name__ == "__main__":
    main()
```

### Type Hints

အမြဲတမ်း type hints ကိုသုံးပါ:

```python
from typing import List, Dict, Any, Optional

def calculate_returns(prices: List[float], period: int = 1) -> Dict[str, Any]:
    """စျေးနှုန်းစီးရီးမှ အမြတ်များကို တွက်ချက်သည်။"""
    ...
```

### Error Handling

```python
def safe_fetch(symbol: str) -> Dict[str, Any]:
    try:
        if not symbol:
            return {"success": False, "error": "Symbol required"}

        data = fetch_data(symbol)
        return {"success": True, "data": data}

    except ValueError as e:
        return {"success": False, "error": f"Invalid input: {e}"}
    except Exception as e:
        return {"success": False, "error": f"Unexpected error: {e}"}
```

---

## စမ်းသပ်ခြင်း

### Manual စမ်းသပ်ခြင်း

```bash
cd fincept-cpp/scripts

# ဒေတာရယူသူကို စမ်းသပ်ပါ
python yfinance_data.py quote AAPL

# ပိုင်းခြားစိတ်ဖြာမှု module ကို စမ်းသပ်ပါ
python Analytics/quantstats_analytics.py metrics '{"returns":[0.01,0.02,-0.01]}'
```

---

## အဓိက Library များ

| Library | ရည်ရွယ်ချက် |
|---------|---------|
| `pandas` | ဒေတာ ကိုင်တွယ်ခြင်း |
| `numpy` | ကိန်းဂဏန်းဆိုင်ရာ တွက်ချက်ခြင်း |
| `scipy` | သိပ္ပံဆိုင်ရာ တွက်ချက်ခြင်း |
| `yfinance` | Yahoo Finance API |
| `akshare` | တရုတ်စျေးကွက်ဒေတာ |
| `pyportfolioopt` | အစုစု ပိုမိုကောင်းမွန်အောင်ပြုလုပ်ခြင်း |
| `quantstats` | စွမ်းဆောင်ရည် ပိုင်းခြားစိတ်ဖြာမှု |
| `ta-lib` / `talipp` | နည်းပညာဆိုင်ရာ အညွှန်းကိန်းများ |
| `statsmodels` | စာရင်းအင်းမော်ဒယ်များ |
| `scikit-learn` | စက်သင်ယူမှု |
| `langchain` | LLM ပေါင်းစည်းမှု |

---

**မေးခွန်းများ** [GitHub](https://github.com/Fincept-Corporation/FinceptTerminal) တွင် issue ဖွင့်ပါ။
