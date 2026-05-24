# -Marvy-Logistics-Operations-Dashboard
A 4-page Power BI analytics dashboard built on 3 years of trucking operations data — transforming 14 raw CSV tables into actionable management intelligence.

📌 Project Overview
This capstone project analyses 3 years of logistics and fleet operations data (January 2022 – December 2024) for a trucking company. The dashboard provides management and stakeholders with a single source of truth across revenue performance, operational efficiency, driver and fleet metrics, and safety intelligence.
ToolMicrosoft Power BI DesktopData PeriodJan 2022 – Dec 2024Source Tables14 CSV filesTotal Records620,000+ rows across all tablesRevenue Analysed$298.6 millionDashboard Pages4 pagesAuthorAghe Marvellous — Data Analyst

🎯 Business Problem
The business had accumulated 3 years of operational data but no single reporting tool to consolidate or visualise it. Key challenges identified:

Zero revenue growth — $99.9M in 2022, $99.8M in 2024 despite consistent load volumes
44.6% on-time delivery rate — more than half of all deliveries arriving late
Fuel consuming 32% of every dollar earned ($95.6M over 3 years)
18.5% revenue capture rate — only capturing $99M/year against $537M in customer potential
Safety incidents rising — 60 incidents in 2024 vs 54 in 2023 (+11.1%)


❓ Business Questions Answered
#QuestionDashboard Page1Which customer type generates the highest revenue?Executive Overview2Which load type contributes the most revenue?Executive Overview3How does revenue trend over time across months and years?Executive Overview4Which booking type generates the most revenue?Executive Overview5Which routes are the most profitable in terms of revenue per mile?Executive + Operations6How are drivers contributing to overall revenue performance?Executive + Driver/Fleet7How does fuel cost impact overall revenue and profitability?Operational Efficiency8Which state has the highest fuel cost and where to focus refuelling?Operational Efficiency9How does fuel cost trend over time?Operational Efficiency10Which drivers generate the highest revenue and on-time delivery rate?Driver & Fleet11Is there a relationship between truck make and maintenance cost?Driver & Fleet12Which vehicles require the most frequent maintenance?Driver & Fleet13Which maintenance types are the most costly?Driver & Fleet14Which routes or states are most prone to safety incidents?Safety Performance15Which drivers are associated with the highest number of incidents?Safety Performance16Which incident types result in the highest costs?Safety Performance17What percentage of incidents are at-fault or preventable?Safety Performance18Are safety incidents increasing or decreasing over time?Safety Performance

📊 Dashboard Pages
Page 1 — Executive Overview

The CEO landing page — headline business health in 30 seconds

KPIs: Total Revenue · Total Miles · Active Drivers · Profit · Total Maintenance Cost · Fuel % of Revenue · On-Time Delivery Rate · Active Trucks
Visuals:

Monthly Revenue Trend (3-year line chart)
Revenue by Booking Type (bar chart)
Revenue by Load Type (donut chart)
Revenue by Customer Type (bar chart)
Top 10 Revenue by Routes (bar chart)
Driver Revenue Performance (ranked table)

Key Finding: Revenue is flat at ~$99M/year with zero growth despite consistent operations. Contract customers lead at $112M — ahead of Dedicated at $92M.

Page 2 — Operational Efficiency

Where is fuel going and which routes earn the most?

KPIs: Total Fuel Cost · Fuel % of Revenue · On-Time Delivery Rate · Delayed Delivery Rate · Avg Fuel Cost per Trip · Revenue per Mile · Avg MPG
Visuals:

Fuel Cost vs Revenue Monthly Trend (dual-axis combo chart)
Fuel Cost Trend 2022–2024 (area line chart)
Top Fuel Cost States (filled map)
Revenue per Mile by Route (horizontal bar chart)
Avg MPG (gauge chart)

Key Finding: Texas ($7.64M) and Tennessee ($7.62M) account for 16% of all fuel spend. Philadelphia → New York earns $2.80/mile — the highest yield route in the network.

Page 3 — Drivers & Fleet Performance

People and assets under the microscope

KPIs: Avg Revenue per Driver · Avg Maintenance Cost per Vehicle · Total Downtime · Active Driver Count · Avg Utilisation Rate
Visuals:

Driver Performance Ranking Table (revenue + OTD + MPG + trips)
Driver Efficiency Scatter Plot (revenue vs OTD rate — categorised as Stars / High Rev)
Maintenance by Truck Make (combo bar + line chart)
Maintenance Event Count — Top Trucks (bar chart)
Maintenance Type Cost Breakdown (horizontal bar chart)

Key Finding: TRK00003 leads with 41 maintenance events — 69% above the 24.3 fleet average. Preventive ($0.96M) and Repair ($0.95M) costs are nearly equal, signalling reactive maintenance is occurring.

Page 4 — Safety Performance

The page leadership always checks first

KPIs: Total Safety Incidents · Preventable Incident Rate · At-Fault Incident Rate · Total Fleet Cost · Highest Risk State · Highest Risk Driver Name
Visuals:

Safety Incidents Map by State (filled map)
Total Safety Incidents by State (bar chart)
Safety Incidents Yearly Trend vs Claims Cost (dual-axis chart)
Driver Safety Performance (horizontal bar — incidents + preventable count)
Incident Type by Cost Components (stacked bar)

Key Finding: Incidents rose to 60 in 2024 (+11.1%) but claims cost fell to $840K — more incidents, less severity per incident. David Miller identified as highest-risk driver with 7 incidents. Tennessee leads state risk with 18 incidents.

🗄️ Data Sources
TableRowsTypeDescriptioncustomers200DimensionCustomer accounts, types, revenue potentialdrivers150DimensionDriver demographics, employment, CDL infotrucks120DimensionFleet equipment, make, model year, statustrailers180DimensionTrailer inventory, type, statusfacilities50DimensionTerminal and warehouse locationsroutes58DimensionOrigin-destination pairs, distances, ratesloads85,410FactAll shipments — revenue, surcharge, typetrips85,410FactTrip execution — distance, fuel, MPG, idlefuel_purchases196,442FactFuel transactions — gallons, price, locationdelivery_events170,820FactPickup/delivery timestamps, on-time flagmaintenance_records2,920FactService history, type, cost, downtimesafety_incidents170FactIncidents — type, fault, injury, claimsdriver_monthly_metrics4,464AggregateMonthly driver performance summariestruck_utilization_metrics3,312AggregateMonthly truck utilisation summaries

Note: driver_monthly_metrics and truck_utilization_metrics are pre-aggregated summaries derived from the loads and trips tables. They were excluded from all revenue measures to prevent double-counting. The single source of truth for all revenue is loads[revenue] + loads[fuel_surcharge] + loads[accessorial_charges].


🔗 Data Model
The model follows a Star Schema architecture — fact tables in the centre, dimension tables connected outward via single-direction relationships.
customers ──────────────────────┐
                                 │ (Many-to-One)
routes ──────────────────────── loads ──────── trips ──────── drivers
                                               │    │
                               fuel_purchases  │    └──────── trucks ──── maintenance_records
                                               │    │
                               delivery_events │    └──────── trailers
                                               │
                               safety_incidents┘
                                               │
                               facilities ─────┘ (via delivery_events)
Total relationships: 11 · Filter direction: Single (all) · Cardinality: Many-to-One (10) + One-to-One (trips ↔ loads)

🧮 Key DAX Measures
Revenue Measures
daxTotal Revenue =
SUMX(
    loads,
    loads[revenue] + loads[fuel_surcharge] + loads[accessorial_charges]
)

Profit =
[Total Revenue] - [Total Fuel Cost] - [Total Maintenance Cost]

Revenue per Mile =
DIVIDE([Total Revenue], SUM(trips[actual_distance_miles]), 0)

Fuel % of Revenue =
DIVIDE([Total Fuel Cost], [Total Revenue], 0)
Delivery Performance
daxOn-Time Delivery Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS(delivery_events),
        delivery_events[event_type] = "Delivery",
        delivery_events[on_time_flag] = TRUE()
    ),
    CALCULATE(
        COUNTROWS(delivery_events),
        delivery_events[event_type] = "Delivery"
    ),
    0
)

Delayed Delivery Rate = 1 - [On-Time Delivery Rate]
Driver Performance
daxDrivers Name = drivers[first_name] & " " & drivers[last_name]  -- Calculated Column

Driver Performance Score =
([On-Time Delivery Rate] * 0.6) + DIVIDE([Total Revenue], 10000000) * 0.4

Driver Risk Level =
SWITCH(
    TRUE(),
    [Driver Incident Count] >= 5, "High Risk",
    [Driver Incident Count] >= 3, "Medium Risk",
    "Low Risk"
)
Safety Measures
daxPreventable Incident Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS(safety_incidents),
        safety_incidents[preventable_flag] = TRUE()
    ),
    [Total Safety Incidents],
    0
)

Total Fleet Cost =
[Total Fuel Cost] + [Total Maintenance Cost] + [Total Claims Cost]

Highest Risk State =
CALCULATE(
    SELECTEDVALUE(safety_incidents[location_state]),
    TOPN(
        1,
        VALUES(safety_incidents[location_state]),
        CALCULATE(COUNTROWS(safety_incidents)),
        DESC
    )
)

🧹 Data Cleaning Summary
All cleaning was performed in Power Query Editor before loading into the model.
IssueAffected TableAction Taken1,714 null driver_id in completed tripstripsReplaced with "Unknown"1,672 null truck_id in completed tripstripsReplaced with "Unknown"~3,880 null truck_id in fuel recordsfuel_purchasesReplaced with "Unknown"1 null truck_id and driver_idsafety_incidentsReplaced with "Unknown"All date columns stored as textAll 14 tablesConverted to Date/DateTime typetermination_date 83% nulldriversNo change — expected for active drivers. employment_status already carries Active/Terminatedhome_terminal considered for join to facilitiesdrivers, trucksNo relationship created — keys do not matchtotal_revenue in aggregate tables considered for measuresdriver_monthly_metrics, truck_utilization_metricsExcluded — these are loads rollups, using them causes double-counting

💡 Key Insights
#InsightImpact🔴Only 44.6% of deliveries arrive on time — a contract retention riskCritical🔴Revenue is flat for 3 years — $99.9M (2022) → $99.8M (2024)Critical🔴Safety incidents rose 11.1% in 2024 (60 vs 54 in 2023)Critical💡$438M annual revenue gap — capturing only 18.5% of customer potentialOpportunity💡$1.96/mile gap between best route (Phila→NY $2.80) and worst (Charlotte→Denver)Opportunity💡$187K/year revenue gap between top and bottom driverOpportunity⚠️Fuel costs declining — $34.3M (2022) → $30.1M (2024) — margin quietly improvingPositive trend⚠️Preventive and Repair costs nearly equal ($0.96M vs $0.95M) — reactive maintenance signalWarning

✅ Recommendations

Improve dispatch and dock scheduling to increase on-time deliveries toward a 75% target
Renegotiate freight rates on all contracts older than 12 months — target $5–15M annual uplift
Reduce driver idle time — current average 7 hrs/trip. A 29% reduction saves ~$135K/year
Focus capacity on high-yield routes — Philadelphia→NY, Las Vegas→LA, Portland→Seattle
Launch a driver performance programme — composite score (OTD 60% + Revenue 40%) with coaching for bottom quartile
Build a state-level refuelling strategy for TX and TN corridors — together accounting for 16% of all fuel spend
Increase preventive maintenance to achieve a 2:1 preventive-to-repair ratio — currently 1.02:1
Implement pre-trip inspection checklists — Equipment Damage at $42K per incident is the costliest and most preventable type
Launch a safety frequency reduction programme — targeting 37.6% preventable rate down to 25% within 12 months
Map customer revenue vs potential — grow share of wallet with existing Contract customers before new acquisition




🛠️ Tools & Technologies
ToolPurposePower BI DesktopDashboard development and visualisationPower Query EditorData cleaning, type conversion, transformationDAX (Data Analysis Expressions)All KPI measures and calculated columnsStar Schema ModellingRelational data model architectureMicrosoft Bing MapsGeographic incident and fuel cost visualisation

🎨 Dashboard Design Decisions

Colour convention: Green = Good · Red = Issue/Risk · Amber = Warning · Purple = Neutral
Theme: Clean executive style — white backgrounds, bold KPI numbers, consistent green accent colour
Layout: KPI cards top → trend charts middle → detailed breakdowns bottom (all 4 pages)
Slicers: Year · Drivers Name · Load Type · Booking Type · Location State · Destination City
Conditional formatting: Applied to driver ranking tables (Green/Amber/Red by incident count and revenue tier)
Driver categories: Stars (high revenue + high OTD) · High Rev (high revenue, average OTD) — visible in scatter plot legend


📄 Documentation
Full project documentation covering data profiling, cleaning decisions, model architecture, all DAX measures, visual configurations, issues encountered, and recommendations is available in:
📎 Logistics_Dashboard_Project_Documentation.docx
Documentation includes:

Section 1 — Project Overview & Objectives
Section 2 — Data Sources (all 14 tables)
Section 3 — Data Profiling Results
Section 4 — Data Cleaning Process
Section 5 — Issues Encountered & Resolutions (12 issues logged)
Section 6 — Data Model & Relationships
Section 7 — All DAX Measures (4 pages)
Section 8 — Dashboard Pages Layout
Section 9 — Business Questions & Answers (18 questions)
Section 10 — Key Insights & Recommendations
Section 11 — Decisions Made & Rationale
Section 12 — Implementation Details & Visual Configuration
Appendix A — KPI Reference (21 KPIs)
Appendix B — Glossary


👤 Author
Aghe Marvellous
Data Analyst
https://www.linkedin.com/in/aghe-marvellous-395285344/

This project was completed as a capstone analytics project demonstrating end-to-end Power BI development — from raw CSV data through cleaning, modelling, DAX development, and dashboard design to actionable business insights.
