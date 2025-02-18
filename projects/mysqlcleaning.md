---
title: Data cleaning in MySQL
layout: page
---
<h1 style="color:#9300ff;">Data cleaning in MySQL</h1>
<h3></h3>
<h2 style="text-align:center;color:#9300ff;">Hungarian prices</h2>
<h3>Unfortunately for me, there was a lot wrong with this data,from messed up columns to missing values so it really challanged me to fix it efficiently.</h3>
<details>
    <summary>Process</summary>
    <pre>
    <samp>
#SELECT *
#FROM tobacco_prices_2023_hu
#WHERE PRICE NOT REGEXP "[0-9]"

update tobacco_prices_2023_hu
set PRODUCT = CONCAT(PRODUCT, PRICE)
WHERE PRICE REGEXP "[a-z]";

SELECT PRICE 
FROM tobacco_prices_2023_hu
WHERE PRICE REGEXP "[a-z]";

select PRICE,REGEXP_REPLACE(PRICE, '[^0-9]+', '')
FROM tobacco_prices_2023_hu;

update tobacco_prices_2023_hu
set PRICE = REGEXP_REPLACE(PRICE, '[^0-9]+', '')
WHERE PRICE REGEXP "[a-z]";

select substr(PRICE,2)
from tobacco_prices_2023_hu
WHERE PRICE LIKE "0%%%";

update tobacco_prices_2023_hu 
SET PRICE = substr(PRICE,2)
WHERE PRICE LIKE "0%%%";

select PRICE
from tobacco_prices_2023_hu
WHERE PRICE LIKE "%00%%%%" and length(price) >4;

select substring(PRICE,4)
from tobacco_prices_2023_hu
WHERE PRICE LIKE "%00%%%%" and length(price) >4;

update tobacco_prices_2023_hu 
SET PRICE = substr(PRICE,4)
WHERE PRICE LIKE "%00%%%%" and length(price) >4;

SELECT substr(PRICE,3)
FROM tobacco_prices_2023_hu
WHERE PRICE LIKE "%0%%%" AND length(PRICE) > 4;

update tobacco_prices_2023_hu 
SET PRICE = substr(PRICE,3)
WHERE PRICE LIKE "%0%%%" AND length(PRICE) > 4;

update tobacco_prices_2023_hu
SET PRICE = ""
WHERE PRICE < 900;

select *,REGEXP_REPLACE(CURRENCY, '[^0-9]+', '')
FROM tobacco_prices_2023_hu
WHERE length(REGEXP_REPLACE(CURRENCY, '[^0-9]+', '')) > 1;

update tobacco_prices_2023_hu
SET CURRENCY = REGEXP_REPLACE(CURRENCY, '[^0-9]+', '')
WHERE length(REGEXP_REPLACE(CURRENCY, '[^0-9]+', '')) > 1;

select *,REGEXP_REPLACE(CURRENCY, '[^0-9]+', '')
FROM tobacco_prices_2023_hu
WHERE CURRENCY LIKE "%%%%%" AND length(CURRENCY) >4;

UPDATE tobacco_prices_2023_hu 
SET CURRENCY = substring(CURRENCY,3)
WHERE CURRENCY LIKE "%%%%%" AND length(CURRENCY) >5;

select substring(CURRENCY,1,4)
FROM tobacco_prices_2023_hu
WHERE CURRENCY LIKE "%%%%%" AND length(CURRENCY) >4;

UPDATE tobacco_prices_2023_hu
SET CURRENCY = substring(CURRENCY,1,4)
WHERE CURRENCY LIKE "%%%%%" AND length(CURRENCY) >4;

UPDATE tobacco_prices_2023_hu
SET CURRENCY = ""
WHERE CURRENCY REGEXP'[a-z]';

update tobacco_prices_2023_hu
set PRICE = CONCAT(PRICE, CURRENCY)
WHERE PRICE = "";

ALTER TABLE tobacco_prices_2023_hu
DROP COLUMN CURRENCY;

SELECT substring(PRODUCT,2)
FROM tobacco_prices_2023_hu;

UPDATE tobacco_prices_2023_hu
SET PRODUCT = substring(PRODUCT,2);


SELECT PRODUCT, REGEXP_REPLACE(PRODUCT, '\\\\xa0', ' ')
FROM tobacco_prices_2023_hu
WHERE PRODUCT LIKE "%xa%";

UPDATE tobacco_prices_2023_hu
SET PRODUCT = REGEXP_REPLACE(PRODUCT, '\\\\xa0', ' ')
WHERE PRODUCT LIKE "%xa%";

SELECT LEFT(PRODUCT, char_length(PRODUCT) - 4)
FROM tobacco_prices_2023_hu
WHERE PRODUCT REGEXP"[0-9]" and PRODUCT LIKE "%1___" and PRODUCT not REGEXP "[s]$";

UPDATE tobacco_prices_2023_hu
SET PRODUCT = LEFT(PRODUCT, char_length(PRODUCT) - 4)
WHERE PRODUCT REGEXP"[0-9]" and PRODUCT LIKE "%1___" and PRODUCT not REGEXP "[s]$";

SELECT LEFT(PRODUCT, char_length(PRODUCT) - 3)
FROM tobacco_prices_2023_hu
WHERE PRODUCT REGEXP"[0-9]" and PRODUCT LIKE "%9__" and PRODUCT not REGEXP "[s]$";

UPDATE tobacco_prices_2023_hu
SET PRODUCT = LEFT(PRODUCT, char_length(PRODUCT) - 3)
WHERE PRODUCT REGEXP"[0-9]" and PRODUCT LIKE "%9__" and PRODUCT not REGEXP "[s]$";

SELECT concat(UPPER(PRICE)," FT")
FROM tobacco_prices_2023_hu;

UPDATE tobacco_prices_2023_hu
SET PRICE = concat(UPPER(PRICE)," FT");

SELECT REGEXP_REPLACE(PRODUCT,":","")
FROM tobacco_prices_2023_hu
WHERE PRODUCT LIKE "%:%";

UPDATE tobacco_prices_2023_hu
SET PRODUCT = REGEXP_REPLACE(PRODUCT,":","")
WHERE PRODUCT LIKE "%:%";

SELECT replace(PRODUCT,"L D","LD")
FROM tobacco_prices_2023_hu
WHERE PRODUCT LIKE "L D%";

UPDATE tobacco_prices_2023_hu
SET PRODUCT = replace(PRODUCT,"L D","LD")
WHERE PRODUCT LIKE "L D%";

SELECT *
FROM tobacco_prices_2023_hu;

SELECT PRODUCT,PRICE
FROM tobacco_prices_2023_hu
GROUP BY PRODUCT,PRICE
HAVING count(*) >1;

create table tobacco_prices_2023_hu_final like tobacco_prices_2023_hu;

INSERT INTO tobacco_prices_2023_hu_final
SELECT DISTINCT*
FROM tobacco_prices_2023_hu;

SELECT PRODUCT, (ROUND(PRICE/381.85,1))
FROM tobacco_prices_2023_hu_final;

ALTER TABLE tobacco_prices_2023_hu_final
ADD COLUMN PRICE_EUR TEXT;

UPDATE tobacco_prices_2023_hu_final
SET PRICE_EUR = (ROUND(PRICE/381.85,1));

UPDATE tobacco_prices_2023_hu_final
SET PRICE_EUR = CONCAT(PRICE_EUR," €");

SELECT DISTINCT *
FROM tobacco_prices_2023_hu_final;
    </samp>
    </pre>
