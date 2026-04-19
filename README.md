# Ticket Price Compare - Multi-Platform Ticket Price Comparison

[![Skill Type](https://img.shields.io/badge/Type-AI%20Skill-blue)]()
[![Python](https://img.shields.io/badge/Python-3.8%2B-green)]()
[![License](https://img.shields.io/badge/License-MIT-yellow)]()

> Real-time multi-platform flight and train ticket price comparison Skill. Supports domestic/international routes, 12306 real-time seat availability, no API key required for core features.

## Highlights

- **Zero Configuration** - Ctrip scraping + 12306 real-time queries, no API registration needed
- **Real-Time Train Seat Availability** - Direct 12306 public endpoint, returns actual schedules, seats, and fares
- **Multi-Source Flight Comparison** - Ctrip scraping / Tequila / Amadeus multiple data sources
- **Domestic + International Routes** - Supports both Chinese and English city names
- **Platform Search Links** - One-click access to Ctrip, Qunar, Fliggy, Tongcheng, Skyscanner, Google Flights, etc.
- **Airline Official Sites** - Covers 10 Chinese + 13 international airlines
- **WeChat Mini Program Links** - Mini program search tips for convenient mobile search
- **Discount Condition Alerts** - Auto-lists student tickets, child tickets, refund/change rules

## Data Sources

| Data Source | Type | API Key Required | Description |
|-------------|------|:--------------:|-------------|
| 12306 | Public API | No | Real-time train seat availability and fares |
| Ctrip Scraping | Web Scraping | No | Flight prices (JS-rendered pages, may return no data) |
| Tequila | REST API | Optional | Kiwi.com flight data (registration closed) |
| Amadeus | REST API | Optional | Global flight data (registration closed) |

## Installation

### 1. Core (No Dependencies)

The script uses only Python standard library, no extra packages needed:

```bash
# No pip install needed, just run
python scripts/ticket_search.py Beijing Guangzhou 2026-05-01 train
```

### 2. Optional Dependencies

If you want to use the Amadeus API:

```bash
pip install amadeus>=12.0.0
```

## Usage

### Train Ticket Search

```bash
python scripts/ticket_search.py Beijing Guangzhou 2026-04-20 train
```

### Flight Ticket Search

```bash
python scripts/ticket_search.py Beijing Guangzhou 2026-04-20 flight
```

### Combined Search (Flight + Train)

```bash
python scripts/ticket_search.py Beijing Guangzhou 2026-04-20 all
```

### International Routes

```bash
python scripts/ticket_search.py Shanghai Tokyo 2026-06-15 flight
python scripts/ticket_search.py Shanghai Tokyo 2026-06-15 all
```

## Environment Variables

| Variable | Required | Description |
|----------|:--------:|-------------|
| `TEQUILA_API_KEY` | No | Kiwi.com Tequila API key |
| `AMADEUS_CLIENT_ID` | No | Amadeus API Client ID |
| `AMADEUS_CLIENT_SECRET` | No | Amadeus API Client Secret |

## Output Examples

### Train Ticket Output

```
=== 12306 Real-Time Query Results ===
Departure: Beijing  Arrival: Guangzhou  Date: 2026-04-20

Train    Departure->Arrival       Time          Duration  Second Class  First Class
G303     Beijing Xi->Guangzhou South  10:00-17:17   7:17     CNY 1033     CNY 1488
D923     Beijing Xi->Guangzhou South  20:22-06:47   10:25    CNY 709      -
Z13      Beijing Fengtai->Guangzhou Baiyun  14:25-12:36+1  22:11   -   CNY 251 (Hard Sleeper)
```

### Flight Ticket Output

```
=== Flight Search Results ===
Departure: Beijing  Arrival: Guangzhou  Date: 2026-04-20

Flight       Departure->Arrival  Time          Price
CA1301       PEK->CAN            07:30-10:45   CNY 820
CZ3104       PKX->CAN            08:00-11:10   CNY 750

Platform Search Links:
- Ctrip: https://flights.ctrip.com/...
- Qunar: https://flight.qunar.com/...
- Skyscanner: https://www.skyscanner.net/...
```

## Project Structure

```
ticket-price-compare/
+-- SKILL.md                        # Skill definition (triggers, workflow)
+-- README.md                       # This file
+-- scripts/
    +-- ticket_search.py            # Core search script
    +-- requirements.txt            # Dependencies (core has zero dependencies)
```

## Technical Details

- **Core Zero Dependencies** - `ticket_search.py` uses only Python standard library (`urllib`, `json`, `ssl`)
- **12306 Endpoint** - Uses `leftTicket/queryZ` public endpoint, no login required
- **Ctrip Scraping** - Attempts direct request to flight search page; falls back to links if JS rendering blocks
- **City Name Mapping** - Built-in 200+ Chinese city/station name to IATA/telegraph code mapping
- **SSL Security** - Only 12306 endpoint has certificate verification disabled (known cert chain issues); all other connections use full TLS verification
- **Encoding Compatibility** - Auto-handles Windows console UTF-8 encoding issues

## As an AI Skill

This Skill can be used on AI skill platforms such as CodeBuddy / ClawHub. It auto-triggers when users ask:

- "Find me flights from Beijing to Guangzhou"
- "How much is Shanghai to Tokyo on April 20"
- "Check train ticket seat availability"
- "Which platform has the cheapest flights"
- "What student ticket discounts are available"

## License

MIT
