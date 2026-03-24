# DeFi Protocol Safety Scoring Tool

I created this project as I wanted a dead-simple way to size up major DeFi protocols without needing a PhD in Solidity or a paid scanners.
It’s a lightweight Python notebook that pulls live-ish data, runs some basic stats and produces a clean safety/risk report PDF with numbers, a heatmap and charts for Aave, Uniswap, and Compound.

Built it to run straight in Google Colab so anyone can do it in two clicks. No heavy blockchain tools, local installations or ML black boxes, it only uses public APIs + simple math.
It feels way more transparent than most “risk dashboards” out there.


## What it looks like with March 2026 data

Screenshots:
![Safety Heatmap](screenshots/report-heatmap-page2.png)
![TVL Trend - Aave](screenshots/report-tvl-aave-page3.png)

PDF:
[DeFi_Safety_Report.pdf](https://github.com/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool/blob/main/sample_report/DeFi_Safety_Report.pdf)

## What the project does 

## Features

The code:
- Grabs live numbers from CoinGecko (token metrics), DefiLlama (TVL + chains), GitHub (commit activity)
- Includes a fallback cache so it doesn’t crash if an API suddenly doesn't work
- Sums everything into one 0–10 composite safety score that mixes financial health, security history and operational risks
- Drops a proper PDF with detailed summary tables, a coloured risk heatmap and a TVL chart (with a simple 7 day forecast) for each protocol
- Added detailed comments where required and the weights are easily customizable for faster tweaking in the future.


## Example report summary

| Protocol | Total Score | Risk Level  | Financial | Security | Operational | TVL    |
| AAVE     | 7.93/10     | LOW RISK    |  8.02     | 8.26     | 7.50        | $25.4B |
| UNISWAP  | 5.43/10     | MEDIUM RISK |  0.90     | 8.13     | 7.25        | $3.2B  |
| COMPOUND | 1.81/10     | HIGH RISK   | -3.39     | 3.06     | 5.75        | $1.4B  |

Aave is still king with fresh 2026 audits and zero core exploits. 
Uniswap holds its own on security but loses points due to governance concentration. 
Compound can be seen as struggling due to an old exploit, stale audits and basically zero dev activity lately.

Financial note: The price-momentum piece moves with the market, so every run should give different numbers.


## How it works

### Financial score components
The TVL size, which is capped for balance. 
The most recent 7-days for price momentum and 30-days for the price swing (raw volatility, not direction).
Also finds a 30-day TVL trend via simple linear regression.

### Security score components
It starts at 10 and subtracts points for known exploits. 
Also adds bonuses for recent audits and active bug bounties. 
Old audits or persisting vulnerabilities get a point penalty.

### Operational score components
- Emergency pause: −0.5 if the protocol has no pause function. Even if immutability is a security property it is riskier to not have a pause ability.
- Oracle risk: A single source oracle failure can enable price manipulation. Rated for for none / medium / high.
- Bridge exposure increases vulnerability to other chains' threats, so −0.5 if it’s cross-chain heavy 
- Chain concentration: a big penalty if one chain holds >70% of TVL as there is a liquidity and operational risk
- Development activity: −0.5 if <20 commits in 90 days, −1.5 if <5. A $1B+ protocol that’s almost totally silent looks like a maintenance red flag.

(Kept the thresholds discrete so that the outputs are rounder numbers instead of many decimals.)


## How to run it

### Google Colab, simple and fast
1. Open the notebook:  
   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool/blob/main/DeFi-Protocol-Safety-Scoring-Tool.ipynb)
2. Hit “Run all” to run all cells. Installs requirements and should execute everything seemlessly.
3. The last cell will generate the DeFi_Safety_Report.pdf and auto-download it, also available in the files on the left. Normally done in less than 30–90 seconds.


### Local version (optional)

```bash
git clone https://github.com/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool.git
cd DeFi-Protocol-Safety-Scoring-Tool
pip install -r requirements.txt
# open the .ipynb (Jupyter/VS Code)
```


## Limitations

This is not financial/investment advice. It is a fast tool to explore a side of these protocols using public APIs (and hardcoded summaries as fallbacks).
It does no on-chain code scanning (kept it light for Colab). Relies on public exploit reports, audit timelines and bounty pages.
APIs on CoinGecko, DefiLlama, GitHub can rate-limit or fail. In such a case it will fall back to hardcoded data and will say so in the report.
It does not normally detect new issues and vulnerabilities unless updated often. 
The trend line is a simple linear forecast to give a rough idea, not meticulous predictive modeling.
It can be used as a good starting point for independent research, but shouldn't be the sole decision-maker.


## API rate limits and how to avoid them

The three APIs used have free tier rate limits that can cause fallback data to be used, especially in Colab where IP addresses are shared.
As mentioned, the PDF will note it if there's fallbacks used. The financial score is the most sensitive to this as it's the most dynamic.
- CoinGecko gives about 30 requests per minute. If it gets limitted in a run, waiting 2-3 minutes before retrying should work.
- DefiLlama: If TVL falls back, simply re-run the cell. It usually resolves on retry.
- Github apparently is at 60 requests per hour. We only do 9 calls total (3 per protocol) so you’re usually fine.


## Exact data sources

- Prices and metrics from CoinGecko API
- TVL and chain breakdown from DefiLlama API
- Bug bounties are from Immunefi for Aave and Compound and from Cantina for Uniswap
- Exploits and audits from Rekt.news, protocol docs, GitHub
- Code freshness is from GitHub last commit API


By GeorgeKGM2058. Built it throughout 2025, gave it a nice update early 2026.