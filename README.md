# Sales_Analytics_MsFabric_PowerBI

Tools: Microsoft Fabric (Pipelines, Notebooks/PySpark, Lakehouse, Warehouse, Star Schema, Semantic Model), Power BI Dataset: Simulated sales orders (1,000 orders, 93 customers, 3 product categories, 4 regions, 2023-2024) Live report: https://github.com/Snehaingale777/Fabric/blob/main/sales_data.csv

Why I built this

I wanted to build something that goes beyond just connecting Power BI to a flat file, and actually shows a full data pipeline the way a real company would set one up: raw data coming in on its own, getting cleaned automatically, getting modeled properly, and then landing in a dashboard without me touching anything by hand in between.

How I built it

I set up a pipeline that copies the raw sales CSV into a Lakehouse, then runs three notebooks one after another: one that loads the raw data as a table, one that cleans it up (fixing types, removing duplicates, standardizing values), and one that builds a summary layer on top. This follows the bronze, silver, gold pattern, where each layer is a bit more refined than the last.

From the cleaned silver data, I built out a proper star schema: one fact table for the actual orders, and separate dimension tables for customers, products, regions, and dates. I built the date dimension myself as a full calendar rather than pulling it from the orders, so months with zero sales still show up correctly instead of just being missing.

I also loaded the data into a Warehouse, organized into staging, analytics, and reporting schemas, and built a view and a stored procedure on top so the same queries don't need to be rewritten every time.

The report connects live to all of this through a Direct Lake semantic model, so it reflects new data automatically.

For security, I set up row-level security so a person assigned to a specific region only sees that region's data. I actually built this two ways: a dynamic version using a security table and USERPRINCIPALNAME() to match the logged-in user to their region automatically, and a static version with a hardcoded region per role. I ended up keeping the static version in the final build, since the dynamic version needs a real user identity to test against and I don't have other real user accounts on my trial to verify it against. The dashboard currently has the static role (Static_NORTH) applied as a working example of how RLS restricts data.

What's in the dashboard

Total revenue, total orders, total quantity sold, and average order value as headline numbers
A monthly trend of revenue and orders across 2023-2024
Revenue and orders broken down by region and by product category
A ranked table of the top 10 customers by revenue

Screenshots

[add your screenshots here: pipeline, Lakehouse tables, warehouse view/stored procedure, RLS setup, dashboard]

What I'd improve

I'd like to properly test and keep the dynamic row-level security setup instead of the static one, once I have real user accounts to verify it against. The logic for it is already built (a security table plus USERPRINCIPALNAME()), it just needs actual users to confirm it filters correctly.
