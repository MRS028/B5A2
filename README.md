**১) PostgreSQL কী?**
PostgreSQL একটি ওপেন-সোর্স রিলেশনাল ডেটাবেস ম্যানেজমেন্ট সিস্টেম (RDBMS) যা ACID (Atomicity, Consistency, Isolation, Durability) নীতি অনুসরণ করে। এটি জটিল কুয়েরি, ট্রানজেকশন, মাল্টি-ভার্সন কনকারেন্সি কন্ট্রোল (MVCC) এবং কাস্টম এক্সটেনশন সমর্থন করে। PostgreSQL JSONB ফরম্যাটে JSON ডেটা সংরক্ষণ, ইনডেক্সিং, ভিউ, স্টোরড প্রসিডিউর, রোল-ভিত্তিক অ্যাক্সেস কন্ট্রোল এবং রিপ্লিকেশন সাপোর্ট দেয়।
**কোথায় ব্যবহার হয়:** ব্যাংকিং, স্বাস্থ্যসেবা, ই-কমার্স, GIS, ডেটা অ্যানালিটিক্স ইত্যাদি যেখানে ডেটার নির্ভুলতা ও নিরাপত্তা জরুরি।
**উদাহরণ:**

```sql
CREATE DATABASE mydb;
CREATE TABLE students (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  admitted DATE
);
```

---

**২) PostgreSQL-এ database schema কেন প্রয়োজন?**
Schema হলো একটি লজিক্যাল কন্টেইনার যা টেবিল, ভিউ, ফাংশন ইত্যাদি সংগঠিত করে রাখে। এটি ডেটাবেসে আলাদা namespace তৈরি করে, যাতে একই নামে টেবিল ভিন্ন স্কিমায় রাখা যায় এবং প্রজেক্ট বা ইউজার-ভিত্তিক আলাদা ডেটা ম্যানেজ করা সহজ হয়।
**সুবিধা:**

* বড় প্রজেক্টে ডেটা আলাদা ভাগে সংগঠিত রাখা।
* আলাদা ইউজারের জন্য পারমিশন নিয়ন্ত্রণ।
* নামের দ্বন্দ্ব এড়ানো।
  **উদাহরণ:**

```sql
CREATE SCHEMA research AUTHORIZATION db_user;
CREATE TABLE research.animals (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100)
);
```

---

**৩) Primary Key ও Foreign Key ব্যাখ্যা**

* **Primary Key (PK):** টেবিলের একটি বা একাধিক কলাম যা প্রতিটি সারির জন্য ইউনিক ও NULL নয়। এটি প্রতিটি রেকর্ডকে সুনির্দিষ্টভাবে চিহ্নিত করে।
* **Foreign Key (FK):** একটি কলাম যা অন্য টেবিলের Primary Key কে রেফার করে। এটি রিলেশনাল ইন্টিগ্রিটি বজায় রাখে।
  **উদাহরণ:**

```sql
CREATE TABLE departments (
  dept_id SERIAL PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE employees (
  emp_id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  dept_id INT REFERENCES departments(dept_id)
);
```

এখানে `employees.dept_id` হলো Foreign Key যা `departments.dept_id` এর সাথে যুক্ত।

---

**৪) VARCHAR ও CHAR এর পার্থক্য**

* **CHAR(n):** নির্দিষ্ট দৈর্ঘ্যের স্ট্রিং সংরক্ষণ করে। n এর চেয়ে ছোট হলে বাকিটা space দিয়ে পূরণ হয়।
* **VARCHAR(n):** পরিবর্তনশীল দৈর্ঘ্যের স্ট্রিং সংরক্ষণ করে এবং যতটুকু দরকার ততটুকু জায়গা নেয়।
  **উদাহরণ:**

```sql
CREATE TABLE demo (
  code CHAR(5),
  title VARCHAR(50)
);
```

প্র্যাকটিক্যালি `VARCHAR` বেশি ব্যবহার হয় কারণ এটি জায়গা বাঁচায় ও লচিকতা দেয়।

---

**৫) SELECT স্টেটমেন্টে WHERE ক্লজের উদ্দেশ্য**
`WHERE` ক্লজ শর্ত দিয়ে ডেটা ফিল্টার করতে ব্যবহৃত হয়, যাতে শুধুমাত্র প্রয়োজনীয় রেকর্ডগুলো পাওয়া যায়।
**উদাহরণ:**

```sql
SELECT * FROM employees
WHERE salary > 50000 AND department_id = 2;
```

এতে শুধু সেই কর্মচারীদের রেকর্ড আসবে যাদের বেতন ৫০,০০০ এর বেশি এবং বিভাগ ২ এ রয়েছে।

---

**৬) LIMIT ও OFFSET এর ব্যবহার**
`LIMIT` দিয়ে কতোটি রেকর্ড ফেরত দিতে হবে নির্ধারণ হয় এবং `OFFSET` দিয়ে কোন নম্বর রো থেকে শুরু হবে তা নির্ধারণ হয়।
**উদাহরণ:**

```sql
SELECT * FROM employees
ORDER BY id
LIMIT 10 OFFSET 20;
```

এতে ২১ নম্বর রেকর্ড থেকে পরবর্তী ১০টি রেকর্ড পাওয়া যাবে। এটি pagination এর জন্য খুব দরকারী।

---

**৭) UPDATE দিয়ে ডেটা পরিবর্তন**
`UPDATE` স্টেটমেন্ট বিদ্যমান রেকর্ড পরিবর্তনের জন্য ব্যবহৃত হয়।
**উদাহরণ:**

```sql
UPDATE employees
SET salary = salary * 1.1
WHERE performance_score > 90
RETURNING id, salary;
```

এটি ৯০ এর বেশি স্কোর থাকা কর্মচারীদের বেতন ১০% বাড়াবে এবং পরিবর্তিত ডেটা দেখাবে।

---

**৮) JOIN এর গুরুত্ব ও কাজ**
`JOIN` দিয়ে একাধিক টেবিল থেকে সম্পর্কযুক্ত ডেটা একসাথে আনা হয়।
**উদাহরণ:**

```sql
SELECT e.name, d.name AS department
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
```

এটি `employees` ও `departments` টেবিল যুক্ত করে কর্মচারী ও বিভাগের নাম একসাথে দেখায়।

---

**৯) GROUP BY ও অ্যাগ্রিগেশন**
`GROUP BY` দিয়ে একই ধরনের মান থাকা রেকর্ডগুলো গ্রুপ করা হয়, যাতে প্রতিটি গ্রুপে aggregate ফাংশন প্রয়োগ করা যায়।
**উদাহরণ:**

```sql
SELECT department_id, COUNT(*) AS total_emp
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

এটি ৫ জনের বেশি কর্মচারী থাকা বিভাগগুলো দেখাবে।

---

**১০) COUNT(), SUM(), AVG() ব্যবহার**
এগুলো aggregate ফাংশন যা ডেটার উপর গণনা করে।
**উদাহরণ:**

```sql
SELECT COUNT(*) AS total_emp,
       SUM(salary) AS total_salary,
       AVG(salary) AS avg_salary
FROM employees;
```

এতে কর্মচারীর সংখ্যা, মোট বেতন এবং গড় বেতন বের হবে।

---
