# Ecommerce
## Inventory logs analysis

### Project Overview
To analyze the store data and identify products with highest number of inventory changes, especially manual adjustments and  stockouts.

### Data sources
Four data sources are used for this analysis, inventory logs data which has information on the inventory changes and timestamp for each inventory change, the products table which has all the details of products, name, date added and updated etc, the product reviews table which has the details of products reviewed by customers and finally the supplier table which has details of suppliers, their contact and location. But the main data used for this analysis is the inventory logs table.

### Tools used
- Excel - used in initial cleaning of the data
- SQL - used in analysing the data and answering the question statement
- Power Bi - creating the report for this analysis

### Data Prepartion and Cleaning
We perforemed the following task at the initial stage
- Loading the data into Excel to see the content
- Used Power Query
  - To perform data standization of date and other columns
- handled nulls in some columns
- Importing data into SQL for further analysis

### Exploratory Data Analysis
This was performed to answer the key questions
1 Analyze inventory logs
2. Spot patterns in stockouts and manual adjustments
3. Highlight the products most affected

## Data Analysis
```sql
CREATE TABLE inventory_logs (
	log_id VARCHAR (50) PRIMARY KEY NOT NULL,
	product_id VARCHAR (50), 
	timestamp VARCHAR (150),
	change_type VARCHAR (150),
	quantity_changed int,
	stock_after_change int,
	log_notes VARCHAR (200)
```
```sql
SELECT * FROM inventory_logs
```
```sql
SELECT log_id, product_id, timestamp, change_type, quantity_changed, stock_after_change, log_notes,  
         ROW_NUMBER() OVER (
            PARTITION BY log_id, product_id, timestamp, change_type, quantity_changed, stock_after_change, log_notes
            ORDER BY timestamp) AS rn
FROM inventory_logs
```
```sql
SELECT SUM(CASE WHEN log_id IS NULL OR log_id = ' ' THEN 1 ELSE 0 END) AS log_id,
	SUM(CASE WHEN product_id IS NULL OR product_id = ' ' THEN 1 ELSE 0 END) AS product_id,
	SUM(CASE WHEN timestamp IS NULL THEN 1 ELSE 0 END) AS timestamp, 
	SUM(CASE WHEN change_type IS NULL OR change_type = ' ' THEN 1 ELSE 0 END) AS change_type,
	SUM(CASE WHEN quantity_changed IS NULL THEN 1 ELSE 0 END) AS quantity_changed,
	SUM(CASE WHEN stock_after_change IS NULL THEN 1 ELSE 0 END) AS stock_after_change,
	SUM(CASE WHEN log_notes IS NULL OR log_notes = ' ' THEN 1 ELSE 0 END) AS log_notes
FROM inventory_logs
```
```sql
SELECT
    percentile_disc(0.5) WITHIN GROUP (ORDER BY stock_after_change) AS median_score
FROM
    inventory_logs;

UPDATE inventory_logs
SET stock_after_change = 999
WHERE stock_after_change IS NULL;

UPDATE inventory_logs
SET timestamp = CURRENT_DATE
WHERE timestamp IS NULL;
```
```sql
SELECT
  product_id,
  SUM(CASE WHEN change_type = 'Inventory_Adjustment_Manual' THEN 1 ELSE 0 END) AS Inventory_Adjustment_Manual,
  SUM(CASE WHEN change_type = 'Stock_Transfer_Out' THEN 1 ELSE 0 END) AS Stock_Transfer_Out,
  SUM(CASE WHEN change_type = 'Stock_Correction_Remove' THEN 1 ELSE 0 END) AS Stock_Correction_Remove,
  COUNT(*) AS total_changes
FROM
  inventory_logs
GROUP BY
  product_id
ORDER BY
  total_changes DESC;
```
### Findings
- These products are the top 5 with the highest inventory changes  PROD0021, PROD0014, PROD0030, PROD0028, PROD0031.
- Date added for some products is far from the last updated date, this means that the inventory does not update automatically when a product is added or taken out.

### Recommendations
- An automated inventory system where items are updated automatically once there is an inventory change.
