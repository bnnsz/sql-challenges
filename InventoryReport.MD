### Challenge: Generate an Inventory Report using SQL

##### Context:
You are working on an inventory management system. The system keeps track of stock entries for every item in an order. The system uses several tables including `inventory_table_stocks`, `inventory_table_products`, `inventory_table_order_items`, and `inventory_table_sales_order`. The system also has other tables that are not provided in the context.

The system is used to manage the inventory of products, track sales orders, and generate reports to help with inventory management decisions. The reports include metrics such as `abcCategory`, `abcCategoryTrend`, `aging`, `agingTrend`, `turnover`, `turnoverTrend`, `stockValue`, `stockValueTrend`, `deadStock`, `deadStockTrend`, etc.

The system is designed to store information about the stock of products, the products themselves, the items in sales orders, and the sales orders. The stock entries represent the quantity of a product in the inventory at a specific location. The products represent the items that are sold or used in the inventory. The order items represent the items in a sales order, including the quantity, price, and other details. The sales orders represent the orders placed by customers.

When a sales order is placed, the system creates stock entries to update the quantity of the products in the inventory. The stock entries can be positive (restocking) or negative (sales). The system also keeps track of the total quantity of each product in the inventory after each stock entry.

For example, consider the following `inventory_table_stocks` table:

| id | date_stock | quantity | uom_id | product_id | location_id | last_total_quantity | current_total_quantity | external_ref | last_restocked_date |
|----|------------|----------|--------|------------|------------|---------------------|-----------------------|-------------|---------------------|
| 1  | 2022-01-01 | 100      | 1      | 1          | 1          | 0                   | 100                   | ABC123      | 2022-01-01          |
| 2  | 2022-01-02 | -50      | 1      | 1          | 1          | 100                 | 50                    | XYZ456      | 2022-01-01          |
| 3  | 2022-01-03 | 200      | 1      | 2          | 1          | 0                   | 200                   | DEF789      | 2022-01-03          |
| 4  | 2022-01-04 | -4     | 1      | 1      | 1          | 50                  | 46                    | GHI101      | 2022-01-04          |

Whe
###### Tables:
The system has the following tables:

1. `inventory_table_stocks`: Represents the stock of a product in the inventory
    - `id` (Primary Key): Unique identifier for each stock entry.
    - `date_stock` (Date): The date when the stock entry was created.
    - `quantity` (Double): The quantity of the product in transit for the operation. The quantity can be positive (restocking) or negative (sales).
    - `uom_id` (Foreign Key): The unit of measurement for the product quantity.
    - `product_id` (Foreign Key): The product associated with the stock entry.
    - `location_id` (Foreign Key): The location where the stock is stored.
    - `last_total_quantity` (Double): The total quantity of the product in the previous stock entry.
    - `current_total_quantity` (Double): The total quantity of the product in the stock after the entry (last_total_quantity + quantity).
    - `external_ref` (String): An external reference associated with the stock entry.
    - `last_restocked_date` (Date): The date when the stock was last restocked.

2. `inventory_table_products`: Represents a product in the inventory
    - `id` (Primary Key): Unique identifier for each product.
    - `sku` (String): The Stock Keeping Unit associated with the product.
    - `barcode` (String): The barcode associated with the product.
    - `cost_price` (Double): The cost price of the product.
    - `serial_number` (String): The serial number of the product.
    - `is_service` (Boolean): A flag indicating whether the product is a service.
    - `name` (String): The name of the product.
    - `image_url` (String): The URL of the product's image.
    - `category_id` (Foreign Key): The category to which the product belongs.

3. `inventory_table_order_items`: Represents an item in an order
    - `id` (Primary Key): Unique identifier for each order item.
    - `source_ref` (String): An external reference associated with the order item.
    - `product_id` (Foreign Key): The product associated with the order item.
    - `order_id` (Foreign Key): The order associated with the order item.
    - `quantity` (Integer): The quantity of the product in the order item.
    - `price` (Double): The price of the product in the order item.
    - `vat` (Double): The Value Added Tax associated with the order item.
    - `total` (Double): The total price of the order item (quantity * price).
    - `sub_total` (Double): The subtotal price of the order item (total - discount).
    - `discount` (Double): The discount applied to the order item.
    - `uom_id` (Foreign Key): The unit of measurement for the product quantity.

4. `inventory_table_sales_order`: Represents an order in the system
    - `id` (Primary Key): Unique identifier for each order.
    - `source_ref` (String): An external reference associated with the order.
    - `description` (String): A description of the order.
    - `total` (Double): The total price of the order.
    - `status` (Enum `OrderStatus`): The status of the order (e.g., pending, completed, cancelled).
    - `sub_total_amount` (Double): The subtotal price of the order (total - discount).
    - `vat_amount` (Double): The Value Added Tax associated with the order.
    - `discount_amount` (Double): The discount applied to the order.
    - `location_id` (Foreign Key): The location where the order was placed.
    - `terminal` (String): The terminal where the order was placed.
    - `loyalty_points_earned` (Long): The loyalty points earned from the order.
    - `loyalty_program_id` (Foreign Key): The loyalty program associated with the order.
    - `customer_id` (Foreign Key): The customer who placed the order.
    - `currency` (String): The currency used for the order.

Please note that the actual table structure and column descriptions may vary based on the actual database schema and the other entity classes not provided in the context.

##### Challenge:
Your task is to write a SQL query that generates an inventory report. The report should include all the metrics such as `abcCategory`, `abcCategoryTrend`, `aging`, `agingTrend`, `turnover`, `turnoverTrend`, `stockValue`, `stockValueTrend`, `deadStock`, `deadStockTrend`, etc. The report should also calculate the trend based on the previous period.
Also, the report can be generated for a specific entity or for the entire inventory. Entity can be a product, category or location.
Considerations:
- You need to consider how the stock entries are created for every item in an order.
- You need to consider how the tables are related and how they are structured in the database.
- You need to consider how to calculate the metrics and the trends based on the current and previous periods.

##### Metrics

1. `abcCategory`: This metric categorizes items based on their importance or value to the business. It typically uses the Pareto principle, also known as the 80/20 rule, where a small percentage of items contribute to a large percentage of the value. The categories are usually labeled as A, B, and C, with A representing the most valuable items and C representing the least valuable items.

2. `abcCategoryTrend`: This metric calculates the trend of the ABC category for each item over a period of time. It helps identify if an item's category is changing over time, which can be useful for inventory management decisions.

3. `aging`: This metric measures the age of inventory items based on the time elapsed since they were received or added to the inventory. It helps identify items that have been in the inventory for a long time and may need special attention, such as clearance sales or markdowns.

4. `agingTrend`: This metric calculates the trend of the aging metric for each item over a period of time. It helps identify if an item's age is increasing or decreasing over time, which can be useful for inventory planning and forecasting.

5. `turnover`: This metric measures how quickly inventory items are sold or used up within a specific period. It is usually calculated as the ratio of the cost of goods sold to the average inventory value. A high turnover indicates efficient inventory management, while a low turnover may indicate excess inventory or slow sales.

6. `turnoverTrend`: This metric calculates the trend of the turnover metric for each item over a period of time. It helps identify if an item's turnover is improving or declining over time, which can be useful for evaluating the effectiveness of inventory management strategies.

7. `stockValue`: This metric represents the total value of the inventory, usually calculated as the sum of the individual item values. It helps provide an overall view of the investment tied up in inventory.

8. `stockValueTrend`: This metric calculates the trend of the stock value metric for the entire inventory over a period of time. It helps identify if the overall value of the inventory is increasing or decreasing over time, which can be useful for financial analysis and decision-making.

9. `deadStock`: This metric identifies items in the inventory that have not been sold or used for a long time and may be considered obsolete or non-performing. It helps identify inventory items that may need to be written off or liquidated.

10. `deadStockTrend`: This metric calculates the trend of the dead stock metric for the entire inventory over a period of time. It helps identify if the amount of dead stock is increasing or decreasing over time, which can be useful for inventory clearance strategies.


**NOTE: Your solution should be in SQL. You should provide all the necessary SQL queries needed to generate the report.**