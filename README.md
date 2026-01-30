#  Hotel Booking Data Warehouse & BI Dashboard

> Business Intelligence project: Hotel booking analysis using MySQL Data Warehouse and Power BI dashboard

---

##  About the Project

This project analyzes **119,390 hotel bookings** from the period 2015-2017. The goal was to create a **Data Warehouse** with a Star Schema model and visualize key business metrics through an interactive Power BI dashboard.

**Dataset**: [Hotel Booking Demand (Kaggle)](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)

---

##  Technologies Used

- **Database**: MySQL 8.0
- **ETL Tool**: KNIME Analytics Platform
- **Visualization**: Power BI Desktop
- **SQL IDE**: DataGrip

---

##  Project Architecture

The project consists of three main components:
```
CSV Data → MySQL Database → KNIME ETL → Power BI Dashboard
```

###  **MySQL Database** (hotel_dwh)

Implemented **Star Schema** with:
- **1 Fact table**: `fact_bookings` (118,675 rows)
- **4 Dimension tables**: 
  - `dim_date` (1,096 rows)
  - `dim_hotel` (45 rows)
  - `dim_customer` (448 rows)
  - `dim_room` (231 rows)

###  **KNIME ETL Pipeline**

KNIME workflow processes data:
- Extracts from source database
- Transforms (cleans, deduplicates)
- Loads into dimension tables

![KNIME Workflow](knime/knime_workflow.png)

###  **Power BI Dashboard**

Interactive dashboard with key metrics and visualizations.

![Power BI Dashboard](powerbi/dashboard_screenshot.png)

---

##  Star Schema Model
```
<img width="1077" height="711" alt="star shema" src="https://github.com/user-attachments/assets/143c6b3b-0b80-4e62-a44e-66ff7825808b" />

```

### Fact Table: fact_bookings

Contains **metrics** (measures) for each booking:
- `revenue` - Total revenue
- `adr` - Average daily rate
- `total_nights` - Number of nights
- `total_guests` - Number of guests
- `is_canceled` - Whether canceled

### Dimension Tables

**dim_date** - Calendar dimension
- year, quarter, month, month_name, is_weekend

**dim_hotel** - Hotel and sales channels
- hotel_name, market_segment, distribution_channel

**dim_customer** - Customer segmentation
- country, customer_type, is_repeated_guest

**dim_room** - Room types
- reserved_room_type, assigned_room_type, meal, deposit_type

---

##  Power BI Dashboard - Visualizations

![Dashboard](powerbi/dashboard_screenshot.png)

The dashboard consists of the following visuals:

### 1. **KPI Cards** (Top)

Four key metrics:
- **Total Revenue**: €42.72M - Total revenue
- **Total Bookings**: 118,675 - Total number of bookings
- **Avg Daily Rate**: €102.44 - Average price per night
- **Cancellation Rate**: 37% - Cancellation rate

### 2. **Revenue by Country** (Map - Top Left)

Geographic distribution of revenue. Bubble size represents revenue amount.

**Findings**: 
- Portugal is #1 with €27.7M
- European countries dominate
- Top 3: Portugal, UK, France

### 3. **Revenue by Customer Type** (Donut Chart - Top Right)

Customer structure by type.

**Findings**:
- 84% Transient (individual tourists)
- 8% Transient-Party
- 7% Contract (contracted guests)
- 1% Group (groups)

### 4. **Market Segment Performance** (Table/Bar Chart - Right)

Revenue by sales channels.

**Findings**:
- Online TA (Booking.com, Expedia): €23.9M (55%)
- Offline TA/TO: €8.2M
- Direct (direct bookings): €5.1M
- **Problem**: High dependency on OTA platforms

### 5. **Room Nights Sold** (Line Chart - Bottom Left)

Trend of room sales by month.

**Findings**:
- Pronounced **seasonality** - summer vs winter
- City Hotel performs better than Resort Hotel
- Peak in August

### 6. **Monthly Revenue & Booking Trend** (Combo Chart - Bottom Right)

Monthly revenue (bars) and number of bookings (line) over the years.

**Findings**:
- August 2017 is the record month
- Winter months (Jan, Feb, Dec) are the weakest
- Revenue and number of bookings correlate (go together)

---

##  KNIME ETL Workflow

![KNIME Workflow](knime/knime_workflow.png)

### Process

KNIME workflow performs the following transformations:

#### dim_hotel workflow:
```
MySQL Source → DB Table Selector → Column Filter → 
Duplicate Row Filter → Column Rename → DB Writer
```

**Result**: 45 unique combinations (hotel + market_segment + distribution_channel)

#### dim_customer workflow:
```
MySQL Source → DB Table Selector → Column Filter → 
Duplicate Row Filter → DB Writer
```

**Result**: 448 unique combinations (country + customer_type + is_repeated_guest)

#### dim_room workflow:
```
MySQL Source → DB Table Selector → Column Filter → 
Duplicate Row Filter → DB Writer
```

**Result**: 231 unique combinations (room types + meal + deposit)

---

##  Key Findings and Insights

### Business Metrics

| Metric | Value | Insight |
|---------|----------|---------|
| Total Revenue | €42.72M | Solid earnings for 3 years |
| Avg Daily Rate | €102.44 | Mid-range price segment |
| Cancellation Rate | **37%** | **PROBLEM** - above average (20-30%) |
| Peak Month | August | 3-4x more revenue than winter |

### Top 10 Countries by Revenue

![Query Results](database/top_countries_screenshot.png)
```
1. Portugal (PRT)    - €27.7M
2. UK (GBR)          - €14.4M
3. France (FRA)      - €11.1M
4. Spain (ESP)       - €9.8M
5. Germany (DEU)     - €6.5M
...
```

### Distribution by Market Segments

![Query Results](database/market_segments_screenshot.png)
```
Online TA         - €23.9M (55%)
Offline TA/TO     - €8.2M  (19%)
Direct            - €5.1M  (12%)
Groups            - €4.7M  (11%)
Corporate         - €0.8M  (2%)
```

### Monthly Trend

![Query Results](database/monthly_trend_screenshot.png)

**Seasonality is clear:**
- Peak: Summer (Jul, Aug) - €3-4M monthly
- Drop: Winter (Jan, Feb, Dec) - €0.5-1M monthly

---

## 🔍 Data Validation

### NULL Foreign Keys Check
```sql
SELECT 
    COUNT(*) as total_records,
    SUM(CASE WHEN date_id IS NULL THEN 1 ELSE 0 END) as null_date_id,
    SUM(CASE WHEN hotel_id IS NULL THEN 1 ELSE 0 END) as null_hotel_id,
    SUM(CASE WHEN customer_id IS NULL THEN 1 ELSE 0 END) as null_customer_id,
    SUM(CASE WHEN room_id IS NULL THEN 1 ELSE 0 END) as null_room_id
FROM fact_bookings;
```

![Validation Results](database/validation_screenshot.png)

**Result**: All NULL counts are 0 ✅ (clean data!)

### Cancellation Rate Analysis
```sql
SELECT 
    is_canceled,
    COUNT(*) as count,
    ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM fact_bookings), 2) as percentage
FROM fact_bookings
GROUP BY is_canceled;
```

**Result**:
- Not Canceled (0): 74,733 (63%)
- Canceled (1): 43,942 (37%)

---

##  Business Recommendations

Based on the analysis, I recommend:

1. **Reduce cancellation rate (37%)**
   - Implement stricter cancellation policies
   - Require deposit for reservations
   - Offer early-bird discounts for non-refundable bookings

2. **Reduce dependency on OTA platforms**
   - Increase direct bookings (lower commission costs)
   - Invest in marketing and SEO
   - Loyalty program for direct guests

3. **Address seasonality problem**
   - Winter promotions and packages
   - Target business guests during off-season
   - Event hosting (conferences, seminars)

4. **Increase corporate and group bookings**
   - Currently only 2% of revenue from corporate clients
   - Potential for growth in B2B segment

---

##  Repository Contents
```
hotel-booking-dwh/
│
├── database/
│   ├── hotel_bookings.csv               # Source data   
│
├── knime/
│   ├── Hotel_ETL.knwf                   # KNIME workflow file
│   └── knime_workflow.png               # Workflow screenshot
│
├── powerbi/
│   ├── Hotel_Booking_Dashboard.pbix     # Power BI file
│   └── dashboard_screenshot.png         # Dashboard screenshot
│
└── README.md                             # This file
```

---



##  Technical Details

### Database
- **DBMS**: MySQL 8.0
- **Schemas**: 3 (hotel_source, hotel_staging, hotel_dwh)
- **Tables**: 5 in DWH (4 dim + 1 fact)
- **Rows**: ~120K in fact table

### ETL
- **Tool**: KNIME Analytics Platform
- **Node count**: ~20 nodes
- **Execution time**: ~2 minutes

### Dashboard
- **Tool**: Power BI Desktop
- **Visuals**: 10+ (KPI cards, map, charts, slicers)
- **Interactivity**: Cross-filtering enabled

---


##  Data Source

Dataset: [Hotel Booking Demand](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand) by Jesse Mostipak (Kaggle)

The original dataset contains 119,390 booking records from two Portuguese hotels (Resort Hotel and City Hotel) from the period 2015-2017.

---
