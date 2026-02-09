# Stock_Dashboard_View
create or replace view view_production_stock as
select a.entity_code,a.div_code,a.item_code, m.item_name,m.item_nature,m.item_catg,m.item_group,m.item_class,m.parent_code,
item_size,m.um,
  sum(opqty) opqty
, sum(dqtyrecd1) dqtyrecd1, sum(dqtyrecd2) dqtyrecd2,sum(dqtyissued1)dqtyissued1, sum(dqtyissued2) dqtyissued2
, sum(mqtyrecd1) mqtyrecd1, sum(mqtyrecd2)mqtyrecd2,sum(mqtyissued1) mqtyissued1, sum(mqtyissued2)mqtyissued2
, sum(yqtyrecd1) yqtyrecd1, sum(yqtyrecd2)yqtyrecd2,sum(yqtyissued1) yqtyissued1, sum(yqtyissued2)yqtyissued2
, nvl(sum(opqty),0)+ nvl(sum(yqtyrecd1),0)+nvl(sum(yqtyrecd2),0)-nvl(sum(yqtyissued1),0)-nvl(sum(yqtyissued2),0) clqty
,  sum(opval) opval
, sum(dvalrecd1) dvalrecd1, sum(dvalrecd2) dvalrecd2,sum(dvalissued1)dvalissued1, sum(dvalissued2) dvalissued2
, sum(mvalrecd1) mvalrecd1, sum(mvalrecd2)mvalrecd2,sum(mvalissued1) mvalissued1, sum(mvalissued2)mvalissued2
, sum(yvalrecd1) yvalrecd1, sum(yvalrecd2)yvalrecd2,sum(yvalissued1) yvalissued1, sum(yvalissued2)yvalissued2
, nvl(sum(opval),0)+ nvl(sum(yvalrecd1),0)+nvl(sum(yvalrecd2),0)-nvl(sum(yvalissued1),0)-nvl(sum(yvalissued2),0) clval
from  (select  entity_code,
               div_code,
               item_code,
               nvl(qtyrecd, 0) - nvl(qtyissued, 0) opqty,
               0 dqtyrecd1,
               0 dqtyrecd2,
               0 dqtyissued1,
               0 dqtyissued2,
               0 mqtyrecd1,
               0 mqtyrecd2,
               0 mqtyissued1,
               0 mqtyissued2,
               0 yqtyrecd1,
               0 yqtyrecd2,
               0 yqtyissued1,
               0 yqtyissued2,
               nvl(valrecd, 0) - nvl(valissued, 0) opval,
               0 dvalrecd1,
               0 dvalrecd2,
               0 dvalissued1,
               0 dvalissued2,
               0 mvalrecd1,
               0 mvalrecd2,
               0 mvalissued1,
               0 mvalissued2,
               0 yvalrecd1,
               0 yvalrecd2,
               0 yvalissued1,
               0 yvalissued2
from   view_item_ledger
where  trunc(vrdate) < lhs_utility.get_from_date
union all
select entity_code,
       div_code,
       item_code,
       0 opqty,
       decode(trunc(vrdate), lhs_utility.get_to_date, qtyrecd, 0) dqtyrecd1,
       0 dqtyrecd2,
       0 dqtyissued1,
       0 dqtyissued2,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),qtyrecd,0) mqtyrecd1,
       0 mqtyrecd2,
       0 mqtyissued1,
       0 mqtyissued2,
       qtyrecd yqtyrecd1,
       0 yqtyrecd2,
       0 yqtyissued1,
       0 yqtyissued2,
       0 opval,
       decode(trunc(vrdate), lhs_utility.get_to_date, valrecd, 0) dvalrecd1,
       0 dvalrecd2,
       0 dvalissued1,
       0 dvalissued2,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),valrecd,0) mvalrecd1,
       0 mvalrecd2,
       0 mvalissued1,
       0 mvalissued2,
       valrecd yvalrecd1,
       0 yvalrecd2,
       0 yvalissued1,
       0 yvalissued2
from   view_item_ledger
where  trunc(vrdate) between lhs_utility.get_from_date and lhs_utility.get_to_date
and    tnature in ('GRNI', 'PROD')
and    qtyrecd > 0
union all
select entity_code,
       div_code,
       item_code,
       0 opqty,
       0 dqtyrecd1,
       decode(vrdate, lhs_utility.get_to_date, qtyrecd, 0) dqtyrecd2,
       0 dqtyissued1,
       0 dqtyissued2,
       0 mqtyrecd1,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),qtyrecd,0) mqtyrecd2,
       0 mqtyissued1,
       0 mqtyissued2,
       0 yqtyrecd1,
       qtyrecd yqtyrecd2,
       0 yqtyissued1,
       0 yqtyissued2,
       0 opval,
       0 dvalrecd1,
       decode(vrdate, lhs_utility.get_to_date, valrecd, 0) dvalrecd2,
       0 dvalissued1,
       0 dvalissued2,
       0 mvalrecd1,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),valrecd,0) mvalrecd2,
       0 mvalissued1,
       0 mvalissued2,
       0 yvalrecd1,
       valrecd yvalrecd2,
       0 yvalissued1,
       0 yvalissued2
from   view_item_ledger
where  trunc(vrdate) between lhs_utility.get_from_date and lhs_utility.get_to_date
and    tnature not in ('GRNI', 'PROD')
and    qtyrecd > 0
union all
select entity_code,
       div_code,
       item_code,
       0 opqty,
       0 dqtyrecd1,
       0 dqtyrecd2,
       decode(vrdate, lhs_utility.get_to_date, qtyissued, 0) dqtyissued1,
       0 dqtyissued2,
       0 mqtyrecd1,
       0 mqtyrecd2,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),qtyissued,0) mqtyissued1,
       0 mqtyissued2,
       0 yqtyrecd1,
       0 yqtyrecd2,
       qtyissued yqtyissued1,
       0 yqtyissued2,
       0 opval,
       0 dvalrecd1,
       0 dvalrecd2,
       decode(vrdate, lhs_utility.get_to_date, valissued, 0) dvalissued1,
       0 dvalissued2,
       0 mvalrecd1,
       0 mvalrecd2,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),valissued,0) mvalissued1,
       0 mvalissued2,
       0 yvalrecd1,
       0 yvalrecd2,
       valissued yvalissued1,
       0 yvalissued2

from   view_item_ledger
where  trunc(vrdate) between lhs_utility.get_from_date and lhs_utility.get_to_date
and    tnature in ('ISSI', 'PROD', 'SALE')
and    qtyissued > 0
union all
select entity_code,
       div_code,
       item_code,
       0 opqty,
       0 dqtyrecd1,
       0 dqtyrecd2,
       0 dqtyissued1,
       decode(vrdate, lhs_utility.get_to_date, qtyissued, 0) dqtyissued2,
       0 mqtyrecd1,
       0 mqtyrecd2,
       0 mqtyissued1,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),qtyissued,0) mqtyissued2,
       0 yqtyrecd1,
       0 yqtyrecd2,
       0 yqtyissued1,
       qtyissued yqtyissued2,
       0 opval,
       0 dvalrecd1,
       0 dvalrecd2,
       0 dvalissued1,
       decode(vrdate, lhs_utility.get_to_date, valissued, 0) dvalissued2,
       0 mvalrecd1,
       0 mvalrecd2,
       0 mvalissued1,
       decode(to_char(vrdate, 'MON-RRRR'),to_char(lhs_utility.get_to_date, 'MON-RRRR'),valissued,0) mvalissued2,
       0 yvalrecd1,
       0 yvalrecd2,
       0 yvalissued1,
       valissued yvalissued2
from   view_item_ledger
where  trunc(vrdate) between lhs_utility.get_from_date and lhs_utility.get_to_date
and    tnature not in ('ISSI', 'PROD', 'SALE')
and    qtyissued > 0
)a,    item_mast m
where  a.item_code=m.item_code
group by a.entity_code,a.div_code,a.item_code, m.item_name,m.um,item_size,m.item_nature,m.item_catg,m.item_group,m.item_class,m.parent_code;
