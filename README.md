# FX Risk Desk — Forex Money Management Calculator

A professional, single-file forex money management web app built for serious traders. Calculate accurate lot sizes, risk exposure, and potential P&L across forex pairs, metals, indices, energy, and crypto — all from one clean interface.

> Built to be deployed on Vercel, Netlify, GitHub Pages, or any static host in seconds.

---

## Screenshot

> *(Add a screenshot of the app here)*

---

## Features

### Risk Calculator
- Select any instrument and input your entry, stop loss, take profit, account balance and risk %
- Outputs **exact lot size** and **broker-rounded lot** (floored to 0.01 increments)
- Shows actual dollar risk after rounding, potential profit, SL/TP pip/point distance
- Visual **Risk/Reward ratio bar** with 1:X ratio display

### Profit Target Calculator
- Input a desired profit target and maximum loss you're willing to accept
- Returns the conservative lot size that satisfies both constraints
- Identifies the **limiting factor** (profit target vs max loss cap)
- Full R:R breakdown with % of balance at risk

### Live Rates Engine
- Fetches real-time institutional FX rates from the **[Frankfurter API](https://api.frankfurter.dev)** — free, no API key required
- Used to compute accurate pip values for **non-USD cross pairs** (e.g. EURGBP, GBPAUD, AUDCAD)
- Refreshes every **5 minutes** automatically
- Live rate status badge shows `● LIVE RATES`, `● RATES Xm ago`, or `● ESTIMATED RATES` if API is unavailable
- Graceful fallback to hardcoded estimates if the API is down — app never breaks

---

## Instruments Covered

| Category | Examples | Count |
|----------|----------|-------|
| Forex Majors | EURUSD, GBPUSD, USDJPY, AUDUSD | 7 |
| JPY Crosses | EURJPY, GBPJPY, CADJPY, ZARJPY | 13 |
| EUR Crosses | EURGBP, EURAUD, EURNZD, EURZAR | 16 |
| GBP Crosses | GBPAUD, GBPNZD, GBPCHF, GBPSGD | 11 |
| AUD / NZD Crosses | AUDNZD, AUDCAD, NZDCHF, NZDSGD | 9 |
| USD Exotics | USDTRY, USDZAR, USDNGN, USDMXN | 19 |
| Scandi / EM Crosses | NOKSEK, CADCHF, SGDHKD | 6 |
| Metals | XAUUSD, XAGUSD, XPTUSD, XPDUSD, XCUUSD | 5 |
| Indices | US30, NAS100, US500, UK100, GER40, JP225 | 12 |
| Energy | USOIL (WTI), UKOIL (Brent), NGAS | 3 |
| Crypto CFDs | BTC, ETH, XRP, SOL, ADA, DOGE, PEPE + 30 more | 37 |

**Total: 140+ instruments**

---

## Pip Value Accuracy

The app uses category-specific formulas — the same logic professional trading desks use:

| Pair Type | Formula | Rate Source |
|-----------|---------|-------------|
| USD-quoted (EURUSD, XAUUSD) | `pip × contract` | Exact — always |
| USD-base (USDJPY, USDCHF, USDCAD) | `(pip × contract) ÷ entry price` | User's entry price |
| JPY crosses (EURJPY, GBPJPY, CADJPY) | `(pip × contract) ÷ entry price` | User's entry price |
| Non-USD crosses (EURGBP, GBPAUD, AUDCAD) | `pip × contract × live quote/USD rate` | **Frankfurter live API** |
| Metals (XAUUSD, XAGUSD) | Fixed contract spec (100 oz / 5000 oz) | Broker standard |
| Indices / Energy / Crypto | Fixed per broker contract spec | Broker standard |

Pip values for cross pairs update in real-time — a shift in GBP/USD from 1.27 → 1.31 is reflected immediately in EURGBP, GBPAUD, GBPCHF pip calculations.

---

## Calculation Logic

### Core Formula
```
Lot Size = Risk Amount ($) / (SL Distance in Pips × Pip Value per Lot)
```

### Lot Size Displayed
- **Exact Lot** — the mathematically precise result
- **Broker Lot** — floored to `0.01` (the minimum increment most brokers accept)
- Actual risk recalculated from the broker-rounded lot so you know the true exposure

### Risk Amount
```
Risk Amount = Account Balance × (Risk % / 100)
```

### Floating Point Protection
All pip distance calculations use a `roundPips()` helper that rounds to 8 decimal places, eliminating IEEE 754 floating point drift (e.g. `1.0850 - 1.0800` in raw JS = `0.004999999...` not `0.005`).

---

## Validation & Guards

The app enforces the following before calculating:

- SL must be on the correct side of entry for the selected direction (Buy/Sell)
- TP must be on the correct side of entry (when provided)
- Risk % is capped at 100%; a warning fires above 10%
- Max loss cannot exceed account balance (Profit Target mode)
- Zero and negative prices/balances are rejected with clear error messages
- Lot size display auto-scales precision so values like `0.003` never show as `0.00`

---

## Tech Stack

| Layer | Detail |
|-------|--------|
| Frontend | Vanilla HTML, CSS, JavaScript — zero dependencies |
| Fonts | Google Fonts (Bebas Neue, DM Mono, DM Sans) |
| Rates API | [Frankfurter.dev](https://api.frankfurter.dev) — free, no key, ECB-sourced |
| Deployment | Static file — works on any host |

No build step. No framework. No bundler. One `.html` file.

---

## Deployment

### Vercel (recommended)

1. Push `forex-money-manager.html` to a GitHub repo
2. Go to [vercel.com](https://vercel.com) → New Project → Import your repo
3. Set the output file as the root — Vercel will detect it automatically
4. Deploy — done

### GitHub Pages

1. Push the file to a repo, rename it `index.html`
2. Go to **Settings → Pages → Source → Deploy from branch**
3. Select `main` / `root` → Save
4. Your app is live at `https://yourusername.github.io/repo-name`

### Netlify / Any Static Host

Drag and drop `forex-money-manager.html` onto [netlify.com/drop](https://app.netlify.com/drop) — live in 10 seconds.

---

## Project Structure

```
/
└── forex-money-manager.html   # The entire app — self-contained
└── README.md
```

---

## Disclaimer

This tool is for **informational and risk management purposes only**. It does not constitute financial advice. Pip values for non-USD cross pairs and indices rely on approximate exchange rates that may differ from your broker's exact contract specifications. Always verify lot sizes and pip values against your broker's platform (MT4/MT5 symbol specifications) before executing a trade.

Forex, CFD, and crypto trading involves significant risk of loss. Trade responsibly.

---

## License

MIT — free to use, modify, and deploy.

---

## Contributing

Pull requests welcome. If you spot a pip value discrepancy for a specific broker's instrument spec, open an issue with the broker name, symbol, and correct contract size.

---

*Built with obsessive attention to calculation accuracy — because in trading, the math has to be right.*
