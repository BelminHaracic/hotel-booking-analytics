#  Hotel Booking Data Warehouse & BI Dashboard

> Business Intelligence project: Hotel booking analysis using MySQL Data Warehouse and Power BI dashboard

---

##  About the Project

This project analyzes **119,390 hotel bookings** from the period 2015-2017. The goal was to create a **Data Warehouse** with a Star Schema model and visualize key business metrics through an interactive Power BI dashboard.

**Dataset**: [Hotel Booking Demand (Kaggle)](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)

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

<img width="1100" height="534" alt="Hotel_BI_ETL" src="https://github.com/user-attachments/assets/b4999551-5b65-49e8-a41e-8eadc71861b3" />


##  Star Schema Model
```
<img width="1077" height="711" alt="star shema" src="https://github.com/user-attachments/assets/41444097-903e-44aa-8655-866f4d873da0" />


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

<img width="1283" height="724" alt="Hotel_Booking_Dashboard" src="https://github.com/user-attachments/assets/3f00c42c-73c8-4ffd-8fa8-147cf313b1a7" />


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

<img width="1100" height="534" alt="Hotel_BI_ETL" src="https://github.com/user-attachments/assets/006c480d-11df-4203-a078-1e7b78ec4fcd" />


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


### Top 10 Countries by Revenue

<img width="812" height="400" alt="image" src="https://github.com/user-attachments/assets/33de3bdd-7d78-4ad6-844c-4960678bc2a3" />

```
1. Portugal (PRT)    - €27.7M
2. UK (GBR)          - €14.4M
3. France (FRA)      - €11.1M
4. Spain (ESP)       - €9.8M
5. Germany (DEU)     - €6.5M
...


### Monthly Trend

<img width="1077" height="574" alt="image" src="https://github.com/user-attachments/assets/d7635998-6b30-42e6-8113-a58e1be17e34" />


**Seasonality is clear:**
- Peak: Summer (Jul, Aug) - €3-4M monthly
- Drop: Winter (Jan, Feb, Dec) - €0.5-1M monthly

---

##  Data Validation

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

<img width="1356" height="136" alt="image" src="https://github.com/user-attachments/assets/b6e4f4e6-788b-4a38-8126-fe29f948c3d3" />


**Result**: All NULL counts are 0 (clean data!)


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
- **DBMS**: MySQL
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
