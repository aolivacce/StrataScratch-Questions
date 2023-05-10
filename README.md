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



