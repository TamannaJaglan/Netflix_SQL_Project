# Netflix Movies and TV Shows data analysis using SQL

![Netflix Logo](https://github.com/TamannaJaglan/Netflix_SQL_Project/blob/main/Netflix%20logo%20(1).jfif)
## Overview
This project involves a comprehensive analysis of Netfli's monies and TV shows data using SQL. Te goal is to extract valuable insights and answer various business questions based on their dataset. Te following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclutions.

##Schema
```sql
create table netflix
 (
 show_id varchar(20),
 type varchar(20),
 title varchar(150),
 director varchar(250),
 casts varchar(1000),
 country varchar(250),
 date_added	varchar(100),
 release_year int,
 rating varchar(100),
 duration varchar(150),
 listed_in varchar(100),
 description varchar(250)
 );
```

15 Business Problems & Solutions

-- 1. Count the number of Movies vs TV Shows

```sql
select type,
count(*) as total_content
from netflix
group by type;
```

-- 2. Find the most common rating for movies and TV shows

```sql
select type, rating
from 
(select type, rating, count(*),
rank() over(partition by type order by count(*) desc) as ranking 
from netflix
group by 1,2
order by 3 desc
) as t1
where ranking = 1;
```

3. List all movies released in a specific year (e.g., 2020)

```sql
select *
from netflix
where type = 'Movie'
and
release_year = 2000;
```

4. Find the top 5 countries with the most content on Netflix.

```sql
select unnest(string_to_array (country, ',')) as new_country,
count(show_id) as content 
from netflix
group by country
order by 2 desc
limit 5;
```


5. Identify the longest movie.

```sql
select *
from netflix
where type = 'Movie'
and 
duration = (select max(duration) from netflix);
```

6. Find content added in the last 5 years.

```sql
select *
from netflix  ---In this we use TO_DATE to convert the format of date into sort, and to get 5 years of data we use, Current_date - Interval '5 Years'
where 
TO_DATE(date_added, 'Month DD, YYYY') >= Current_date - Interval '5 Years'
```


7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

```sql
Select *
from netflix
where
director ILike '%Rajiv Chilaka%';-- here we are using Like to receive only Rajiv Chilaka and I is to ignore casesensitive and % is to check the existance of the writer.
```

8. List all TV shows with more than 5 seasons.

```sql
select * 
from netflix
Where type = 'TV Show'
and 
split_part(duration, ' ', 1) :: numeric > 5;
```

9. Count the number of content items in each genre.

```sql
Select 
Unnest(String_to_array(listed_in, ',')) as Genre,
count(show_id) as Total_Content
from netflix
group by listed_in
order by 2 desc;
```

10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

```sql
select 
Extract(Year from(To_date(date_added, 'Month DD, YYYY' ))) as year,
count(*) as content,
Round(
count(*) :: numeric/(select count(*) from netflix where country = 'India') :: numeric * 100 
,2) as avg_of_content
from netflix
where country = 'India'
group by 1
order by 2 desc
limit 6;
```

11. List all movies that are documentaries.

```sql
Select type,
listed_in,
*
from netflix
where
listed_in ILike '%Documentaries%'
and 
type = 'Movie';
```

12. Find all content without a director.

```sql
select * 
from netflix
where director is null;
```

13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

```sql
select * from netflix
where 
casts ILike '%Salman_Khan%'
and
release_year > Extract(year from current_date) - 10;
```

14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

```sql
Select 
unnest(string_to_array(casts, ',')) as actor,
Count(*) as content
from netflix
where country Ilike '%India%'
and 
type = 'Movie'
group by 1
order by 2 desc
limit 10;
```

select * from netflix;

```sql
15.Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
the description field. Label content containing these keywords as 'Bad' and all other 
content as 'Good'. Count how many items fall into each category.

with new_table
as
(
select *,
case
    when description ILike '%kill%'
    or 
    description ILike '%violence%' 
	then 'Bad_Content'
	else 'Good_content'
	end category
from netflix
)
select category,
count(*) as total_content 
from new_table
group by 1;
```

Thank You
