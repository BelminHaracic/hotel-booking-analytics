#  Hotel Booking Data Warehouse & BI Dashboard

> Business Intelligence project: Analysis of hotel bookings using a
> MySQL Data Warehouse and Power BI dashboard

------------------------------------------------------------------------

##  About the Project

This project analyzes **119,390 hotel bookings** from the period
**2015--2017**.\
The goal was to design a **Data Warehouse** using a **Star Schema**
model and visualize key business metrics through an interactive **Power
BI dashboard**.

**Dataset**: Hotel Booking Demand (Kaggle)

------------------------------------------------------------------------

##  Technologies Used

-   **Database**: MySQL\
-   **ETL Tool**: KNIME Analytics Platform\
-   **Visualization**: Power BI Desktop\
-   **SQL IDE**: DataGrip

------------------------------------------------------------------------

##  Project Architecture

The project consists of three main components:

CSV Data → MySQL Database → KNIME ETL → Power BI Dashboard

------------------------------------------------------------------------

##  MySQL Database (hotel_dwh)

Implemented **Star Schema** with: - **1 Fact table**: `fact_bookings`
(118,675 rows) - **4 Dimension tables**: - `dim_date` (1,096 rows) -
`dim_hotel` (45 rows) - `dim_customer` (448 rows) - `dim_room` (231
rows)

------------------------------------------------------------------------

##  KNIME ETL Pipeline

The KNIME workflow: - Extracts data from the source database -
Transforms data (cleaning, deduplication) - Loads data into dimension
tables

------------------------------------------------------------------------

##  Power BI Dashboard

<img width="1283" height="724" alt="Hotel_Booking_Dashboard" src="https://github.com/user-attachments/assets/c8fe77a8-9000-4a5e-b9f3-52f49abc4d7e" />


------------------------------------------------------------------------

##  Star Schema Model
<img width="1077" height="711" alt="star shema" src="https://github.com/user-attachments/assets/6ccf897b-8bd1-4244-b67d-d1574569da6a" />


------------------------------------------------------------------------

## Fact Table: fact_bookings

Contains **measures** per booking: - `revenue` -- Total revenue - `adr`
-- Average Daily Rate - `total_nights` -- Number of nights -
`total_guests` -- Number of guests - `is_canceled` -- Cancellation flag

------------------------------------------------------------------------

## Dimension Tables

**dim_date** - year, quarter, month, month_name, is_weekend

**dim_hotel** - hotel_name, market_segment, distribution_channel

**dim_customer** - country, customer_type, is_repeated_guest

**dim_room** - reserved_room_type, assigned_room_type, meal,
deposit_type

------------------------------------------------------------------------

## Power BI Dashboard -- Visualizations

### KPI Cards

-   **Total Revenue**: €42.72M\
-   **Total Bookings**: 118,675\
-   **Average Daily Rate**: €102.44\
-   **Cancellation Rate**: 37%

### Revenue by Country

-   Portugal is #1 with €27.7M\
-   Top 3: Portugal, UK, France

### Revenue by Customer Type

-   84% Transient
-   8% Transient-Party
-   7% Contract
-   1% Group

### Market Segment Performance

-   Online TA: €23.9M (55%)
-   Offline TA/TO: €8.2M
-   Direct: €5.1M

### Room Nights Sold

-   Strong seasonality
-   Peak in August

### Monthly Revenue & Booking Trend

-   August 2017 is the best month
-   Winter months perform the worst

------------------------------------------------------------------------

## Key Findings & Insights

  Metric              Value     Insight
  ------------------- --------- ---------------------------
  Total Revenue       €42.72M   Solid 3-year performance
  Avg Daily Rate      €102.44   Mid-range pricing
  Cancellation Rate   37%       Very high -- needs action
  Peak Month          August    Strong seasonality

------------------------------------------------------------------------

##  Business Recommendations

1.  Reduce cancellation rate\
2.  Decrease dependency on OTA platforms\
3.  Address seasonality with promotions\
4.  Increase corporate and group bookings

------------------------------------------------------------------------

##  Repository Structure

hotel-booking-dwh/ - database/ - knime/ - powerbi/ - README.md

------------------------------------------------------------------------


##  Data Source

Hotel Booking Demand dataset by Jesse Mostipak (Kaggle)

------------------------------------------------------------------------

