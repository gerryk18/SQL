# SQL
This is a simple project where I look for SQL problems online from various websites and solve them.

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
## Question 2:
Show patient_id, weight, height, isObese from the patients table.

Display isObese as a boolean 0 or 1.

Obese is defined as weight(kg)/(height(m)2) >= 30.

weight is in units kg.

height is in units cm.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)

## Answer:
 
``` sql
SELECT patient_id, weight, height,
(CASE WHEN weight / (power(height/100.0, 2)) >=30 then 
1 
else 
0 END) as isObese
FROM patients
;
```
## Question 3:
Show patient_id, first_name, last_name, and attending doctor’s specialty.
Show only the patients who has a diagnosis as ‘Epilepsy’ and the doctor’s first name is ‘Lisa’

Check patients, admissions, and doctors tables for required information.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)
![alt text](https://masterful-media.com/wp-content/uploads/2024/02/admissions-table.png)
![alt text](https://masterful-media.com/wp-content/uploads/2024/02/doctors-table.png)

## Answer:
 
``` sql
SELECT p.patient_id, p.first_name, p.last_name, d.specialty
FROM patients p
JOIN admissions a on p.patient_id = a.patient_id
JOIN doctors d ON a.attending_doctor_id = d.doctor_id

WHERE a.diagnosis = 'Epilepsy'
AND d.first_name = 'Lisa'
;
```
