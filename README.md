# Ticket Price Compare - 全网票价比价

[![Skill Type](https://img.shields.io/badge/Type-AI%20Skill-blue)]()
[![Python](https://img.shields.io/badge/Python-3.8%2B-green)]()
[![License](https://img.shields.io/badge/License-MIT-yellow)]()

> 航班和火车票全网实时比价 Skill，支持国内/国际航线，12306 实时余票查询，无需 API Key 即可使用核心功能。

## 功能亮点

- **零配置使用** — 携程爬取 + 12306 实时查询，无需注册任何 API
- **火车票实时余票** — 直接调用 12306 公开接口，返回真实车次、余票、票价
- **机票多源比价** — 携程爬取 / Tequila / Amadeus 多数据源
- **国内 + 国际航线** — 同时支持中文城市名（北京→广州）和英文（Shanghai→Tokyo）
- **平台搜索链接** — 一键直达携程、去哪儿、飞猪、同程、Skyscanner、Google Flights 等
- **航空公司官网** — 覆盖 10 家国内航司 + 13 家国际航司
- **微信小程序入口** — 提供小程序搜索提示，方便手机端查询
- **优惠条件提醒** — 自动列出学生票、儿童票、退改签规则等

## 数据源

| 数据源 | 类型 | 需要 API Key | 说明 |
|--------|------|:----------:|------|
| 12306 | 公开 API | 否 | 火车票实时余票与票价 |
| 携程爬取 | Web Scraping | 否 | 机票价格（JS 渲染页面，可能无数据） |
| Tequila | REST API | 可选 | Kiwi.com 航班数据（注册已关闭） |
| Amadeus | REST API | 可选 | 全球航班数据（注册已关闭） |

## 安装

### 1. 核心（无依赖）

脚本仅使用 Python 标准库，无需安装额外包：

```bash
# 无需 pip install，直接运行
python scripts/ticket_search.py 北京 广州 2026-05-01 train
```

### 2. 可选依赖

如果你想使用 Amadeus API：

```bash
pip install amadeus>=12.0.0
```

## 使用方法

### 火车票查询

```bash
python scripts/ticket_search.py 北京 广州 2026-04-20 train
```

### 机票查询

```bash
python scripts/ticket_search.py 北京 广州 2026-04-20 flight
```

### 同时查询机票+火车票

```bash
python scripts/ticket_search.py 北京 广州 2026-04-20 all
```

### 国际航线

```bash
python scripts/ticket_search.py 上海 东京 2026-06-15 flight
python scripts/ticket_search.py Shanghai Tokyo 2026-06-15 all
```

## 环境变量

| 变量名 | 必需 | 说明 |
|--------|:----:|------|
| `TEQUILA_API_KEY` | 否 | Kiwi.com Tequila API 密钥 |
| `AMADEUS_CLIENT_ID` | 否 | Amadeus API Client ID |
| `AMADEUS_CLIENT_SECRET` | 否 | Amadeus API Client Secret |

## 输出示例

### 火车票输出

```
=== 12306 实时查询结果 ===
出发: 北京  到达: 广州  日期: 2026-04-20

车次    出发→到达           时间          历时     二等座   一等座
G303    北京西→广州南       10:00-17:17   7:17     ¥1033   ¥1488
D923    北京西→广州南       20:22-06:47   10:25    ¥709    -
Z13     北京丰台→广州白云   14:25-12:36+1 22:11    -       ¥251(硬卧)
```

### 机票输出

```
=== 机票查询结果 ===
出发: 北京  到达: 广州  日期: 2026-04-20

航班         出发→到达      时间          价格
CA1301       PEK→CAN       07:30-10:45   ¥820
CZ3104       PKX→CAN       08:00-11:10   ¥750

平台搜索链接:
- 携程旅行: https://flights.ctrip.com/...
- 去哪儿旅行: https://flight.qunar.com/...
- Skyscanner: https://www.skyscanner.net/...
```

## 项目结构

```
ticket-price-compare/
├── SKILL.md                        # Skill 定义文件（触发条件、工作流）
├── README.md                       # 本文件
└── scripts/
    ├── ticket_search.py            # 核心搜索脚本
    └── requirements.txt            # 依赖声明（核心零依赖）
```

## 技术细节

- **核心零依赖** — `ticket_search.py` 仅使用 Python 标准库（`urllib`, `json`, `ssl`）
- **12306 接口** — 使用 `leftTicket/queryZ` 公开端点，无需登录
- **携程爬取** — 尝试直接请求航班搜索页面，若 JS 渲染则退回提供链接
- **城市名映射** — 内置 200+ 中国城市/车站名到 IATA/电报码的映射
- **SSL 安全** — 仅对 12306 端点禁用证书验证（其证书链有已知问题），其余连接均使用完整 TLS 验证
- **编码兼容** — 自动处理 Windows 控制台 UTF-8 编码问题

## 作为 AI Skill 使用

本 Skill 可用于 CodeBuddy / ClawHub 等 AI 技能平台。当用户提问涉及以下场景时自动触发：

- "帮我查北京到广州的机票"
- "4月20日上海飞东京多少钱"
- "火车票余票查询"
- "哪个平台机票最便宜"
- "学生票有什么优惠"

## License

MIT
