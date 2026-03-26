# DeFi Protocol Safety Scoring Tool

I created this project to compare the DeFi protocols in a nice way while explaining some theory in the notebook. 
It pulls live data, does calculations and produces a safety/risk report PDF with the numbers, 
a heatmap and charts for Aave, Uniswap and Compound. 
It runs easily in Google Colab so anyone can do it in two clicks. 
No heavy blockchain tools, local installations or ML packages with mysterious contents, 
it only uses public APIs and simple math for transparency and speed.


## What it looks like with March 2026 data
![Safety Heatmap](screenshots/report-heatmap-page2.png)
![TVL Trend - Aave](screenshots/report-tvl-aave-page3.png)
[DeFi_Safety_Report.pdf](https://github.com/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool/blob/main/sample_report/DeFi_Safety_Report.pdf)


## More details on what happens

The code first grabs live token metrics from CoinGecko, TVL and chains from DefiLlama and the recent commit activity from GitHub. 
It also includes a fallbacks so it doesn’t crash if an API suddenly doesn't work. 
It then sums everything into one 0–10 composite safety score that mixes financial health, security history and operational risks. 
Finally it produces a PDF with detailed summary, a coloured risk heatmap and a TVL chart for each protocol which includes a simple 7 day forecast.
I also added detailed comments where required and the weights are easily customizable for faster tweaking.

### Financial score
This takes into consideration the TVL size, which is capped for balance, 
the most recent 7-days for price momentum and 30-days for the price swing (the raw volatility, not its direction). 
It also finds a 30-day TVL trend with simple linear regression. 
The price moves with the market, so every run should give different numbers.

### Security score
It starts at 10 (perfect safety) and normally subtracts points for any known exploits, 
old audits, recent development absence or other vulnerabilities. 
On the contrary it will add points for recent audits and active bug bounties. 

### Operational score
A lack of an emergency pause function gets a −0.5, as even if immutability is a security property, not have a pause ability is risky. 
An oracle failure can enable price manipulation. Such case is rate with a none/medium/high for a point penalty. 
If it’s cross-chain heavy case it gets −0.5 points, as bridge exposure increases vulnerability to other chains' threats too. 
If one chain holds more than 70% of TVL, there is a liquidity and operational risk, so it also gets a suitable penalty. 
The development activity on the protocol is a big deal, so it gets −0.5 if there are less than 20 commits in the last 90 days and
−1.5 if less than 5. Maintenance is always required, especially as more money is involved.


## How to run it

### Google Colab, simple and fast
- Open the notebook:  
   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool/blob/main/DeFi-Protocol-Safety-Scoring-Tool.ipynb)
- Click “Run all” to run all cells. It should execute everything seemlessly. 
The last cell will generate the report pdf and auto-download it, also available in the files on the left. 
Normally done in less than 30–90 seconds.


### The local run version, optional
```bash
git clone https://github.com/GeorgeKGM2058/DeFi-Protocol-Safety-Scoring-Tool.git
cd DeFi-Protocol-Safety-Scoring-Tool
pip install -r requirements.txt
# open the .ipynb (Jupyter/VS Code)
```


## Limitations

This is not financial/investment advice. 
It is a fast tool to explore a side of these protocols using public APIs (and hardcoded summaries as fallbacks).
It does no onchain code scanning as I kept it light for Colab. 
It relies on public exploit reports, audit timelines and bounty pages. 
APIs on CoinGecko, DefiLlama, GitHub can rate limit or block. 
In such a case it will fall back to hardcoded data and will say so in the report. 
It does not normally detect new issues and vulnerabilities unless updated often or upgraded. 
Also, the trend line is a simple linear forecast to give a rough idea, not super accurate predictive modeling. 
I'd use it as a good starting point for independent research, but it shouldn't be the sole decision-maker.


## API rate limits and how to avoid them

As mentioned, the three APIs used have free tier rate limits that can cause fallback data to be used, 
especially in Colab where IP addresses are shared.
The financial score is the most sensitive to this as it's the most dynamic (distance between live data and fallbacks). 
- CoinGecko gives about 30 requests per minute. If it gets limitted in a run, waiting 2-3 minutes before retrying can work.
- DefiLlama: If TVL falls back to the hardcoded values just re-run the cell, but do not spam. It usually resolves on retry.
- Github apparently is at 60 requests per hour. A single run does 9 calls total (3 per protocol) so it's usually fine.


## Exact data sources

The prices and metrics come from CoinGecko API, TVL and chain breakdown from DefiLlama API. 
The bug bounties are split coming from Immunefi for Aave and Compound and from Cantina for Uniswap. 
Exploits and audits accounted for come from Rekt.news, protocol docs and GitHub. 
The code freshness is also from GitHub, using a last commit API.


By GeorgeKGM2058. Built it throughout 2025, updated early 2026 with more features and realism.