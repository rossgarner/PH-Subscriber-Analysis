# PH-Subscriber-Analysis

Following on from our TAM Analysis of the Smart subscribers, I want to ask the question if the users Smart is acquiring for NBA League Pass are new, or if they are repeat customers, or if they are somewhat cannibalizing NBA League Pass direct signups

To start we use the SQL query in the attached file

SELECT FAN_ID, LPSUBSFLAG,ORDER_FISCAL_YEAR, ORDER_DATE_EST, ACCOUNT_CREATED_DATE_EST, (ORDER_DATE_EST = ACCOUNT_CREATED_DATE_EST) AS dates_match
FROM DB_DBTDEV.DBT_LKHATRI.SUBSCRIPTION__FCT_ORDERS_BY_OPIN_PARTNERS
WHERE COUNTRY_NAME = 'PHILIPPINES' AND OPIN_PARTNER = 'SMART'
GROUP BY FAN_ID, LPSUBSFLAG, ORDER_FISCAL_YEAR, ORDER_DATE_EST, ACCOUNT_CREATED_DATE_EST
ORDER BY ORDER_FISCAL_YEAR DESC

This allows us to pull the PLSUBSFLAG which determines if the user was a prior paying subscriber, a new subscriber, or a promo code winback subsriber - and also the relevant dates of when they ordered, when they created their account and if those two column dates match

If a user created an account on the same day they "ordered" NBA League Pass, logic dictates they would be assigned a new subscriber flag on LPSUBSFLAG

Here's a preview of the results set

<img width="1257" height="175" alt="image" src="https://github.com/user-attachments/assets/b3e9f7ca-27b6-4ff6-ac98-02187c015cf7" />


Next with the data exported, we shall continue our analysis in Pyhton to see what we can determine

