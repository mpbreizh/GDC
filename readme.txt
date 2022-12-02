# Data Engineer/Analyst Test - mpbreizh - 02/12/2022

Hello Gens de Confiance's team !

# 1. Data engineering
First of all, I download the 4 CSV table (ads, ads_transaction, users and referrals).
I use Python / Jupyter Notebook to the data engineering part.
I made some data quality to clean some features, modalities in each table.

I deleted some index variables.
I changed some modalities ,for exemple F1 to T1 in the title feature, only one realestate modality or gender modality.
I created news features like the title_type, the created_year.

Please find the differents quality steps in the .ipynb file in my GitHub.

# 2. Data analyst

I switch on SQL (GCP) to make some analysis from the data.

I analyse the buyer rate on differents axes : year, title type, category, title.

And then the average sold price, the sold price rate (sold price / price ads).

Also, do we have for each referrer a minimum of 3 referree to consider them as GDC members.

# 3. Next Steps

It would be interesting to made a Customer profil using the Age, Gender, Region.
Also use the different UTM code to check which source our members are coming from.
Made a dashboard for the differents evolutions relating to numbers of ads, per category.

# 3. SQL Request

Here my SQL request use to produce the Report Excel File.

/*Buyer rate per category*/
select distinct 
	 case when a.FLG_LOCATION="True" then "Location"
	     when a.FLG_VENTE="True" then "Vente"
	     when a.FLG_RECHERCHE="True" then "Recherche" end as title_type
	,a.category
	,a.created_year
	,count(distinct id) as nb_ads
from work.ads a
group by 1,2,3


/*Buyer rate per category*/
select distinct a.category
	,a.created_year
	,sum(case when b.ad_owner_id is not null then 1 else 0 end) as nb_buyer
	,count(distinct owner_id) as nb_owner_id
	,sum(case when b.ad_owner_id is not null then 1 else 0 end)/count(distinct owner_id) as rate_buyer format percent7.1
from work.ads a
left join work.ads_transaction b on a.owner_id=b.ad_owner_id and a.id=b.ad_id
group by 1,2

/*Buyer rate per title*/
select distinct a.title
	,a.created_year
	,sum(case when b.ad_owner_id is not null then 1 else 0 end) as nb_buyer
	,count(distinct owner_id) as nb_owner_id
	,sum(case when b.ad_owner_id is not null then 1 else 0 end)/count(distinct owner_id) as rate_buyer format percent7.1
from work.ads a
left join work.ads_transaction b on a.owner_id=b.ad_owner_id and a.id=b.ad_id
group by 1,2

/*Buyer rate per type*/
select distinct 
	case when a.FLG_LOCATION="True" then "Location"
	     when a.FLG_VENTE="True" then "Vente"
	     when a.FLG_RECHERCHE="True" then "Recherche" end as title_type
	,a.category
	,a.created_year
	,sum(case when b.ad_owner_id is not null then 1 else 0 end) as nb_buyer
	,count(distinct owner_id) as nb_owner_id
	,sum(case when b.ad_owner_id is not null then 1 else 0 end)/count(distinct owner_id) as rate_buyer format percent7.1
from work.ads a
left join work.ads_transaction b on a.owner_id=b.ad_owner_id and a.id=b.ad_id
group by 1,2,3

/*Sold Rate Price*/
select distinct
	case when a.FLG_LOCATION="True" then "Location"
	     when a.FLG_VENTE="True" then "Vente"
	     when a.FLG_RECHERCHE="True" then "Recherche" end as title_type
	,a.category 
	,a.created_year
	,mean(price) as avg_sold_price format 7.
	,mean(b.sold_price/a.price) as sold_rate_price format percent7.1
from work.ads a
left join work.ads_transaction b on a.owner_id=b.ad_owner_id and a.id=b.ad_id
group by 1,2,3

/*Number of non-members ? referree with less than 3 referrer*/
select distinct referrer_user_id
	,count(distinct referree_user_id) as nb_referree
from WORK.REFERRALS
group by 1
having nb_referree<3;















