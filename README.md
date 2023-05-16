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
![173831EC-8353-4934-AE2D-A5A4ED8D1FF6_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/419e0413-cec2-4bb0-8958-22e576100d21)

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
![528C4B90-07BF-46E1-8657-9D2F356FFCA8_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/bebe4c60-57bb-470e-ba1d-dbdc21c7cb6a)

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
![23652726-A957-4A12-B80D-72E49FA908CB_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/684d8cf6-ec4d-400a-81d0-0555d2abe833)

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
![1B7F5939-DB67-4C63-BBFF-82303C9CAB1C_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/a573d6d7-98be-4c2e-b628-ab72a6138a34)

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

![87F51D87-B74C-4422-9422-0EF1A109F996_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/28887cf9-76c9-4147-ab4a-d4e678254166)

### Level: Hard 
**Yelp | Top 5 States With 5 Star Businesses** <br>
[Question: ](https://platform.stratascratch.com/coding/10046-top-5-states-with-5-star-businesses?code_type=1)Find the top 5 states with the most 5 star businesses. Output the state name along with the number of 5-star businesses and order records by the number of 5-star businesses in descending order. In case there are ties in the number of businesses, return all the unique states. If two states have the same result, sort them in alphabetical order.

```ruby
SELECT DISTINCT state, business_count
FROM (
    SELECT state, COUNT(business_id) AS business_count, RANK() OVER (ORDER BY COUNT(business_id) DESC) AS rank
    FROM yelp_business
    WHERE stars = 5
    GROUP BY state
) AS ranked
WHERE rank <= 5
ORDER BY business_count DESC;
```
Output: <br>
![1B7F5939-DB67-4C63-BBFF-82303C9CAB1C_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/b2b1fe75-2ef6-45ac-af46-90d440fefa22)

---

**Microsoft | Premium vs Freemium** <br>
[Question:](https://platform.stratascratch.com/coding/10300-premium-vs-freemium?code_type=1) Find the total number of downloads for paying and non-paying users by date. Include only records where non-paying customers have more downloads than paying customers. The output should be sorted by earliest date first and contain 3 columns date, non-paying downloads, paying downloads.

```ruby
SELECT dldata.date,
SUM(CASE WHEN paying_customer = 'no' THEN downloads ELSE 0 END) AS freemium,
SUM(CASE WHEN paying_customer = 'yes' THEN downloads ELSE 0 END) AS premium
FROM ms_user_dimension AS u
JOIN ms_acc_dimension AS acct ON u.acc_id = acct.acc_id
JOIN ms_download_facts AS dldata ON u.user_id = dldata.user_id
GROUP BY 1
having sum(case when paying_customer = 'no' then downloads else 0 end ) > sum(case when paying_customer = 'yes' then downloads else 0 end )
ORDER BY 1;
```

Output:<br>
![1B7F5939-DB67-4C63-BBFF-82303C9CAB1C_4_5005_c](https://github.com/aolivacce/StrataScratch-Questions/assets/72052149/c9bfecb2-373e-400b-92d7-e16ff04a974a)














