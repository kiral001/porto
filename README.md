# porto
gudang project

/* basic year  */
with basic_year as (
  select 
extract (year from o.created_at) year,
count(distinct o.order_id) Number_of_order,
count(distinct detail.id)Number_of_item,
round(sum (detail.sale_price),2) sales,
round(sum (detail.sale_price - detail.cost),2) profit


  from 
  bigquery-public-data.thelook_ecommerce.orders o
  right join
  ( select oi.product_id, oi.sale_price,p.cost, p.category, oi.order_id, oi.id
  from
  bigquery-public-data.thelook_ecommerce.order_items oi
  left join
  bigquery-public-data.thelook_ecommerce.products p
on p.id = oi.product_id
) detail
on detail.order_id = o.order_id
where o.status = "Complete" 

group by 1
order by 1
)
select 
year, Number_of_order,Number_of_item, sales, profit,
round(profit/lag (profit) over(order by year),2)*100 profit_growth
from
basic_year
order by 1



















