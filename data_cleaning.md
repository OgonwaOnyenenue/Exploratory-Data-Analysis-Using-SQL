
```sql
-- Data Cleaning

-- Rename column names in products table
ALTER TABLE products RENAME COLUMN product_name_lenght TO product_name_length;
ALTER TABLE products RENAME COLUMN product_description_lenght TO product_description_length;

-- Delete review_comment_title from order_reviews table
ALTER TABLE order_reviews DROP COLUMN review_comment_title;

-- Delete review_comment_message from order_reviews table
ALTER TABLE order_reviews DROP COLUMN review_comment_message;

-- Replace NULLs in product_category_name column
UPDATE products
SET product_category_name = COALESCE(product_category_name, 'N/A') 
WHERE product_category_name IS NULL;

-- Replace NULLs in product_name_length column
UPDATE products
SET product_name_length = COALESCE(product_name_length, '0') 
WHERE product_name_length IS NULL;

-- Replace NULLs in product_description_length column
UPDATE products
SET product_description_length = COALESCE(product_description_length, '0') 
WHERE product_description_length IS NULL;

-- Replace NULLs in product_photos_qty column
UPDATE products
SET product_photos_qty = COALESCE(product_photos_qty, '0') 
WHERE product_photos_qty IS NULL;

-- Replace NULLs in product_weight_g column
UPDATE products
SET product_weight_g = COALESCE(product_weight_g, '0') 
WHERE product_weight_g IS NULL;

-- Replace NULLs in product_length_cm column
UPDATE products
SET product_length_cm = COALESCE(product_length_cm, '0') 
WHERE product_length_cm IS NULL;

-- Replace NULLs in product_height_cm column
UPDATE products
SET product_height_cm = COALESCE(product_height_cm, '0') 
WHERE product_height_cm IS NULL;

-- Replace NULLs in product_width_cm column
UPDATE products
SET product_width_cm = COALESCE(product_width_cm, '0') 
WHERE product_width_cm IS NULL;

-- Replace null values in the date columns of the orders table
-- Get the mean time it takes for an order to be approved
SELECT AVG(order_approved_at - order_purchase_timestamp) FROM orders
WHERE order_approved_at IS NOT NULL;

-- Replace null values in order_approved date column
UPDATE orders
SET order_approved_at = COALESCE(order_approved_at, order_purchase_timestamp + INTERVAL '10 days 25 hours 11 minutes 665072 microseconds') 
WHERE order_approved_at IS NULL;

-- Get the mean time it takes for an approved order to be delivered by the carrier
SELECT AVG(order_delivered_carrier_date - order_approved_at) FROM orders
WHERE order_delivered_carrier_date IS NOT NULL;

-- Replace null values in order_delivered_carrier_date column
UPDATE orders
SET order_delivered_carrier_date = COALESCE(order_delivered_carrier_date, order_approved_at + INTERVAL '2 days 19 hours 17 minutes 26 seconds 665072 microseconds') 
WHERE order_delivered_carrier_date IS NULL;

-- Get the mean time it takes for an approved order to get to the customer
SELECT AVG(order_delivered_customer_date - order_delivered_carrier_date) FROM orders
WHERE order_delivered_customer_date IS NOT NULL;

-- Replace null values in order_delivered_customer_date column
UPDATE orders
SET order_delivered_customer_date = COALESCE(order_delivered_customer_date, order_delivered_carrier_date + INTERVAL '8 days 31 hours 56 minutes 24 seconds 889437 microseconds') 
WHERE order_delivered_customer_date IS NULL AND order_status <> 'canceled';

-- Remove special characters from geolocation_city column
CREATE EXTENSION IF NOT EXISTS unaccent;

UPDATE geolocations
SET geolocation_city = unaccent(geolocation_city);

-- Remove leading whitespace from geolocation_city column
UPDATE geolocations
SET geolocation_city = TRIM(geolocation_city);

-- Capitalize lowercase
-- geolocation_city column
UPDATE geolocations
SET geolocation_city = UPPER(LEFT(geolocation_city, 1)) || RIGHT(geolocation_city, LENGTH(geolocation_city) - 1);

-- customer_city column
UPDATE customers
SET customer_city = UPPER(LEFT(customer_city, 1)) || RIGHT(customer_city, LENGTH(customer_city) - 1);

-- seller_city column
UPDATE sellers
SET seller_city = UPPER(LEFT(seller_city, 1)) || RIGHT(seller_city, LENGTH(seller_city) - 1);

-- Change state abbreviations to state full names
-- seller_state
UPDATE sellers SET seller_state =
    CASE 
         WHEN seller_state = 'AC' THEN 'Acre'
		 WHEN seller_state = 'AL' THEN 'Alagoas'
		 WHEN seller_state = 'AL' THEN 'Alagoas'
		 WHEN seller_state = 'AP' THEN 'Amapa'
		 WHEN seller_state = 'AM' THEN 'Amazonas'
		 WHEN seller_state = 'BA' THEN 'Bahia'
		 WHEN seller_state = 'CE' THEN 'Ceara'
		 WHEN seller_state = 'DF' THEN 'Distrito Federal'
		 WHEN seller_state = 'ES' THEN 'Espirito Santo'
		 WHEN seller_state = 'GO' THEN 'Goias'
		 WHEN seller_state = 'MA' THEN 'Maranhao'
		 WHEN seller_state = 'MT' THEN 'Mato Grosso'
		 WHEN seller_state = 'MS' THEN 'Mato Grosso do Sul'
		 WHEN seller_state = 'MG' THEN 'Minas Gerais'
		 WHEN seller_state = 'PA' THEN 'Para'
		 WHEN seller_state = 'PB' THEN 'Paraiba'
		 WHEN seller_state = 'PR' THEN 'Parana'
		 WHEN seller_state = 'PE' THEN 'Pernambuco'
		 WHEN seller_state = 'PI' THEN 'Piaui'
		 WHEN seller_state = 'RJ' THEN 'Rio de Janeiro'
		 WHEN seller_state = 'RN' THEN 'Rio Grande do Norte'
		 WHEN seller_state = 'RS' THEN 'Rio Grande do Sul'
		 WHEN seller_state = 'RO' THEN 'Rondonia'
		 WHEN seller_state = 'RR' THEN 'Roraima'
		 WHEN seller_state = 'SC' THEN 'Santa Catarina'
		 WHEN seller_state = 'SP' THEN 'Sao Paulo'
		 WHEN seller_state = 'SE' THEN 'Sergipe'
		 WHEN seller_state = 'TO' THEN 'Toca		 ELSE seller_state
		 END;
    END;

-- customer_state
UPDATE customers SET custom WHEN customer_state = 'AC' THEN 'Acre'
		 WHEN customer_state = 'AL' THEN 'Alagoas'
		 WHEN customer_state = 'AL' THEN 'Alagoas'
		 WHEN customer_state = 'AP' THEN 'Amapa'
		 WHEN customer_state = 'AM' THEN 'Amazonas'
		 WHEN customer_state = 'BA' THEN 'Bahia'
		 WHEN customer_state = 'CE' THEN 'Ceara'
		 WHEN customer_state = 'DF' THEN 'Distrito Federal'
		 WHEN customer_state = 'ES' THEN 'Espirito Santo'
		 WHEN customer_state = 'GO' THEN 'Goias'
		 WHEN customer_state = 'MA' THEN 'Maranhao'
		 WHEN customer_state = 'MT' THEN 'Mato Grosso'
		 WHEN customer_state = 'MS' THEN 'Mato Grosso do Sul'
		 WHEN customer_state = 'MG' THEN 'Minas Gerais'
		 WHEN customer_state = 'PA' THEN 'Para'
		 WHEN customer_state = 'PB' THEN 'Paraiba'
		 WHEN customer_state = 'PR' THEN 'Parana'
		 WHEN customer_state = 'PE' THEN 'Pernambuco'
		 WHEN customer_state = 'PI' THEN 'Piaui'
		 WHEN customer_state = 'RJ' THEN 'Rio de Janeiro'
		 WHEN customer_state = 'RN' THEN 'Rio Grande do Norte'
		 WHEN customer_state = 'RS' THEN 'Rio Grande do Sul'
		 WHEN customer_state = 'RO' THEN 'Rondonia'
		 WHEN customer_state = 'RR' THEN 'Roraima'
		 WHEN customer_state = 'SC' THEN 'Santa Catarina'
		 WHEN customer_state = 'SP' THEN 'Sao Paulo'
		 WHEN customer_state = 'SE' THEN 'Sergipe'
		 WHEN customer_state = 'TO' THEN 'Tocantins'
		 ELSE customer_statee
        ELSE customer_state
    END;

-- geolocation_state
UPDATE geolocations SET geolocat  WHEN geolocation_state = 'AC' THEN 'Acre'
		 WHEN geolocation_state = 'AL' THEN 'Alagoas'
		 WHEN geolocation_state = 'AL' THEN 'Alagoas'
		 WHEN geolocation_state = 'AP' THEN 'Amapa'
		 WHEN geolocation_state = 'AM' THEN 'Amazonas'
		 WHEN geolocation_state = 'BA' THEN 'Bahia'
		 WHEN geolocation_state = 'CE' THEN 'Ceara'
		 WHEN geolocation_state = 'DF' THEN 'Distrito Federal'
		 WHEN geolocation_state = 'ES' THEN 'Espirito Santo'
		 WHEN geolocation_state = 'GO' THEN 'Goias'
		 WHEN geolocation_state = 'MA' THEN 'Maranhao'
		 WHEN geolocation_state = 'MT' THEN 'Mato Grosso'
		 WHEN geolocation_state = 'MS' THEN 'Mato Grosso do Sul'
		 WHEN geolocation_state = 'MG' THEN 'Minas Gerais'
		 WHEN geolocation_state = 'PA' THEN 'Para'
		 WHEN geolocation_state = 'PB' THEN 'Paraiba'
		 WHEN geolocation_state = 'PR' THEN 'Parana'
		 WHEN geolocation_state = 'PE' THEN 'Pernambuco'
		 WHEN geolocation_state = 'PI' THEN 'Piaui'
		 WHEN geolocation_state = 'RJ' THEN 'Rio de Janeiro'
		 WHEN geolocation_state = 'RN' THEN 'Rio Grande do Norte'
		 WHEN geolocation_state = 'RS' THEN 'Rio Grande do Sul'
		 WHEN geolocation_state = 'RO' THEN 'Rondonia'
		 WHEN geolocation_state = 'RR' THEN 'Roraima'
		 WHEN geolocation_state = 'SC' THEN 'Santa Catarina'
		 WHEN geolocation_state = 'SP' THEN 'Sao Paulo'
		 WHEN geolocation_state = 'SE' THEN 'Sergipe'
		 WHEN geolocation_state = 'TO' THEN 'Tocantins'
		 ELSE geolocation_state       ELSE geolocation_state
    END;
```
