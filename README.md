# robinhood-readonly-mcp

A read-only MCP server for Robinhood portfolio research. Wraps [robin_stocks](https://github.com/jmfernandes/robin_stocks) to give AI assistants access to your portfolio data for analysis.

> **⚠️ Research Tool Only** - This server provides read-only access. No trading functionality is exposed.

> **⚠️ Unofficial API** - Uses robin_stocks unofficial API. May break without notice. Use at your own risk.

## Source Project

This project is based on [verygoodplugins/robinhood-mcp](https://github.com/verygoodplugins/robinhood-mcp).

## What Can You Do With This?

Once connected, you can have natural conversations with Claude about your portfolio:

### Portfolio Health Check

> "Give me a health check on my portfolio. What's my total value, sector concentration, and any positions that are significantly up or down?"

Claude will pull your positions, calculate sector exposure, identify your best and worst performers, and flag any concentration risks.

### Research Before Buying

> "I'm thinking about adding to my NVDA position. Show me the fundamentals, recent news, analyst ratings, and how it's performed over the past year."

Get comprehensive research combining price history, P/E ratios, earnings dates, and analyst sentiment in one response.

### Compare Investments

> "Compare the cruise lines in my portfolio - show me CCL, RCL, and NCLH side by side with their P/E ratios, market caps, and year-to-date performance."

Quickly evaluate similar holdings to identify relative value.

### Dividend Analysis

> "What dividends have I received this year? Which of my holdings pay dividends and what are their yields?"

Track your passive income and identify dividend opportunities in your portfolio.

### Risk Assessment

> "What's my exposure to the energy sector? How concentrated am I in my top 5 holdings?"

Analyze sector concentration and identify positions that might be overweight.

### Earnings Calendar

> "Which of my holdings have earnings coming up in the next two weeks?"

Stay ahead of earnings volatility with a personalized calendar.

### Performance Attribution

> "Break down my portfolio returns. What's driving my gains and losses?"

Understand which positions are contributing most to your performance.

### Watchlist Research

> "Pull quotes and fundamentals for everything in my watchlist. Which ones look interesting right now?"

Bulk research stocks you're tracking.

## Installation

Check out the source and run the server from that checkout:

```bash
git clone <your-github-repository-url> robinhood-readonly-mcp
cd robinhood-readonly-mcp

python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e .
```

Start the MCP server from the source tree:

```bash
python -m robinhood_mcp.server
```

The server uses stdio transport and waits for an MCP client to connect. It is
not intended to be opened as a normal web server or visited in a browser.

## Configuration

### VS Code

Add the following server configuration to `~/Library/Application Support/Code/User/mcp.json`:

```json
{
  "servers": {
    "robinhood": {
      "command": "/absolute/path/to/robinhood-mcp/.venv/bin/python",
      "args": ["-m", "robinhood_mcp.server"],
      "env": {
        "ROBINHOOD_USERNAME": "user name",
        "ROBINHOOD_PASSWORD": "password"
      }
    }
  }
}
```

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "robinhood": {
      "command": "/absolute/path/to/robinhood-readonly-mcp/.venv/bin/python",
      "args": ["-m", "robinhood_mcp.server"],
      "cwd": "/absolute/path/to/robinhood-readonly-mcp",
      "env": {
        "ROBINHOOD_USERNAME": "your_email",
        "ROBINHOOD_PASSWORD": "your_password"
      }
    }
  }
}
```

### Claude Code

```bash
claude mcp add robinhood -- /absolute/path/to/robinhood-readonly-mcp/.venv/bin/python -m robinhood_mcp.server
```

## Available Tools

| Tool                              | Description                                          |
| --------------------------------- | ---------------------------------------------------- |
| `robinhood_get_portfolio`         | Portfolio value, equity, buying power, day change    |
| `robinhood_get_positions`         | All holdings with cost basis, current value, P&L     |
| `robinhood_get_position`          | One holding by ticker with quantity, value, and P&L  |
| `robinhood_get_watchlist`         | Stocks in your watchlists                            |
| `robinhood_get_quote`             | Real-time price, bid/ask, volume                     |
| `robinhood_get_fundamentals`      | P/E ratio, market cap, dividend yield, 52-week range |
| `robinhood_get_historicals`       | OHLCV price history (day/week/month/year)            |
| `robinhood_get_news`              | Recent news articles for a symbol                    |
| `robinhood_get_earnings`          | Earnings dates, EPS estimates, actuals               |
| `robinhood_get_ratings`           | Analyst buy/hold/sell ratings                        |
| `robinhood_get_dividends`         | Dividend payment history                             |
| `robinhood_get_options_positions` | Current options positions                            |
| `robinhood_get_available_expirations` | Available option expiration dates              |
| `robinhood_get_option_chain`      | Read-only option chain data for an expiration       |
| `robinhood_get_options_by_expiration_and_strike` | Options at an expiration and strike |
| `robinhood_search_symbols`        | Search stocks by name or ticker                      |

## Example Conversations

**Simple queries:**

- "What's my portfolio worth right now?"
- "Show me my top 5 holdings by value"
- "Do I already own HIMS, and what's my current position?"
- "Get me a quote for AAPL"

For single-symbol portfolio questions, prefer `robinhood_get_position` over
`robinhood_get_positions`. The single-symbol tool avoids rebuilding every
holding and is much faster for questions like "Should I add more HIMS?"

**Analysis requests:**

- "Compare the fundamentals of GOOGL vs META"
- "Which of my stocks are trading below their 52-week average?"
- "Show me the price chart for TSLA over the past year"
- "What's my best performing stock? What's my worst?"

**Research workflows:**

- "I want to understand the cruise line industry. Pull data on CCL, RCL, and NCLH - compare their fundamentals and recent performance."
- "Find stocks in my portfolio with a P/E under 15 and positive earnings growth"
- "I'm down big on a few positions. Show me the fundamentals and news for my worst performers to help me decide if I should hold or cut losses."

## Limitations

- **Read-only**: Cannot place trades, modify watchlists, or change account settings
- **Unofficial API**: Robinhood may change their API at any time, breaking functionality
- **No real-time streaming**: Quotes are point-in-time, not live feeds
- **Session expiry**: You may need to re-authenticate periodically
- **Rate limits**: Heavy usage may trigger Robinhood's rate limiting

## Security Notes

- Credentials are only used locally to authenticate with Robinhood
- Session tokens are cached in `~/.tokens/robinhood.pickle` by robin_stocks
- Never commit your `.env` file or expose credentials
- This tool cannot execute trades - it's read-only by design

## Development

```bash
cd robinhood-readonly-mcp
python -m pip install -e ".[dev]"

# Lint
ruff check . && ruff format --check .

# Test
pytest

# Run locally
python -m robinhood_mcp.server
```

## Troubleshooting

**"Not logged in" errors:**

- Verify your username and password are correct
- Try logging in through the Robinhood app to ensure your account isn't locked

**Rate limiting:**

- robin_stocks doesn't have built-in rate limiting
- If you hit rate limits, wait a few minutes before retrying

## License

MIT

This project includes code originally released under the MIT License. The
original copyright and permission notice are preserved in `LICENSE`.

## Disclaimer

This tool is for educational and research purposes only. It uses unofficial APIs that may break at any time. The authors are not responsible for any account restrictions, data inaccuracies, or financial losses.

**This project is not affiliated with, endorsed by, or connected to Robinhood Markets, Inc.**

## Automation Examples

### Daily Portfolio Review with Claude Code

Set up a cron job to get a daily portfolio briefing:

```bash
# ~/.claude/commands/portfolio-review.md
---
description: "Daily portfolio health check"
---
Using the robinhood MCP tools:
1. Get my current portfolio value and day change
2. Identify my top 3 gainers and top 3 losers today
3. Flag any positions that are down more than 20% from cost basis
4. Check if any holdings have earnings in the next 7 days
5. Give me a 2-3 sentence summary I can read with my morning coffee
```

Run it daily:

```bash
# Add to crontab -e
0 7 * * 1-5 cd ~/Projects && claude -p "/portfolio-review" --dangerously-skip-permissions >> ~/portfolio-reports/$(date +\%Y-\%m-\%d).md
```

### Weekly Research Digest

```bash
# ~/.claude/commands/weekly-research.md
---
description: "Weekly deep dive on portfolio"
---
For each of my top 10 holdings by value:
1. Pull current fundamentals and compare to sector averages
2. Get recent news and analyst rating changes
3. Flag any significant changes from last week
4. Identify 2-3 stocks from my watchlist that might be worth adding

Format as a markdown report I can review on the weekend.
```

Powered by [robin_stocks](https://github.com/jmfernandes/robin_stocks) and [FastMCP](https://github.com/jlowin/fastmcp).
