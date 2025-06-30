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
