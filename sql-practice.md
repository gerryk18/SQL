# sql-practice.com
## Question 1:
Show all of the patients grouped into weight groups.
Show the total amount of patients in each weight group.
Order the list by the weight group decending.
For example, if they weight 100 to 109 they are placed in the 100 weight group, 110-119 = 110 weight group, etc.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)

## Answer:
 
``` sql
SELECT count(patient_id) as patients_in_group,
FLOOR(weight / 10) * 10 AS weightgroup
/*
FLOOR(weight / 10) * 10 effectively groups weights into multiples of 10. 
This is often used to create ranges or bins for grouping numeric values, 
as it simplifies the process compared to manually defining each range using a CASE statement.
*/
FROM patients
group by weightgroup
order by weightgroup desc
;
```
