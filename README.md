# AIR Bandenservice MCP server

<img src="logo.png" alt="AIR Bandenservice" width="96" align="right">

A remote [MCP](https://modelcontextprotocol.io) server for **[AIR Bandenservice](https://www.airbandenservice.com/nl)**, a tyre shop and workshop in Haarlem, the Netherlands. It lets an AI assistant look up tyres by size, price an order line by line, book fitting at the workshop and place the order for a customer. The customer always pays on a Mollie payment page, so nothing is ordered until they pay.

- **Endpoint:** `https://www.airbandenservice.com/mcp`
- **Transport:** Streamable HTTP (stateless, JSON responses)
- **Authentication:** none
- **Protocol versions:** 2026-07-28, 2025-11-25, 2025-06-18, 2025-03-26

## Connect

**Claude** (claude.ai, Desktop, mobile): Settings → Connectors → *Add custom connector* → name `AIR Bandenservice`, URL `https://www.airbandenservice.com/mcp`.

**ChatGPT**: Settings → Apps & Connectors → Advanced settings → turn on *Developer mode* → *Create* → URL `https://www.airbandenservice.com/mcp`, authentication *No authentication*.

**Claude Code**

```bash
claude mcp add --transport http air-bandenservice https://www.airbandenservice.com/mcp
```

**Cursor, VS Code, Windsurf and other clients** (`mcp.json`):

```json
{
  "mcpServers": {
    "air-bandenservice": { "type": "http", "url": "https://www.airbandenservice.com/mcp" }
  }
}
```

## Tools

| Tool | What it does | Side effects |
| --- | --- | --- |
| `search_tyres` | Tyres by size (e.g. `205/55R16`), season and brand, with the all-in price per tyre and availability | none |
| `get_shop_info` | Address, opening hours, phone, delivery fee, fitting price, payment methods, terms and privacy links | none |
| `list_fitting_days` | Workshop days with room for fitting in the next 4 weeks | none |
| `get_quote` | The exact total for an order, line by line: tyres, fitting, sensor surcharges, disposal fee, delivery | none |
| `place_order` | Creates the order and returns a Mollie `checkoutUrl` for the customer | creates an unpaid order |
| `get_order_status` | Payment and order status by order reference (no personal data) | none |

## How ordering works

1. `search_tyres` for the size on the tyre sidewall.
2. Optionally `list_fitting_days` to have the tyres fitted and balanced at the workshop.
3. `get_quote` returns every line and the total. Prices are in euros and include 21% VAT and, where it applies, the €1.70 tyre disposal fee.
4. The assistant shows the customer the lines, the total and the [terms](https://www.airbandenservice.com/nl/algemene-voorwaarden). Only after the customer approves and accepts the terms themselves does the assistant call `place_order`, passing the quoted `expectedTotalCents`.
5. The customer opens the returned `checkoutUrl` and pays with iDEAL, card or another Mollie method. The order is placed only after payment. The customer then gets a confirmation and a receipt by email, and the shop is notified.

An unpaid order expires after about 15 minutes and its tyres go back on sale.

### Limits

- Online payment only: there is no pay-on-pickup through an assistant.
- At most 4 products and 8 tyres per order.
- An order is refused if the price changed since the quote. The assistant quotes again and asks the customer again.
- A limited number of unpaid assistant orders can be open at once, per customer and shop-wide.
- Delivery addresses in the Netherlands only. Discount codes work only on the website.

## Example prompts

- "I need four summer tyres 205/55R16 from AIR Bandenservice in Haarlem. What are my options?"
- "What does it cost to have two 195/65R15 tyres fitted at AIR Bandenservice next week?"
- "Order the cheapest 225/45R17 all-season tyres for pickup on Saturday morning, with fitting."
- "Is my AIR Bandenservice order AIR-2026-… paid yet?"

## Privacy and terms

The server stores only what an order needs: the customer's name, email and, where given, phone, address, licence plate and company details. Search and quote calls store nothing personal. See the [privacy policy](https://www.airbandenservice.com/nl/privacybeleid) and the [terms and conditions](https://www.airbandenservice.com/nl/algemene-voorwaarden). Dutch is the binding language of both.

## Support

AIR Bandenservice, Jan Tademaweg 16, 2031 CV Haarlem, the Netherlands. Email [info@airbandenservice.com](mailto:info@airbandenservice.com), phone +31 23 785 37 31.

This repository holds documentation only; the server runs on the shop's website.
