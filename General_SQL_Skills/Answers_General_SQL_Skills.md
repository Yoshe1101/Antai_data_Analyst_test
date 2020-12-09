
# Questions - GEneral SQL Skills

## -Describe, using SQL statements, how you would transform ​Table A ​into ​Table B.

If we wanna group the amounts by months first we would need to get from the column 'order_date' that is in 'datetime' format the value month, on SQLite we can do it with the following command 'strftime('%m', order_date)' and we call it month as it appears in the table B, in order to obtain the sum of each column of the Table B, [Ipad_pro_total, Iphone_Xs_total, other_total],  we use the command SUM() with the variable 'total_amount' of the Table A and we filter by the name (e.g. SUM(total_amount) as Ipad_pro_total WHERE product_name == 'Ipad pro'ORDER BY month) and we obtain the sum of the 'total_amount' for that product. We can se an example how we would build each column separately:

    SELECT strftime('%m', order_date) AS month , 
           SUM(total_amount) as Ipad_pro_total  
           FROM sales WHERE product_name == 'Ipad pro' 
           GROUP BY month;

    SELECT strftime('%m', order_date) AS month , 
           SUM(total_amount) as Iphone_Xs_total  
           FROM sales WHERE product_name == 'Iphone Xs' 
           GROUP BY month;

    SELECT strftime('%m', order_date) AS month ,
           SUM(total_amount) as Other_total
           FROM sales WHERE (product_name != 'Iphone Xs') AND (product_name != 'Ipad pro') 
           GROUP BY month;
           
![Ipad_pro](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/1.png) ![Iphone_Xs_total](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/2.png) ![Other_total](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/3.png)

To get the table B from this queries we can perform a left join function and replacing the 'null' values by 0 <br/> (Note: in the next steps we show how to create this TABLE B from TABLE A more efficiently with one statement)

    select A.month, COALESCE (C.Ipad_pro_total, 0 ) AS Ipad_pro_total,
           COALESCE (B.Iphone_Xs_total, 0 ) AS Iphone_xs_total , 
           A.Other_total  
           from (SELECT strftime('%m', order_date) AS month , 
                                                   SUM(total_amount) as Other_total  
                                                   FROM sales WHERE (product_name != 'Iphone Xs') AND (product_name != 'Ipad pro') GROUP BY month) AS A 

           left join (SELECT strftime('%m', order_date) AS month , 
                                                        SUM(total_amount) as Iphone_Xa_total  
                                                        FROM sales WHERE product_name == 'Iphone Xs' ORDER BY month)AS B on A.month = B.month 
    
           left join (SELECT strftime('%m', order_date) AS month , 
                                                        SUM(total_amount) as Ipad_pro_total  
                                                        FROM sales WHERE product_name == 'Ipad pro' ORDER BY month) AS C on C.month = A.month

![Table_B_JOIN](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/4.png)



### -Would you use materialized tables or SQL Views?

In this particualr case witht the data we already have I think it would make more sense to use SQL views due to does not look that we are dealing with large amounts of data and if we add or modify some row of the original dataset the View would be updated automatically and we would not need extra storage to save this queary.

But if in the future we would like to use this query for other pruposes and we need instant acces to this data and we want to perform more heavy computational transformations or processes or the size of the original dataset increases substantially we could use a materialized table but but taking into account that it will have to be updated manually periodically and keep in mind that we would have to store it somewhere.


### -Is it possible to do this transformation using a single SQL statement?

Yes, it is posible using the CASE() function (in SQLite) (or e.g. the IF() function in MySQL and similar in other systems). 
The statemntent:

    SELECT
        strftime('%m', order_date) AS month,
        SUM(CASE WHEN product_name = 'Ipad pro' THEN total_amount ELSE 0 END) AS Ipad_pro_total,
        SUM(CASE WHEN product_name = 'Iphone Xs' THEN total_amount ELSE 0 END) AS Iphone_Xs_total,
        SUM(CASE WHEN product_name NOT IN ('Iphone Xs', 'Ipad pro' ) THEN total_amount ELSE 0 END) AS other_total
    FROM sales
    GROUP BY month
    ORDER BY month;
    
![Table_B_CASE](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/5.png)


### -How would the transformation change if we wanted to group the sales by week number? 

In the function we use ti obtain the month we change the variable '%m' by the '%W' abtainig then the week of the year (strftime('%m', order_date)).
E.g:

    SELECT
        strftime('%W', order_date) AS week,
        SUM(CASE WHEN product_name = 'Ipad pro' THEN total_amount ELSE 0 END) AS Ipad_pro_total,
        SUM(CASE WHEN product_name = 'Iphone Xs' THEN total_amount ELSE 0 END) AS Iphone_xs_total,
        SUM(CASE WHEN product_name NOT IN ('Iphone Xs', 'Ipad pro' ) THEN total_amount ELSE 0 END) AS other_total
    FROM sales
    GROUP BY week
    ORDER BY week;
![Table_week](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/6.png)

### -And using the name of the month (ie. July instead of the number of the month 07 ) ?

In systems list MySQL exist functions like 'MONTHNAME()' which you can get the name of the month directly from a 'Datatime' variable.
In our case SQLite does not have this function but we can manage to transform our own one with the CASE() function used previously.
Then we have the following result:

    SELECT
        case strftime('%m', order_date) when '01' then 'January' when '02' then 'Febuary' when '03' then 'March' when '04' then 'April' when '05' then 'May' when         '06' then 'June' when '07' then 'July' when '08' then 'August' when '09' then 'September' when '10' then 'October' when '11' then 'November' when '12'             then 'December' else '' end AS month,
    SUM(CASE WHEN product_name == 'Ipad pro' THEN total_amount ELSE 0 END) AS Ipad_pro_total,
    SUM(CASE WHEN product_name == 'Iphone Xs' THEN total_amount ELSE 0 END) AS Iphone_xs_total,
    SUM(CASE WHEN product_name NOT IN ('Iphone Xs', 'Ipad pro' ) THEN total_amount ELSE 0 END) AS other_total
    FROM sales
    GROUP BY month
    ORDER BY month DESC;
    
![Table_month](https://github.com/Yoshe1101/Antai_data_Analyst_test/blob/master/General_SQL_Skills/img/7.png)


