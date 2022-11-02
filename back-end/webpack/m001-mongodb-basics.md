# M001 : MongoDB Basics

## Chapter 1: What is MongoDB

### Lab2 : Connect to your Atlas Cluster

1. atlas에 database 계정 생성 (<mark style="color:red;">**`username: m001-student`**</mark>`,` <mark style="color:red;">**`password: m001-mongodb-basic`**</mark>)
2. mongo shell로 mongo 클러스터 접속

```shell
mongo "mongodb+srv://sandbox.vlwl8hn.mongodb.net/admin" --username m001-student --password m001-mongodb-basic
```



## Chapter 4: Advanced CRUD Operations

### Lab 1: Comparison Operators

<details>

<summary>Problem</summary>

To complete this exercise connect to your Atlas cluster using the in-browser IDE space at the end of this chapter.

How many documents in the sample\_training.zips collection have fewer than 1000 people listed in the pop field?

Copy/paste the exact numeric value (without double quotes) of the result that you get into the response field.

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**8065**</mark>

To get this value use the following query:

```javascript
db.zips.find({ "pop": { "$lt": 1000 }}).count()
```

Use the $lt instead of $lte operator to exclude all documents that have exactly 1000 people listed in the pop field.

</details>



### Lab 2: Comparison Operators

<details>

<summary>Problem</summary>

To complete this exercise connect to your Atlas cluster using the in-browser IDE space at the end of this chapter.

What is the difference between the number of people born in 1998 and the number of people born after 1998 in the sample\_training.trips collection?

Enter the exact numeric value of the result that you get into the response field.

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**6**</mark>

You can use the following queries to get this value:

```javascript
db.trips.find({ "birth year": { "$gt": 1998 }}).count()
db.trips.find({ "birth year": 1998 }).count()
```

Use the $gt instead of $gte operator to exclude all 1998 births, and then see how many people were born in 1998 by using implicit equality, then subtract the two values to get the difference.

</details>



### Lab 3: Comparison Operators

<details>

<summary>Problem</summary>

Problem:

To complete this exercise connect to your Atlas cluster using the in-browser IDE space at the end of this chapter.

Using the sample\_training.routes collection find out which of the following statements will return all routes that have at least one stop in them?

**Attempts Remaining:**Correct Answer

Check all answers that apply:

* **`db.routes.find({ "stops": { "$gte": 0 }}).pretty()`**

<!---->

* **`db.routes.find({ "stops": { "$lt": 0 }}).pretty()`**

<!---->

* **`db.routes.find({ "stops": { "$ne": 0 }}).pretty()`**

<!---->

* **`db.routes.find({ "stops": { "$gt": 0 }}).pretty()`**

</details>

<details>

<summary>Explanation</summary>

* <mark style="color:green;">**`db.routes.find({ "stops": { "$gt": 0 }}).pretty()`**</mark>

This is **correct**.

The given query looks for strict equality where the stops field has to be greater than zero, thus excluding all zero stops.

* <mark style="color:green;">**`db.routes.find({ "stops": { "$ne": 0 }}).pretty()`**</mark>

This is **correct**.

This query will also work, given that there are no non-negative or non- numeric values in this collection. It returns all documents where the stops field is not equal to 0.

* **`db.routes.find({ "stops": { "$gte": 0 }}).pretty()`**

This is **incorrect**.

Using greater than or equal to operator will include documents where stops equal to zero making the result set invalid for this question.

* **`db.routes.find({ "stops": { "$lt": 0 }}).pretty()`**

This is **incorrect**.

There is no way to have less than zero stops for a flight, so this query yields zero results and asks for impossible information.

</details>



### Lab 4: Logic Operators

<details>

<summary>Problem</summary>

To complete this exercise connect to your Atlas cluster using the in-browser IDE space at the end of this chapter.

Before solving this exercise, make sure to undo some of the changes that we made to the zips collection earlier in the course by running the following command:

```javascript
db.zips.updateMany({ "city": "HUDSON" }, { "$inc": { "pop": -10 } })
```

How many zips in the sample\_training.zips dataset are neither over-populated nor under-populated?

In this case, we consider population of more than 1,000,000 to be over- populated and less than 5,000 to be under-populated.

Copy/paste the exact numeric value (without double quotes) of the result that you get into the response field.

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**11193**</mark>

To get this value use the following range query:

```javascript
db.zips.find({ "pop": { "$gte": 5000, "$lte": 1000000 }}).count()
```

You could also use the $nor operator if you want to use a logic operator like so:

```javascript
db.zips.find({ "$nor": [ { "pop": { "$lt":5000 } },
             { "pop": { "$gt": 1000000 } } ] } ).count()
```

The best operator for this case is $nor, it excludes both cases from consideration and we are left with the remaining set.

</details>
