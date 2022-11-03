---
description: >-
  mongoimport 명령어로 people.json, restaurants.json 실습데이터 업로드 필요 파일이 커서 atlas
  cluster에 더미 데이터를 생성 된 상태에서 이것까지 올리면 용량 부족함 기존 더미데이터 지우고 import 필요 아님 로컬 mongod
  single 구성 후 실습해야함
---

# ☝ M201 : MongoDB Performance

## "Mongodbimport" 명령어는 mongodb-database-tools 안에 있음 따라서 없는 경우 설치 필요!

> mac mongodb database tool install

```
brew install mongodb/brew/mongodb-database-tools
```

> window mongodb database tool install

{% embed url="https://www.mongodb.com/try/download/bi-connector" %}

## Chapter 1: Introduction

### Lab 1: Install Course Tools and Datasets

<details>

<summary>Problem</summary>

Welcome to your first lab in M201!

In this lab, you will install some tools and load some of the datasets we will use in the course.

You can follow the lessons and do the labs using either:

1. Your private Atlas cluster
2. A local installation of MongoDB on your machine

**Option 1: Your private Atlas cluster**

You will need to:

* create an [Atlas cluster](https://www.mongodb.com/university-signup) ([Atlas text instructions](https://docs.atlas.mongodb.com/tutorial/create-new-cluster/) or [Atlas video demo](https://www.youtube.com/watch?v=rPqRyYJmx2g))
* [install the MongoDB shell and tools](https://www.mongodb.com/try/download/database-tools?jmp=university) to load the datasets into the cluster and access the databases

Your Atlas cluster should be reachable with a URI. You can see the URI by clicking the **Connect** button next to your cluster in _Atlas_.

For example, this is what the **mongodb+srv** URI looks like for the M001 introductory course cluster, You likely connected to that cluser when you took the M001 course.

COPY

```
mongodb+srv://<username>:<password>@cluster0-jxeqq.mongodb.net/test
```

Your URI will have a different cluster name, and you will need to provide the appropriate **\<username>** and **\<password>**

Once you identified your URI, let's upload the few datasets attached to this lesson. Download the datasets and uncompress them if necessary. On my cluster, the command look like the following from the Unix/MacOS/Windows shell:

```shell
$ # Example for my Atlas cluster, you will need to change the
$ # user, password and Atlas cluster name

$ mongoimport --drop -c people --uri mongodb+srv://theusername:thepassword@m201-nhtgi.mongodb.net/m201 people.json

$ mongoimport --drop -c restaurants --uri mongodb+srv://theusername:thepassword@m201-nhtgi.mongodb.net/m201 restaurants.json
```

**Option 2: A local installation of MongoDB on your machine**

To install MongoDB, you are going to need to head over to our online documentation and follow the [instructions on installing MongoDB](https://docs.mongodb.com/manual/administration/install-community/).

Assuming you are running **mongod** on port **27017**, your **mongodb** URI is:

```
mongodb://localhost:27017
```

Let's upload the few datasets attached to this lesson. Download the datasets and uncompress them if necessary. On my cluster, the command look like the following from the Unix/MacOS/Windows shell:

```
$ # Example for my local cluster running on port 27017

$ mongoimport --drop -c people --uri mongodb://localhost:27017/m201 people.json

$ mongoimport --drop -c restaurants --uri mongodb://localhost:27017/m201 restaurants.json
```

**Verification**

To confirm that you've successfully completed the required steps to follow the class and do the labs, run the following query on the **m201** database from the **mongo shell** and paste its output into the submission area below:

```
> use m201
> db.people.count({ "email" : {"$exists": 1} })
```

</details>

<details>

<summary>Explanation</summary>

If you created a cluster and imported data correctly, then when you ran:

```
> use m201
> db.people.count({ "email" : {"$exists": 1} })
```

The count of documents with an _email_ field should be:

```
50474
```

</details>



## Chapter 2: MongoDB Indexes

### Lab 1: Using Indexes to Sort

<details>

<summary>Problem</summary>

In this lab you're going to determine which queries are able to successfully use a given index for both filtering and sorting.

Given the following index:

```javascript
{ "first_name": 1, "address.state": -1, "address.city": -1, "ssn": 1 }
```

Which of the following queries are able to use it for both filtering and sorting?

**Attempts Remaining:**Correct Answer

Check all answers that apply:

* `db.people.find({ "first_name": { $gt: "J" } }).sort({ "address.city": -1 })`

<!---->

* `db.people.find({ "first_name": "Jessica", "address.state": { $lt: "S"} }).sort({ "address.state": 1 })`

<!---->

* `db.people.find({ "first_name": "Jessica" }).sort({ "address.state": 1, "address.city": 1 })`

<!---->

* `db.people.find({ "address.state": "South Dakota", "first_name": "Jessica" }).sort({ "address.city": -1 })`

<!---->

* `db.people.find({ "address.city": "West Cindy" }).sort({ "address.city": -1 })`

</details>

<details>

<summary>Explanation</summary>



The key to this lab is to identify the prefixes for the given index, and to take your time and think about each query one by one.

Here's an explanation for each query:

*   `db.people.find({ "first_name": { $gt: "J" } }).sort({ "address.city": -1 })`

    No, this query doesn't use equality on the index prefix. When using an index for filtering and sorting the query must include equality conditions on all the prefix keys that precede the sort keys. Moreover, on the sort predicate it skipped the next key in the prefix "address.state".
*   `db.people.find({ "first_name": "Jessica" }).sort({ "address.state": 1, "address.city": 1 })`

    <mark style="color:green;">**Yes**</mark>, this query matches with equality on the query predicate with an index prefix, and continues the prefix in the sort predicate by walking the index backward.
*   `db.people.find({ "first_name": "Jessica", "address.state": { $lt: "S"} }).sort({ "address.state": 1 })`

    <mark style="color:green;">**Yes**</mark>, while this query fails to use equality on the "address.state" field of the index prefix, it uses the same field for sorting.
*   `db.people.find({ "address.city": "West Cindy" }).sort({ "address.city": -1 })`

    No, this query does not use an index prefix.
*   `db.people.find({ "address.state": "South Dakota", "first_name": "Jessica" }).sort({ "address.city": -1 })`

    <mark style="color:green;">**Yes**</mark>, this query is able to use the index prefix. The order of the fields in the **query** predicate does not matter. Since both address.state and first\_name are part of the index prefix, this query can utilize the index for the equality condition.

</details>



### Lab 2: Optimizing Compound Indexes

<details>

<summary>Problem</summary>

In this lab you're going to examine several example queries and determine which compound index will best service them.

```javascript
> db.people.find({
    "address.state": "Nebraska",
    "last_name": /^G/,
    "job": "Police officer"
  })
```

```javascript
> db.people.find({
    "job": /^P/,
    "first_name": /^C/,
    "address.state": "Indiana"
  }).sort({ "last_name": 1 })
```

```javascript
> db.people.find({
    "address.state": "Connecticut",
    "birthday": {
      "$gte": ISODate("2010-01-01T00:00:00.000Z"),
      "$lt": ISODate("2011-01-01T00:00:00.000Z")
    }
  })
```

If you had to build one index on the **people** collection, which of the following indexes would best service all 3 queries?

**Attempts Remaining:**Correct Answer

Choose the best answer:

* `{ "job": 1, "address.state": 1, "first_name": 1 }`

<!---->

* `{ "job": 1, "address.state": 1 }`

<!---->

* `{ "job": 1, "address.state": 1, "last_name": 1 }`

<!---->

* `{ "address.state": 1, "last_name": 1, "job": 1 }`

<!---->

* `{ "address.state": 1, "job": 1 }`

<!---->

* `{ "address.state": 1, "job": 1, "first_name": 1 }`

</details>

<details>

<summary>Explanation</summary>

The key to this lab is to determine which index will provide the most index prefixes that can be utilized by the 3 example queries.

Let's analyze each option:

*   `{ "address.state": 1, "job": 1 }`

    No, while this index would be able to service all 3 of the example queries, there's a better index that can be used on the first query, and the second query has to do an in-memory sort.
*   `{ "address.state": 1, "job": 1, "first_name": 1 }`

    No, this index is better than the first, but it still doesn't help with the sort on the second query.
*   `{ "address.state": 1, "last_name": 1, "job": 1 }`

    <mark style="color:green;">**Yes**</mark>, this is the best index. This index matches the first query, can be used for sorting on the second, and has an prefix for the 3rd query.
*   `{ "job": 1, "address.state": 1 }`

    No, this index can only be used by the first two queries.
*   `{ "job": 1, "address.state": 1, "first_name": 1 }`

    No, while this index is better than the one directly above it, this index still cannot be used by the 3rd query at all.
*   `{ "job": 1, "address.state": 1, "last_name": 1 }`

    No, this index has the same issues as the index directly above it.

</details>



## Chapter 3: Index Operations

### Lab 1: Explain Output

<details>

<summary>Problem</summary>

In this lab you're going to determine which index was used to satisfy a query given its explain output.

The following query was ran:

```javascript
> var exp = db.restaurants.explain("executionStats")
> exp.find({ "address.state": "NY", stars: { $gt: 3, $lt: 4 } }).sort({ name: 1 }).hint(REDACTED)
```

Which resulted in the following output:

```javascript
{
  "queryPlanner": {
    "plannerVersion": 1,
    "namespace": "m201.restaurants",
    "indexFilterSet": false,
    "parsedQuery": "REDACTED",
    "winningPlan": {
      "stage": "SORT",
      "sortPattern": {
        "name": 1
      },
      "inputStage": {
        "stage": "SORT_KEY_GENERATOR",
        "inputStage": {
          "stage": "FETCH",
          "inputStage": {
            "stage": "IXSCAN",
            "keyPattern": "REDACTED",
            "indexName": "REDACTED",
            "isMultiKey": false,
            "isUnique": false,
            "isSparse": false,
            "isPartial": false,
            "indexVersion": 1,
            "direction": "forward",
            "indexBounds": "REDACTED"
          }
        }
      }
    },
    "rejectedPlans": [ ]
  },
  "executionStats": {
    "executionSuccess": true,
    "nReturned": 3335,
    "executionTimeMillis": 20,
    "totalKeysExamined": 3335,
    "totalDocsExamined": 3335,
    "executionStages": "REDACTED"
  },
  "serverInfo": "REDACTED",
  "ok": 1
}
```

Given the redacted explain output above, select the index that was passed to hint.

Note: The hint() method is used to force the query planner to select a particular index for a given query. You can learn more about hint by visiting [its documentation](https://docs.mongodb.com/manual/reference/method/cursor.hint/).

**Attempts Remaining:** Correct Answer

Choose the best answer:

* `{ "address.state": 1 }`

<!---->

* `{ "address.state": 1, "stars": 1, "name": 1 }`

<!---->

* `{ "address.state": 1, "name": 1 }`

<!---->

* `{ "address.state": 1, "name": 1, "stars": 1 }`

</details>

<details>

<summary>Explanation</summary>

*   `{ "address.state": 1, "name": 1, "stars": 1 }`

    No, if this index was used, then there would be no SORT stage.
*   `{ "address.state": 1, "stars": 1, "name": 1 }`

    <mark style="color:green;">**Yes**</mark>, this query wouldn't need to examine any extra index keys, so since nReturned and totalKeysExamined are both 3,335 we know this index was used.
*   `{ "address.state": 1, "name": 1 }`

    No, if this index was used, then there would be no SORT stage.
*   `{ "address.state": 1 }`

    No, if this index was used, then we would expect that we'd have to examine some unnecessary documents and index keys. Since there are 50 states in the US, and we have 1,000,000 documents we'd expect to examine about 20,000 documents, not the 3,335 we see in the output.

</details>



## Chapter 4: CRUD Optimization

### Lab 1: Equality, Sort, Range

<details>

<summary>Problem</summary>

In this lab you're going to use the equality, sort, range rule to determine which index best supports a given query.

Given the following query:

```javascript
db.accounts.find( { accountBalance : { $gte : NumberDecimal(100000.00) }, city: "New York" } )
           .sort( { lastName: 1, firstName: 1 } )
```

Which of the following indexes best supports this query with regards to the equality, sort, range rule.

**Attempts Remaining:**Correct Answer

Choose the best answer:

* `{ accountBalance: 1, city: 1, lastName: 1, firstName: 1 }`

<!---->

* `{ city: 1, lastName: 1, firstName: 1, accountBalance: 1 }`

<!---->

* `{ lastName: 1, firstName: 1, accountBalance: 1, city: 1 }`

<!---->

* `{ lastName: 1, firstName: 1, city: 1, accountBalance: 1 }`

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">`{ city: 1, lastName: 1, firstName: 1, accountBalance: 1 }`</mark>

&#x20;인덱스 생성 순서는 동등, 정렬 (순서), 범위 비교 순이 효과적임

Find 필터 과정에서 사용된 필드는 복합 인덱스 생성시 순서 고려 하지 않아도 된다.

</details>



### Lab 2: Aggregation Performance

<details>

<summary>Problem</summary>

For this lab, you're going to create an index so that the following aggregation query can be executed successfully.

We assume you have imported the _restaurants_ dataset in your cluster. If not, it is attached to this lesson, so you can import it following the instructions in the _Install Course Tools and Datasets_ lesson in Chapter 1.

Before you work on your solution, delete all indexes for the _restaurants_ dataset:

```javascript
db.restaurants.dropIndexes()
```

Then, run the following query and you will receive an error.

```javascript
db.restaurants.aggregate([
  { $match: { stars: { $gt: 2 } } },
  { $sort: { stars: 1 } },
  { $group: { _id: "$cuisine", count: { $sum: 1 } } }
])
```

```javascript
{
  "ok": 0,
  "errmsg": "Sort exceeded memory limit of 104857600 bytes, but did not opt in to external sorting. Aborting operation. Pass allowDiskUse:true to opt in.",
  "code": 16819,
  "codeName": "Location16819"
}
```

The task of this lab is to identify why this error is occuring and build an index to resolve the issue.

Keep in mind that there might be several indexes that resolve this error, but we're looking for an index with the smallest number of fields that resolves the error and services this command.

In the text box below, submit the index that resolves the issue.

For example, if you ran:

```javascript
db.restaurants.createIndex( { foobar: 1 } )
```

to create the index that fixes the error, then you would enter the following document which lists the fields indexed into the text box:

```javascript
{ foobar: 1 }
```

</details>

<details>

<summary>Explanation</summary>

The reason that the aggregation query failed is because an in-memory sort is necessary to satisfy the aggregation query, and that in-memory sort uses more than 100MB of memory.

To resolve the issue you can create an index on the field that it sorts on, stars, like so:

```javascript
db.restaurants.createIndex({ stars: 1 })
```

Other indexes could also be used like db.restaurants.createIndex({ stars: 1, cuisine: 1}) however, if we are looking for most effective index to support our aggregation command, { stars: 1 } is the optimal option.

</details>



## Final Exam

<details>

<summary>Question 1 - explanation</summary>

The only true statement was:

* **Creating an ascending index on a monotonically increasing value creates index keys on the right-hand side of the index tree.**

Let's take a look at why each of the other statements are false:

*   **You can index multiple array fields in a single document with a single compound index.**

    Multikey indexes allow us to index on array fields, but they do not support indexes on multiple array fields on single documents.
*   **Covered queries can sometimes still require some of your documents to be examined.**

    A query is covered if and only if it can be satisfied using the keys of the index.
*   **Write concern has no impact on write latency.**

    Different write concerns can certainly impact your write latency. Write concerns that only need acknowledgment from a primary are generally faster than ones that need acknowledgment from a majority of replica set members.
*   **A collection scan has a logarithmic search time.**

    No, collection scans have a **linear** search time.

</details>

<details>

<summary>Question 2 - explanation</summary>

All of the following statements are true!

* **Indexes can decrease insert throughput.**
* **Partial indexes can be used to reduce the size requirements of the indexes.**
* **It's important to ensure that secondaries with indexes that differ from the primary not be eligible to become primary.**
* **Indexes can be walked backwards by inverting their keys in a sort predicate.**
* **It's important to ensure that your shard key has high cardinality.**

</details>

<details>

<summary>Question 3 - explanation</summary>

Let's take a closer look at each of these possibilities:

*   **MongoDB indexes are markov trees.**

    No, MongoDB indexes are designed using B-trees.
*   **By default, all MongoDB user-created collections have an \_id index.**

    Yes, this is true!
*   **Background index builds block all reads and writes to the database that holds the collection being indexed.**

    No, **foreground** index builds block all reads and writes to the database that holds the collection being indexed. Background index builds don't have this limitation, but are generally slower than foreground index builds.
*   **It's common practice to co-locate your mongos on the same machine as your application to reduce latency.**

    Yes, this is true!
*   **Collations can be used to create case insensitive indexes.**

    Yes, this is true!

</details>

<details>

<summary>Question 4 - explanation</summary>

*   **Indexes can solve the problem of slow queries.**

    This is correct.
*   **Indexes are fast to search because they're ordered such that you can find target values with few comparisons.**

    This is correct.
*   **Under heavy write load you should scale your read throughput by reading from secondaries.**

    No, since writes are replicated to secondaries all members of the replica set have about the same write workload, therefore sending reads to a secondary will not scale you read throughput. However, after MongoDB 4.0 all secondary reads can read from snapshot without being blocked by replication writes.
*   **When you index on a field that is an array it creates a partial index.**

    No, when you index a field that is an array it creates a **multikey** index.
*   **On a sharded cluster, aggregation queries using $lookup will require a merge stage on a random shard.**

    No, $lookup, $graphLookup, $facet, and $out all require a merge stage on the **primary** shard, not a random shard like most other merged queries.

</details>

<details>

<summary>Question 5 - explanation</summary>

Let's take a moment to examine each of the choices:

*   **Compound indexes can service queries that filter on any subset of the index keys.**

    No, not all subsets of a index's keys can service a query. The prefix of an index's keys can service a query.
*   **Compound indexes can service queries that filter on a prefix of the index keys.**

    Yes, this is true!
*   **If no indexes can be used then a collection scan will be necessary.**

    Yes, this is true and should be avoided!
*   **Query plans are removed from the plan cache on index creation, destruction, or server restart.**

    Yes, this is true!
*   **By default, the explain() command will execute your query.**

    No, by default explain() will **not** execute your query. This is useful to test queries that need to run on a server under heavy load. Passing "executionStats" or "allPlansExecution" will execute the query and collect execution statistics.

</details>

<details>

<summary>Question 6 - explanation</summary>

*   **An index doesn't become multikey until a document is inserted that has an array value.**

    This is correct!
*   **Running performance tests from the mongo shell is an acceptable way to benchmark your database.**

    No, you're performance tests should be as close to your production environment as possible. The mongo shell is designed for administrative tasks and ah-hoc queries, not performance benchmarks. You'd also be running in a single thread, which is unlikely how you'd be operating in production.
*   **You can use the --wiredTigerDirectoryForIndexes option to place your indexes on a different disk than your data.**

    This is correct!
*   **Indexes can only be traversed forward.**

    No, indexes can be traversed both forward _and_ backward.
*   **The ideal ratio between nReturned and totalKeysExamined is 1.**

    This is correct!

</details>

<details>

<summary>Question 7 - explanation</summary>

Let's examine each of these choices:

*   **Index #1 would provide a sort to query #3.**

    Yes, that is correct.
*   **Index #2 properly uses the equality, sort, range rule for query #1.**

    No, if we were to build an index for query #1 using the equality, sort, range rule, then the index would be: { in\_stock: 1, name: 1, price: 1 }.
*   **There would be a total of 4 index keys created across all of these documents and indexes.**

    No, there would be 5 total index keys:

    * { categories: 'Beauty', price: 2.99 }
    * { categories: 'Personal Care', price: 2.99 }
    * { categories: 'Outdoors', price: 7.99 }
    * { in\_stock: true, price: 2.99, name: 'Soap' }
    * { in\_stock: false, price: 7.99, name: 'Knife'}

    The additional index keys are due to the multikey index on categories.
*   **Index #2 can be used by both query #1 and #2.**

    Yes, that is correct.

</details>

> Written by

{% embed url="https://github.com/LakHyeonKim" %}
