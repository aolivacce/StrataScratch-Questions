# StrataScratch Questions

[StrataScratch](https://www.stratascratch.com/) is a data science platform with real interview questions from top companies, including FAANG companies. I'll be solving a few of the SQL prompts below. 

### Level: Medium 

**Airbnb | Ranking Most Active Guests** <br>
[Question:](https://platform.stratascratch.com/coding/10159-ranking-most-active-guests?code_type=1) Rank guests based on the number of messages they've exchanged with the hosts. Guests with the same number of messages as other guests should have the same rank. Do not skip rankings if the preceding rankings are identical.
Output the rank, guest id, and number of total messages they've sent. Order by the highest number of total messages first.

```ruby
SELECT dense_rank() OVER (ORDER BY sum(n_messages) DESC) AS rank, 
    id_guest,
    sum(n_messages)
FROM airbnb_contacts
group by id_guest
order by rank asc;
```
Output: <br>
![image](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/d1a8746d-dc39-4066-a027-b5b040adafe6)

---

**Meta/Facebook | Highest Energy Consumption** <br>
[Question:](https://platform.stratascratch.com/coding/10064-highest-energy-consumption?code_type=1) Find the date with the highest total energy consumption from the Meta/Facebook data centers. Output the date along with the total energy consumption across all data centers.

```ruby
WITH centers AS (
  SELECT * FROM fb_eu_energy
  UNION ALL
  SELECT * FROM fb_asia_energy
  UNION ALL
  SELECT * FROM fb_na_energy
),
totals AS (
SELECT date, SUM(consumption) total_consumption
FROM centers
group by date
ORDER BY total_consumption DESC
)

  SELECT * FROM totals
  WHERE total_consumption = (SELECT MAX(total_consumption) FROM totals)

```
Output: <br>
![image](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/1e135be2-82b8-44dd-8d0d-5f459ec3fc4b)

---

**Salesforce | Highest Target Under Manager** <br>
[Question:](https://platform.stratascratch.com/coding/9905-highest-target-under-manager?code_type=1) Find the highest target achieved by the employee or employees who works under the manager id 13. Output the first name of the employee and target achieved. The solution should show the highest target achieved under manager_id=13 and which employee(s) achieved it.

```ruby
SELECT first_name, MAX(target)
FROM salesforce_employees
WHERE manager_id = 13 AND target IN (
  SELECT MAX(target) 
  FROM salesforce_employees
  WHERE manager_id = 13
)
GROUP BY first_name, target;
```
Output: <br>
![image](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/08fbe2f5-644a-48c5-838c-e30ff0e7605a)

---

**Amazon | Finding User Purchases** <br>
[Question:](https://platform.stratascratch.com/coding/10322-finding-user-purchases?code_type=1) Write a query that'll identify returning active users. A returning active user is a user that has made a second purchase within 7 days of any other of their purchases. Output a list of user_ids of these returning active users.

```ruby
select DISTINCT (a.user_id)
from amazon_transactions a
JOIN amazon_transactions b 
on a.user_id = b.user_id
WHERE a.created_at - b.created_at between 0 and 7
AND a.id != b.id;
```
Output: <br>
![image](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/57cbc7f9-0052-49dd-a7df-a5dcd4e35da5)

---

**Meta/Facebook | Acceptance Rate By Date** <br>
[Question:](https://platform.stratascratch.com/coding/10285-acceptance-rate-by-date?code_type=1)
What is the overall friend acceptance rate by date? Your output should have the rate of acceptances by the date the request was sent. Order by the earliest date to latest.


Assume that each friend request starts by a user sending (i.e., user_id_sender) a friend request to another user (i.e., user_id_receiver) that's logged in the table with action = 'sent'. If the request is accepted, the table logs action = 'accepted'. If the request is not accepted, no record of action = 'accepted' is logged.


```ruby
WITH rate AS (
    SELECT date,
        action,
        LEAD(action) over (PARTITION BY user_id_sender, user_id_receiver ORDER BY date) AS done
    FROM fb_friend_requests
)
SELECT date,
    COUNT(done) * 1.0 / COUNT(action) AS percent_acceptance
FROM rate
WHERE action = 'sent'
GROUP BY date 
```

Output:<br>

![image](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/40794b16-1cad-44f6-9bf8-3f0768dae93e)
---




