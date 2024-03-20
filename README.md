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
![alt text](https://masterful-media.com/wp-content/uploads/2024/03/doctors-table.png)

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
## Question 4:
All patients who have gone through admissions, can see their medical documents on our site. Those patients are given a temporary password after their first admission. Show the patient_id and temp_password.

The password must be the following, in order:

1. patient_id
2. the numerical length of patient’s last_name
3. year of patient’s birth_date


![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)
![alt text](https://masterful-media.com/wp-content/uploads/2024/02/admissions-table.png)

## Answer:
 
``` sql
SELECT patient_id,
CONCAT(patient_id, len(last_name), YEAR(birth_date)) as temp_password
FROM patients 
WHERE patient_id in (SELECT patient_id FROM admissions)

;
```
## Question 5:
Each admission costs $50 for patients without insurance, and $10 for patients with insurance. All patients with an even patient_id have insurance.

Give each patient a ‘Yes’ if they have insurance, and a ‘No’ if they don’t have insurance. Add up the admission_total cost for each has_insurance group.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/admissions-table.png)

## Answer:
 
``` sql
SELECT 
CASE WHEN patient_id % 2 = 0 then 'Yes'
  ELSE 'No' END AS has_insurance,
SUM(
  CASE WHEN patient_id % 2 = 0 then 10 
  ELSE 50 END) as cost_after_insurance
FROM admissions
group by has_insurance
;
/*
% is Modulus operator, when the remainder from division by two is zero, 
the number is even, else it's odd.
*/
```
## Question 6:
Show the provinces that has more patients identified as ‘M’ than ‘F’. Must only show full province_name

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)
![alt text](https://masterful-media.com/wp-content/uploads/2024/03/province-table.png)

## Answer:
 
``` sql
SELECT n.province_name 
from province_names n 
JOIN patients p ON n.province_id = p.province_id
GROUP BY province_name
HAVING
COUNT(CASE WHEN gender = 'M' THEN 1 END) > COUNT(CASE WHEN gender = 'F' THEN 1 END)
;
```
## Question 7:
We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:

* First_name contains an ‘r’ after the first two letters.
* Identifies their gender as ‘F’
* Born in February, May, or December
* Their weight would be between 60kg and 80kg
* Their patient_id is an odd number
* They are from the city ‘Kingston’


![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)

## Answer:
 
``` sql
SELECT *
FROM patients
WHERE SUBSTRING(first_name, 3 , 1) = 'r'
-- first_name LIKE '__r%' also works
AND gender = 'F'
AND MONTH(birth_date) IN (2,5,12)
AND weight between 60 and 80
and patient_id % 2 <> 0
And city = 'Kingston';
```
## Question 8:
Show the percent of patients that have ‘M’ as their gender. Round the answer to the nearest hundreth number and in percent form.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/patients-table.png)

## Answer:
 
``` sql
with t1 AS(
SELECT 
COUNT(*) as all_patients,
COUNT(CASE WHEN gender = 'M' THEN 1 END) AS male_patients
FROM patients)
SELECT 
CONCAT(
ROUND(
  SUM(
  cast(male_patients as float) -- Must use float
  /
  CAST(all_patients AS float)
)
  , 4)
* 100 ,'%')
as percent
FROM t1;
```
## Question 9:
For each day display the total amount of admissions on that day. Display the amount changed from the previous date.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/admissions-table.png)

## Answer:
 
``` sql
SELECT admission_date,
COUNT(patient_id) AS admissions_per_day,
    COUNT(patient_id) - LAG(COUNT(patient_id), 1) OVER (ORDER BY admission_date) AS change_from_previous_day
FROM admissions
group by admission_date
ORDER BY admission_date;
```
## Question 10:
Sort the province names in ascending order in such a way that the province ‘Ontario’ is always on top.

![alt text](https://masterful-media.com/wp-content/uploads/2024/03/province-table.png)

## Answer:
 
``` sql
SELECT province_name from
province_names
order by CASE WHEN province_name = 'Ontario' THEN 1 ELSE 2 END;
```
## Question 11:
We need a breakdown for the total amount of admissions each doctor has started each year. 
Show the doctor_id, doctor_full_name, specialty, year, total_admissions for that year.

![alt text](https://masterful-media.com/wp-content/uploads/2024/02/admissions-table.png)
![alt text](https://masterful-media.com/wp-content/uploads/2024/03/doctors-table.png)

## Answer:
 
``` sql
SELECT attending_doctor_id, 
d.first_name || ' ' || d.last_name as doctor_name, 
d.specialty,
YEAR(admission_date),
COUNT(patient_id) AS admissions_per_year
FROM admissions
join doctors d on attending_doctor_id = d.doctor_id
group by 
attending_doctor_id, YEAR(admission_date)
order by
attending_doctor_id
;
```
