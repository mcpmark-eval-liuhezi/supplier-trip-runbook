# Beijing–Shanghai Supplier Trip Runbook

Operational runbook for the Beijing South → Shanghai Hongqiao supplier-check trip.
Written so a colleague can cover the shop while the owner is travelling.

---

## 1. Trip Dates (resolved via the 12306 rail booking system clock)

All dates below were resolved against the rail booking system's own clock (Shanghai time, UTC+8) — this is the clock that ticket availability is keyed to, and it is the only clock considered authoritative for this trip.

| Item | Resolved Date |
|---|---|
| Day the trip was planned | 2026-10-24 (per 12306 clock) |
| **Outbound date ("tomorrow")** | **2026-10-25** |
| **Return date ("day after tomorrow")** | **2026-10-26** |

## 2. Station Codes (official, for the courier routing sheet)

| Station | Chinese | Official Station Code |
|---|---|---|
| Beijing South | 北京南 | **VNP** |
| Shanghai Hongqiao | 上海虹桥 | **AOH** |

## 3. Chosen Trains

### 3.1 Outbound — G531 (Sunday, 2026-10-25)

- **Route:** 北京南 (VNP) → 上海虹桥 (AOH)
- **Departure:** 06:08 from 北京南 (earliest morning G-train departure of the day)
- **Arrival:** 12:04 at 上海虹桥
- **Duration:** 05:56
- Availability at time of booking: Business 5 tickets, First class available, Second class 1 ticket remaining, Standing available.

**Full stop list for G531 (2026-10-25):**

| # | Station | Arrival | Departure |
|---|---|---|---|
| 1 | 北京南 | — | 06:08 |
| 2 | 天津南 | 06:39 | 06:41 |
| 3 | 德州东 | 07:21 | 07:23 |
| 4 | 泰安 | 07:57 | 07:59 |
| 5 | 曲阜东 | 08:18 | 08:20 |
| 6 | 徐州东 | 08:55 | 08:57 |
| 7 | 宿州东 | 09:15 | 09:30 |
| 8 | 蚌埠南 | 09:52 | 09:54 |
| 9 | 滁州 | 10:21 | 10:23 |
| 10 | 南京南 | 10:42 | 10:45 |
| 11 | 无锡东 | 11:27 | 11:29 |
| 12 | 昆山南 | 11:46 | 11:48 |
| 13 | 上海虹桥 | 12:04 | 12:04 |

### 3.2 Return — G32 (Monday, 2026-10-26)

- **Route:** 上海虹桥 (AOH) → 北京南 (VNP)
- **Departure:** 19:25 from 上海虹桥 (latest G-train departure after 17:00 that day)
- **Arrival:** 23:49 at 北京南
- **Duration:** 04:24
- Availability at time of booking: Business 5 tickets, First class available, Second class available, Standing available.

**Full stop list for G32 (2026-10-26):**

| # | Station | Arrival | Departure |
|---|---|---|---|
| 1 | 上海虹桥 | — | 19:25 |
| 2 | 南京南 | 20:24 | 20:27 |
| 3 | 济南西 | 22:24 | 22:26 |
| 4 | 北京南 | 23:49 | 23:49 |

### 3.3 Stations Involved in the Trip (for partner matching)

- End stations (always count): **北京南**, **上海虹桥**
- Intermediate stops of the two chosen trains: 天津南, 德州东, 泰安, 曲阜东, 徐州东, 宿州东, 蚌埠南, 滁州, 南京南, 无锡东, 昆山南 (G531); 南京南, 济南西 (G32).

## 4. Pickup Partners on the Route & WooCommerce Shipping Zones

Partner data source: `partner_pickups.csv` in the sandbox working directory.
A partner is "on the route" if its station is one of the two end stations, or an intermediate stop where at least one of the two chosen trains actually stops.

| Station | Partner | Contact | Shipping Zone Created (exact name) | Zone ID | Flat-Rate Method |
|---|---|---|---|---|---|
| 北京南 | 京华快运 | 138-0100-1111 | Beijing South Station Pickup | 1 | Flat rate (instance 1, enabled) |
| 上海虹桥 | 沪上驿站 | 021-6400-2222 | Shanghai Hongqiao Station Pickup | 2 | Flat rate (instance 2, enabled) |
| 天津南 | 津门货运 | 022-8700-8888 | Tianjin South Station Pickup | 3 | Flat rate (instance 3, enabled) |
| 济南西 | 泉城取件 | 0531-8600-4444 | Jinan West Station Pickup | 4 | Flat rate (instance 4, enabled) |
| 徐州东 | 彭城货运 | 0516-8700-5555 | Xuzhou East Station Pickup | 5 | Flat rate (instance 5, enabled) |
| 南京南 | 金陵速递 | 025-5200-3333 | Nanjing South Station Pickup | 6 | Flat rate (instance 6, enabled) |

Before creating these, the store's existing shipping zones were checked (only the system default "Locations not covered by your other zones" zone existed), so no zone was duplicated. Each new zone has the standard flat-rate shipping method enabled — a zone without a shipping method is dead weight.

**Partners NOT on the route (no zone created):**

| Station | Partner | Contact | Reason |
|---|---|---|---|
| 苏州北 | 姑苏驿站 | 0512-6300-6666 | Not served by either chosen train (G531 stops at 昆山南/无锡东 but not 苏州北; G32 does not stop there either) |
| 杭州东 | 西湖取件 | 0571-8700-7777 | Not on the Beijing–Shanghai corridor of either chosen train |

## 5. Data File Integrity (ops rule: every cited data file gets its checksum logged)

| File | SHA-256 Checksum |
|---|---|
| `partner_pickups.csv` | `aa37f02d92128f71e541fa08b3b5b600b50a7dc93107d6484b551b9adfdc6b14` |

Before relying on the partner data, re-run `sha256sum partner_pickups.csv` and confirm it matches the value above. If it does not, the file has changed since this runbook was written — re-check the partner list and zones before acting.

## 6. Coverage Notes for the Colleague

1. **While I'm away:** the shop runs as usual. The six station pickup zones above are live in WooCommerce, each with flat-rate shipping enabled.
2. **Order questions:** route station pickup orders to the partner whose zone matches the customer's destination station; contacts are in the table in section 4.
3. **Capacity:** partner daily capacities from `partner_pickups.csv` — 京华快运 40, 沪上驿站 35, 津门货运 12, 泉城取件 20, 彭城货运 15, 金陵速递 25 (items/day).
4. **If a train changes:** re-resolve dates against the 12306 system clock (Shanghai time) — do not trust any other clock for ticket availability.
5. **If partner data changes:** verify the SHA-256 checksum first; if it differs, treat the file as updated and re-run the route matching before creating any new zone (always check existing zones first to avoid duplicates).
