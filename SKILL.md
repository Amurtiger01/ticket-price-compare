---
name: ticket-price-compare
description: This skill should be used when the user wants to compare and search for flight or train ticket prices across multiple platforms. It supports both domestic (China) and international routes, fetches real-time train availability via 12306, generates direct search links for all major booking platforms and airline official websites, provides WeChat mini program quick links for mobile search, and highlights discount conditions. For flight prices, it attempts web scraping first (no API key needed), then falls back to Tequila/Amadeus API for existing key holders. Trigger scenarios include: searching for cheap flights, comparing train ticket prices, finding international flight deals, looking for the best ticket booking platform, or asking about ticket discount conditions.
---

# Ticket Price Compare - 全网票价比价

## Overview

This skill enables real-time comparison of flight and train ticket prices. It fetches **real-time train availability** via 12306 (no API key needed), and attempts to scrape **flight prices from 携程** (no API key needed). It also generates direct search links for all major booking platforms and **WeChat mini program quick links** for convenient mobile search. Tequila/Amadeus APIs are available as optional fallbacks for users who already have keys. Discount conditions and restrictions are clearly listed separately.

## Data Sources

### 携程 Web Scraping (Primary, No API Key Needed)
- Scrapes public Ctrip flight search pages for price data
- **No registration or API key required** — works out of the box
- Note: Ctrip renders pages via JavaScript, so scraping may not always return prices. In that case, platform links are provided for manual search.

### 12306 (Train Tickets, No API Key Needed)
- Uses 12306 public endpoint for real-time train availability
- Supports major Chinese cities with station code auto-mapping

### Optional APIs (For Existing Key Holders Only)
- **Kiwi.com Tequila API**: If `TEQUILA_API_KEY` is set (registration may no longer be available)
- **Amadeus API**: If `AMADEUS_CLIENT_ID` + `AMADEUS_CLIENT_SECRET` are set (registration is closed)
- These are used as fallbacks only when web scraping returns no results

## Core Capabilities

### 1. Flight Ticket Search

```bash
python scripts/ticket_search.py "<departure>" "<arrival>" "<date>" flight
```

- Domestic: `python scripts/ticket_search.py "北京" "上海" "2026-05-01" flight`
- International: `python scripts/ticket_search.py "上海" "东京" "2026-06-15" flight`

**Data sources (in order of priority)**:
1. 携程 Web Scraping (no API key, may return prices or flight info)
2. Tequila API (if API key configured)
3. Amadeus API (if API keys configured)

**Covered domestic platforms**: 携程旅行, 去哪儿旅行, 飞猪旅行, 同程旅行, 途牛旅游

**Covered international platforms**: Skyscanner天巡, Google Flights, Kayak客涯, Momondo, Expedia, Booking.com

**Covered airline official sites**: 10 Chinese + 13 international airlines

### 2. Train Ticket Search with Real-Time Availability

```bash
python scripts/ticket_search.py "<departure>" "<arrival>" "<date>" train
```

**Real-time data from 12306**: Returns actual train schedules with:
- Train code & type (高铁/动车/特快/快速)
- Departure/arrival stations & times
- Duration
- Available seat types & counts (商务座/一等座/二等座/硬卧/硬座 etc.)

### 3. Combined Search (Flight + Train)

```bash
python scripts/ticket_search.py "<departure>" "<arrival>" "<date>" all
```

Train results are automatically excluded for international routes.

## Output Sections

The script generates structured output with these sections (in order):

1. **Route Summary** — Departure, arrival, date, route type
2. **Data Source Status** — Whether scraping/APIs returned live data
3. **Real-Time Flight Prices** — Table of flight offers with prices (if available)
4. **Transfer Details** — Multi-segment flight details (if any transfers)
5. **Flight Discount Conditions** — Refund/change rules, baggage limits, cabin restrictions
6. **Real-Time Train Info** — Table of actual trains with seat availability (if domestic)
7. **Train Discount Conditions** — Student tickets, child tickets, change rules
8. **Platform Links** — Direct search URLs for all booking platforms
9. **Airline Official Sites** — Direct links to airline websites
10. **WeChat Mini Program Quick Links** — Mobile H5 links + WeChat mini program search tips for 携程/飞猪/同程/去哪儿 and major airlines
11. **Search Tips** — Route-specific advice

### Discount Conditions

Always include the dedicated "优惠条件/限制条件" section. Reference `references/platforms_guide.md` for detailed per-platform discount conditions. Load this file when:
- User asks about specific discount conditions
- Presenting results that include discounted fares
- User asks which platform has the best deals for their situation

## Workflow

1. **Collect query parameters**: Get departure city, arrival city, and travel date. If date not specified, ask. Default ticket type to "all".

2. **Execute search**: Run `scripts/ticket_search.py` with the parameters.

3. **Present results**: Show the complete output including:
   - Real-time prices (if available from scraping or API)
   - All platform links for comparison
   - Discount conditions section
   - Search tips

4. **If no flight prices returned**: Inform the user that real-time flight prices could not be fetched automatically, and recommend clicking the platform links to compare prices manually. Mention that 12306 train data is always available for domestic routes.

## Date Flexibility

If a user asks for "cheapest dates" or "price trends":
- Run the script with multiple date parameters to compare
- For domestic flights: Also suggest 携程/去哪儿 price calendar features
- For international flights: Suggest Skyscanner "cheapest month" or Google Flights date grid

## Important Notes

- **Primary method**: Web scraping (携程) — no API key needed, but may not always work due to JavaScript rendering
- **Fallback APIs**: Tequila/Amadeus — only for users who already have keys; registration is closed for new users
- **Without any flight data**: Platform search links are always provided (users click to see prices)
- **12306 train data** is always real-time (no API key needed)
- **SSL verification**: Only disabled for 12306 endpoints (known certificate chain issues); all other connections use full TLS verification
- Prices vary in real-time; recommend checking 2-3 platforms for confirmation
- Airline official websites sometimes offer exclusive prices not available on OTA platforms
- Always remind users about potential discount conditions before booking
