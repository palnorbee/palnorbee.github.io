---
title: Data cleaning in MySQL
layout: page
---
<h1 style="color:#9300ff;">Data cleaning in MySQL</h1>
<h3></h3>
<h2 style="text-align:center;color:#9300ff;">Hungarian prices.</h2>
<h3>Unfortunately for me, there was a lot wrong with this data, from messed-up columns to missing values, so it really challenged me to fix it efficiently.</h3>
<details>
    <summary>Process:</summary>
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
</details>
<h2 style="text-align:center;color:#9300ff;">Belgian prices.</h2>
<h3>This one was easier.I got rid of the price/200 column since the Hungarian products come in 20 pieces.</h3>
<details>
    <summary>Process:</summary>
    <pre>
    <samp>
SELECT *
FROM tobacco_prices_2023_be;

ALTER TABLE tobacco_prices_2023_be
DROP COLUMN PRICE_PER_200,DROP COLUMN PRICE_PER_200_CONT;

SELECT replace(PRICE_CONT,"0","")
FROM tobacco_prices_2023_be
WHERE PRICE_CONT = 0;

UPDATE tobacco_prices_2023_be
SET PRICE_CONT = REPLACE(PRICE_CONT,"0","")
WHERE PRICE_CONT = "0";

ALTER TABLE tobacco_prices_2023_be
MODIFY PRICE_CONT text;

SELECT CONCAT(PRICE,".",PRICE_CONT)
FROM tobacco_prices_2023_be
WHERE PRICE_CONT >0;

UPDATE tobacco_prices_2023_be
SET PRICE = CONCAT(PRICE,".",PRICE_CONT)
WHERE PRICE_CONT >0;

ALTER TABLE tobacco_prices_2023_be MODIFY PRICE TEXT;

ALTER TABLE tobacco_prices_2023_be
DROP COLUMN PRICE_CONT;

ALTER TABLE tobacco_prices_2023_be MODIFY PRICE FLOAT;

SELECT *
FROM tobacco_prices_2023_be;

SELECT RIGHT(PRODUCT, char_length(PRODUCT) - 2)
FROM tobacco_prices_2023_be;

UPDATE tobacco_prices_2023_be
SET PRODUCT = RIGHT(PRODUCT, char_length(PRODUCT) - 2);

UPDATE tobacco_prices_2023_be
SET PRODUCT = upper(PRODUCT);

CREATE TABLE tobacco_prices_2023_be_final LIKE tobacco_prices_2023_be;

INSERT INTO tobacco_prices_2023_be_final
SELECT *
FROM tobacco_prices_2023_be
WHERE SIZE = 20;

ALTER TABLE tobacco_prices_2023_be_final
DROP COLUMN SIZE;

ALTER TABLE tobacco_prices_2023_be_final
MODIFY PRICE TEXT;

UPDATE tobacco_prices_2023_be_final
SET PRICE = concat(PRICE," €");
    </samp>
    </pre>
</details>
<h2 style="text-align:center;color:#9300ff;">Results:</h2>
<a href="https://palnorbee.github.io/assets/tobacco_prices_2023_hu_clean.csv">Tobacco prices 2023 Hungary.</a><br><br>
<a href="https://palnorbee.github.io/assets/tobacco_prices_2023_be_clean.csv">Tobacco prices 2023 Belgium.</a>
<h6 style="text-align:center;color:#9300ff;">Resources:</h6>
<ul>
    <li>
        <a href="https://szamoldki.hu/hu/hirek/cigaretta-arak-2023-mennyibe-kerul-egy-doboz-cigi-2023-ban">Tobacco prices 2023 Hungary.</a>
    </li>
    <li>
      <a href="https://www.tabakshoekje.be/index.php?p=product&subcat=sigaretten">Tobacco prices 2023 Belgium.</a>  
    </li>
</ul>
