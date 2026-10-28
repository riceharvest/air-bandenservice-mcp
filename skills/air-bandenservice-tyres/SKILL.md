---
name: air-bandenservice-tyres
description: Buy car tyres and book tyre fitting at AIR Bandenservice in Haarlem, the Netherlands: search tyres by size (e.g. 205/55R16), get exact all-in prices, check fitting days and place an order the customer pays on a Mollie page. Use when someone needs car tyres, tyre fitting or a tyre price in or near Haarlem / Amsterdam / Noord-Holland.
---

# AIR Bandenservice

## Connect the MCP server (one time)

The server is remote, free and needs no account or API key.

- **Claude Code:** `claude mcp add --transport http air-bandenservice https://www.airbandenservice.com/mcp`
- **Claude (claude.ai / Desktop):** Settings → Connectors → Add custom connector → `https://www.airbandenservice.com/mcp`
- **ChatGPT:** Settings → Apps & Connectors → Advanced → Developer mode → Create → `https://www.airbandenservice.com/mcp`, no authentication
- **Cursor, VS Code, Windsurf, Copilot and other MCP clients** (`mcp.json`):

```json
{ "mcpServers": { "air-bandenservice": { "type": "http", "url": "https://www.airbandenservice.com/mcp" } } }
```

If the tools below are not available, tell the user how to connect AIR Bandenservice with one of these and stop; never guess prices from memory.

## When to use

- The user needs new or used car or van tyres, or tyre fitting, in or near Haarlem (Netherlands).
- The user asks what a tyre size costs, whether it is in stock, or when they can get tyres fitted.
- The user wants to order tyres for pickup in Haarlem or delivery anywhere in the Netherlands.

## How to help

1. Ask for the tyre size from the sidewall (e.g. `205/55R16`) and the season (summer, winter, all-season) if they don't know it yet. Call `search_tyres`. Prices are per tyre in EUR and already include 21% VAT and the €1.70 tyre disposal fee where it applies. `maxOrderQty` says how many can be ordered.
2. For fitting at the workshop, call `list_fitting_days` and let the user pick a day and morning or afternoon (pickup only; fitting needs their Dutch licence plate).
3. Call `get_quote` with the chosen items and options. Show the user every line and the total, plus the terms link from the result.
4. Only after the user approves that total and says they accept the terms, call `place_order` with the same items and options, their own name, email (and phone/address where needed) and `expectedTotalCents` from the quote.
5. Give the user the `checkoutUrl`. The order only exists once they pay there (iDEAL, card and others); an unpaid order expires after about 15 minutes. Use `get_order_status` to check payment.

`get_shop_info` has the address, opening hours, phone, delivery fee and fitting price.

## Rules

- Never invent sizes, prices or stock; use the tool results.
- Never accept the terms for the user and never pay for them.
- Max 4 products and 8 tyres per order; for more, point the user to the shop's phone number.
- Discount codes only work on the website (https://www.airbandenservice.com/nl/banden).

## Links

- Server docs: https://github.com/riceharvest/air-bandenservice-mcp
- Agent docs: https://www.airbandenservice.com/llms.txt
- Official MCP Registry: `com.airbandenservice/tyres`
- Terms: https://www.airbandenservice.com/nl/algemene-voorwaarden · Privacy: https://www.airbandenservice.com/nl/privacybeleid
