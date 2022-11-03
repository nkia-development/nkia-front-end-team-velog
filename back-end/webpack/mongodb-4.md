---
description: >-
  Lab 실습문제 중 여기에서 제공해주는 Validators를 통해 Json 파일로 (패턴)설계 후 정답이면 코드가 생성됨 정답 제출 시
  필요함
---

# ☝ M320 : MongoDB Data Modeling

<details>

<summary><strong>Validators setting</strong></summary>

**Validators for the course for each OS**

[Download MacOSX Validator](https://university-courses.s3.amazonaws.com/M320/validators/v2.01/macosx/validate\_m320)

If the below warning message pops up when running the validator, make sure to give this application permission to run.

![https://university-courses.s3.amazonaws.com/M320/error.png](https://university-courses.s3.amazonaws.com/M320/error.png)

[Follow these instructions](https://www.howtogeek.com/205393/gatekeeper-101-why-your-mac-only-allows-apple-approved-software-by-default/) to allow the application run on your machine.

[Download Linux Validator](https://university-courses.s3.amazonaws.com/M320/validators/v2.01/linux/validate\_m320)[\
Download Windows 64bit Validator](https://university-courses.s3.amazonaws.com/M320/validators/v2.01/win64/validate\_m320.exe)

**To change file permissions from the Mac or Linux shell:**

```
chmod 555 validate_m320
```

**Ensure you have this specific version:**

```
validate_m320 --version
```

```
"validate_m320 version 02.01"
```

**Example problem:**

Below is the old schema that describes what document structure our application was programmed for in the past. In the updated version of the application, it is expecting to receive a **boolean** value for the in\_house field instead of an **integer**.

[Here is the list of valid BSON types for reference.](https://docs.mongodb.com/manual/reference/bson-types/)

Modify the following schema to reflect the required change.

```javascript
{
  "_id": "<objectId>",
  "title": "<string>",
  "artist": "<string>",
  "room": "<string>",
  "spot": "<string>",
  "on_display": "<bool>",
  "in_house": "<int>",
  "events": [{
    "k": "<string>",
    "v": "<date>"
  }]
}
```

**To run the validator for the example lab on Windows, run the following command:**

```
validate_m320 example --file answer_schema.json
```

**To run the validator for the example lab on MacOS and Linux, run the following command:**

```
./validate_m320 example --file answer_schema.json
```

**To help verify the syntax of your JSON files**

[JSON validator tool.](https://jsonlint.com/)

**To learn more about JSON Schema**

[JSON Schema validation reference.](https://json-schema.org/)

</details>

## Capter 2: Relationships

### Lab1: Many-to-Many Relationship

<details>

<summary>Problem</summary>

Problem:

Given the following Collection Relationship Diagram (CRD), identify the relationships that represent Many-to-Many relationships.

We are asking you to identify not only direct Many-to-Many relationships, but also transitives ones. For example a user has a One-to-Many relationship with its reviews and a One-to-Many relationship with its credit cards, making a Many-to-Many relationship between the reviews and the credit cards.

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_many-to-many-problem.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_many-to-many-problem.png)

**Attempts Remaining:**Correct Answer

Check all answers that apply:

* **users.credit\_cards.number** and **items.reviews.body**

<!---->

* **users.shipping\_address.street** and **items.reviews.body**

<!---->

* **items.sold\_at** and **items.reviews.body**

<!---->

* **items.title** and **items.reviews.body**

<!---->

* **stores.address.street** and **items.description**

</details>

<details>

<summary>Explanation</summary>



<mark style="color:green;">**Correct Options:**</mark>

*   <mark style="color:green;">**stores.address.street**</mark> <mark style="color:green;"></mark><mark style="color:green;">and</mark> <mark style="color:green;"></mark><mark style="color:green;">**items.description**</mark>

    There is one given street address for a store and one description for an item.

    However, the **stores** and **items** entities have a Many-to-Many relationship per the Crow's Foot symbol. To be more precise, it says that a given item can be sold at 0 to Many stores (as represented by the **items.sold\_at** array), and a store can sell 0 to Many items (as represented by one **stores.\_id** being stored in 0 to Many **items.sold\_at** arrays).

    The two directional One-to-Many readings give us a Many-to-Many relationship.

    In this case, linking is used to establish the relationship.
*   <mark style="color:green;">**items.sold\_at**</mark> <mark style="color:green;"></mark><mark style="color:green;">and</mark> <mark style="color:green;"></mark><mark style="color:green;">**items.reviews.body**</mark>

    From an **items** point of view there are possibly _Many_ values of **sold\_at** and _Many_ values of **reviews.body**.

    So going from one of those two fields to another, we will traverse a Many-to-One relationship, then a One-to-Many relationship, making the whole relationship a Many-to-Many relationship.

    Another way to look at it is to understand that a given review will be indirectly linked to all the stores that sell the product and that a given store has multiple reviews pertaining to the products it sells.
*   <mark style="color:green;">**users.credit\_cards.number**</mark> <mark style="color:green;"></mark><mark style="color:green;">and</mark> <mark style="color:green;"></mark><mark style="color:green;">**items.reviews.body**</mark>

    A user writes many reviews.

    Reading the relationship from the other end, a review is written by only one user, however, there are many credit cards identifying this user, due to the fact that a user has an array of credit cards. So a review for a given user can be can be indirectly linked to many credit cards.

    Those two readings give us again a Many-to-Many relationship between those 2 fields.

One thing to understand from all of the above examples of Many-to-Many relationships is that traversing a relationship through either a noted One-to-Many CRD relationship or through entering an array will identify a directional relationship as One-to-Many.

This means that you don't really need to understand the domain of a system to identify potential One-to-Many relationships. Reading the relationships between collections and accounting for the traversal of arrays is enough to identify relationships.

Getting One-to-Many relationships from both directions will identify the relationship as a Many-to-Many relationship.

***

**Incorrect Options:**

*   **items.title** and **items.reviews.body**

    A given item, identified by its title here, has many reviews, represented here by an array.

    However, a given review only applies to one item.

    This means we have a One-to-Many relationship between the **items.title** and **items.reviews.body**. Going from the **items.reviews.body** to the **items.title** we walk out of the array, not in, so this is One-to-One in this direction, meaning for a given **reviews.body** there is only one **product.description** to which it applies.
*   **users.shipping\_address.street** and **items.reviews.body**

    For a given **users.shipping\_address.street** there are many related **items.reviews.body** values, so One-to-Many from this direction.

    The **items.reviews.body** values are associated to a single user, which has a single shipping address, leading to a One-to-One relationship in this opposite direction.

    Overall, the relationship between **users.shipping\_address.street** and **items.reviews.body** is One-to-Many.

</details>

###

### Lab2: One-to-One Relationship

<details>

<summary>Problem</summary>

A legacy database has been ported to MongoDB, resulting in a set of collections that were mapped to their original tables. This port has been quickly identified as a poor solution.

We have been tasked with redesigning the **employees** collection to make better use of the document model to make the information clearer.

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-problem.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-problem.png)

While we are restructuring the database, the Human Resources department would like us to move any confidential employee information to a different collection to make the information easier to protect.

Consider the following potential schema designs. Each of these designs represents an individual employee and the One-to-One relationship between all of their fields.

The ideal schema design should store:

* address information together as an embedded sub-document
* all of an employee's phone numbers together as an embedded sub-document
* all salary and bonus compensation information together as an embedded sub-document
* all confidential information in a separate collection

Once you've identified the ideal design, you can deepen your knowledge by trying to explain why each of the other options is not the preferred design choice.

**Attempts Remaining:**Correct Answer

Choose the best answer:

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-5.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-5.png)![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-4.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-4.png)![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-2.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-2.png)![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-3.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-3.png)![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-1.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-1.png)

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**Correct Option:**</mark>

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-1.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-1.png)

In the original schema, all fields had a One-to-One relationship by being at the same level.

First we migrated fields containing confidential information to the **emp\_confidential** collection and established a relationship between the two collections. Even though this information is stored across two collections, it still represents a One-to-One relationship.

Then we created sub-documents for **phones**, **address**, and **compensation** to organize the information. Embedding an entity inside our collection does not change the relationship, as long as the embedded entities are not arrays of entities.

***

**Incorrect Options:**

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-2.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-2.png)

In this option, the **phones** are represented as an array. You see it through the **\[0, 1, 10]** notation of the cardinalities of the array.

Using an array of phones instead of an embedded sub-document changes the relationship between an employee and their phone numbers. Instead of an employee having a One-to-One relationship with each of their specific phone numbers, they now have a One-to-Many relationship between a person and a variable number of phone numbers. Additionally, each number can no longer be identified by its intended purpose.

Because, there is only one **home\_phone** and one **work\_phone**, creating a sub-document is sufficient.

***

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-3.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-3.png)

In this option, the field **employee\_id** does not exist in the **emp\_confidential** collection. We can't use **\_id** to establish the relationship back to the **employees** collection field **employee\_id** because they have different types.

***

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-4.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-4.png)

In this option, we erroneously established a One-to-Many relationship between an employee and their confidential information.

You can see the relationship by the Crow's Foot connector or by the **\[0, 1, 10]** cardinality on the relationship.

***

![https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-5.png](https://university-courses.s3.amazonaws.com/M320/lab\_relationship\_one-to-one-answer-5.png)

In this option, we did not create a separate collection for the confidential information.

We could still restrict the access of some fields through the use of views in MongoDB, but it is much easier to do it at the collection level.

[\
](https://university.mongodb.com/mercury/M320/2022\_October\_18/chapter/Chapter\_2\_Relationships/lesson/5ccb54538ea52e59d872bf53/lecture)

</details>



## Chapter 3: Patterns (Part 1)

### Lab1: Apply the Attribute Pattern

<details>

<summary>Problem</summary>

**User Story**

The museum we work at has grown from a local attraction to one that is seen as having very popular items.

For this reason, other museums in the World have started exchanging pieces of art with our museum.

Our database was tracking if our pieces are on display and where they are in the museum.

To track the pieces we started exchanging with other museum, we added an array called events, in which we created an entry for each date a piece was loaned and the museum it was loaned to.

```javascript
{
  "_id": ObjectId("5c5348f5be09bedd4f196f18"),
  "title": "Cookies in the sky",
  "artist": "Michelle Vinci",
  "date_acquisition": ISODate("2017-12-25T00:00:00.000Z"),
  "location": "Blue Room, 20A",
  "on_display": false,
  "in_house": false,
  "events": [{
    "moma": ISODate("2019-01-31T00:00:00.000Z"),
    "louvres": ISODate("2020-01-01T00:00:00.000Z")
  }]
}
```

The problem with this design is that we need to build a new index every time there is a new museum with which we start exchanging pieces. For example, when we started working with The Prado in Madrid, we needed to add this index:

```javascript
{ "events.prado" : 1 }
```

***

**Task**

To address this issue, you've decided to change the schema to:

* use a single index on all event dates.
* transform the field that tracks the date when a piece was acquired, _date\_acquisition_, so that it is also indexed with the values above.

To ensure the validator can verify your solution, use "k" and "v" as field names if needed.

To complete this lab:

*   Modify the following schema to incorporate the above changes:

    ```javascript
    {
      "_id": "<objectId>",
      "title": "<string>",
      "artist": "<string>",
      "date_acquisition": "<date>",
      "location": "<string>",
      "on_display": "<bool>",
      "in_house": "<bool>",
      "events": [{
        "moma": "<date>",
        "louvres": "<date>"
      }]
    }
    ```
* Save your new schema to a file named **pattern\_attribute.json**.
*   Validate your answer on _Windows_ by running in the **CMD** shell:

    ```shell
    validate_m320 pattern_attribute --file pattern_attribute.json
    ```
*   Validate your answer on _MacOS_ and _Linux_ by running:

    ```shell
    ./validate_m320 pattern_attribute --file pattern_attribute.json
    ```

After running this script you will either be given a validation code or an error message indicating what might be missing in your file.

When you get the validation code, paste it in the text box below and click the submit button.

</details>

<details>

<summary>Explanation</summary>

To solve this problem, you had to use the _Attribute Pattern_

Start by replacing the fields under the _event_ field with _k_ and _v_, where _k_ will contain the name of an event as _string_ and _v_ will contain the _date_ for such event.

Also, you needed to remove _acquisition\_date_ from the root of the document and use _k/v_ to represent it.

The document listed in the question would look like the following once transformed:

```javascript
{
  "_id": ObjectId("5c5348f5be09bedd4f196f18"),
  "title": "Cookies in the sky",
  "artist": "Michelle Vinci",
  "location": "Blue Room, 20A",
  "on_display": false,
  "in_house": false,
  "events": [{
    "k": "date_acquisition",
    "v": ISODate("2017-12-25T00:00:00.000Z")
  }, {
    "k": "moma",
    "v": ISODate("2019-01-31T00:00:00.000Z")
  }, {
    "k": "louvres",
    "v": ISODate("2020-01-01T00:00:00.000Z")
  }]
}
```

And the corresponding schema that you verified with the validator would look like this:

```javascript
{
  "_id": "<objectId>",
  "title": "<string>",
  "artist": "<string>",
  "location": "<string>",
  "on_display": "<bool>",
  "in_house": "<bool>",
  "events": [{
    "k": "<string>",
    "v": "<date>"
  }]
}
```

</details>



### Lab2: Apply the Subset Pattern

<details>

<summary>Problem</summary>

You are the lead developer for an online organic recycled clothing store. Consider the following user story:

**User Story**

Due to the growing number of environmentally-conscious consumers, our store's inventory has increased exponentially. We now also have an increasingly large pool of makers and suppliers.

We recently found that our shopping app is getting slower due to the fact that the frequently-used documents can no longer all fit in RAM. This is happening largely due to having all product reviews, questions, and specs stored in the same document, which grows in size as reviews and ratings keep coming in.

To resolve this issue, we want to reduce the amount of data immediately available to the user in the app and only load additional data when the user asks for it.

Currently a typical document in our data base looks like this:

```javascript
{
  "_id": ObjectId("5c9be463f752ec6b191c3c7e"),
  "item_code": "AS45OPD",
  "name": "Recycled Kicks",
  "maker_brand": "Shoes From The Gutter",
  "price": 100.00,
  "description": "These amazing Kicks are made from recycled plastics and
  fabrics.They come in a variety of sizes and are completely unisex in design.
  If your feet don't like them within the first 30 days, we'll return your
  money no questions asked.
  ",
  "materials": [
    "recycled cotton",
    "recycled plastic",
    "recycled food waste",
  ],
  "country": "Russia",
  "image": "https:///www.shoesfromthegutter.com/kicks/AS45OPD.img",
  "available_sizes": {
      "mens": ["5", "6", "8", "8W", "10", "10W", "11", "11W", "12", "12W"],
      "womens": ["5", "6", "7", "8", "9", "10", "11", "12"]
  },
  "package_weight_kg": 2.00,
  "average_rating": 4.8,
  "reviews": [{
      "author": "i_love_kicks",
      "text": "best shoes ever! comfortable, awesome colors and design!",
      "rating": 5
    },
    {
      "author": "i_know_everything",
      "text": "These shoes are no good because I ordered the wrong size.",
      "rating": 1
    },
    "..."
  ],
  "questions": [{
      "author": "i_love_kicks",
      "text": "Do you guys make baby shoes?",
      "likes": 1223
    },
    {
      "author": "i_know_everything",
      "text": "Why do you make shoes out of garbage?",
      "likes": 0
    },
    "..."
  ],
  "stock_amount": 10000,
  "maker_address": {
    "building_number": 7,
    "street_name": "Turku",
    "city": "Saint-Petersburg",
    "country": "RU",
    "postal_code": 172091
  },
  "makers": ["Ilya Muromets", "Alyosha Popovich", "Ivan Grozniy", "Chelovek Molekula"],
}
```

***

**Task**

To address this user story, you must modify the following schema so that the working set can fit in RAM:

```javascript
{
  "_id": "<objectId>",
  "item_code": "<string>",
  "name": "<string>",
  "maker_brand": "<string>",
  "price": "<decimal>",
  "description": "<string>",
  "materials": ["<string>"],
  "country": "<string>",
  "image": "<string>",
  "available_sizes": {
    "mens": ["<string>"],
    "womens": ["<string>"]
  },
  "package_weight_kg": "<decimal>",
  "average_rating": "<decimal>",
  "reviews": [{
    "author": "<string>",
    "text": "<string>",
    "rating": "<int>"
  }],
  "questions": [{
    "author": "<string>",
    "text": "<string>",
    "likes": "<int>"
  }],
  "stock_amount": "<int>",
  "maker_address": {
    "building_number": "<string>",
    "street_name": "<string>",
    "city": "<string>",
    "country": "<string>",
    "postal_code": "<string>"
  },
  "makers": ["<string>"]
}
```

You should accomplish this task by completing the following steps:

* Remove the maker address and the list of makers from the schema with the intention of moving those pieces of information to a separate collection.
* Replace the reviews and questions fields with top\_five\_reviews and top\_five\_questions respectively.
* Save your modified schema to a file named **pattern\_subset.json**.
*   Validate your answer on _Windows_ by running in the **CMD** shell:

    ```
    validate_m320 pattern_subset --file pattern_subset.json
    ```
*   Validate your answer on _MacOS_ and _Linux_ by running:

    ```
    ./validate_m320 pattern_subset --file pattern_subset.json
    ```

After running this script you will either be given a validation code or an error message indicating what might be missing in your file.

When you get the validation code, paste it in the text box below and click submit.

</details>

<details>

<summary>Explanation</summary>

To solve this problem, you had to use the _Subset Pattern_

Start by removing the unnecessary fields, such as _makers_ and _maker address_. These fields are irrelevant for our users and the app.

We want to keep the rest of the fields renaming _reviews_ to _top 5 reviews_ and _questions_ to _top 5 questions_ to minimize the amount of data stored in a single document.

The document listed in the question would look like the following once transformed:

```javascript
{
  "_id": ObjectId("5c9be463f752ec6b191c3c7e"),
  "item_code": "AS45OPD",
  "name": "Recycled Kicks",
  "maker_brand": "Shoes From The Gutter",
  "price": 100.00,
  "description": "These amazing Kicks are made from recycled plastics and
  fabrics.They come in a variety of sizes and are completely unisex in design.
  If your feet don't like them within the first 30 days, we'll return your
  money no questions asked.
  ",
  "materials": [
    "recycled cotton",
    "recycled plastic",
    "recycled food waste",
  ],
  "country": "Russia",
  "image": "https:///www.shoesfromthegutter.com/kicks/AS45OPD.img",
  "available_sizes": {
      "mens": ["5", "6", "8", "8W", "10", "10W", "11", "11W", "12", "12W"],
      "womens": ["5", "6", "7", "8", "9", "10", "11", "12"]
  },
  "package_weight_kg": 2.00,
  "average_rating": 4.8,
  "top_five_reviews": [{
      "author": "i_love_kicks",
      "text": "best shoes ever! comfortable, awesome colors and design!",
      "rating": 5
    },
    {
      "author": "i_know_everything",
      "text": "These shoes are no good because I ordered the wrong size.",
      "rating": 1
    },
    "..."
  ],
  "top_five_questions": [{
      "author": "i_love_kicks",
      "text": "Do you guys make baby shoes?",
      "likes": 1223
    },
    {
      "author": "i_want_to_know_everything",
      "text": "How are these shoes made?",
      "likes": 1120
    },
    "..."
  ],
  "stock_amount": 10000,
}
```

And the corresponding schema that you verified with the validator would look like this:

```javascript
{
  "_id": "<objectId>",
  "item_code": "<string>",
  "name": "<string>",
  "maker_brand": "<string>",
  "price": "<decimal>",
  "description": "<string>",
  "materials": ["<string>"],
  "country": "<string>",
  "image": "<string>",
  "available_sizes": {
    "mens": ["<string>"],
    "womens": ["<string>"]
  },
  "package_weight_kg": "<decimal>",
  "average_rating": "<decimal>",
  "top_five_reviews": [{
    "author": "<string>",
    "text": "<string>",
    "rating": "<int>"
  }],
  "top_five_questions": [{
    "author": "<string>",
    "text": "<string>",
    "likes":"<int>"
  }],
  "stock_amount": "<int>"
}j
```

</details>



## Chapter 4: Patterns (Part 2)

### Lab1: Apply the Computed Pattern

<details>

<summary>Problem</summary>

**User Story**

Our city is going green, and we're reassessing our power plant. A lot of residents are switching to solar panels for their source of electricity and the power plant needs to be both ecologically friendly and flexible in its distribution of electricity throughout the city.

A number of residents are on board with the Green New Deal and are promptly installing solar panels on their homes, which reduces the consumption of electricity from the power plant.

In some cases, the new solar homes produce more energy than they use, thus selling the excess energy back to the power plant.

Our database is tracking the following data about each building in the city: how much energy in kilowatts (kW) per hour it produces (if any), how much energy it consumes, and how much energy needs to be supplemented daily.

In order to make our plant more flexible in adapting to the growing number of solar-powered homes and to project out to the happy date when there won't be a need for a city power plant, our computer algorithms are analyzing consumption data by city zones one day at a time.

```javascript
{
  "_id": ObjectId("5c9414f25e6aff2b8870a2d0"),
  "address": {
    "building number": "742",
    "street name": "Evergreen Terrace",
    "city": "Springfield",
    "state": "MA",
    "zip code": "01111"
  },
  "owner": "Homer J. Simpson",
  "zone": 13,
  "date": ISODate("2019-03-21T23:03:31.197Z"),
  "kW per day": {
    "consumption": 10,
    "self-produced": 7,
    "city-supplemented": 3
  }
}
```

The problem with this design is that we need to calculate zone totals from each building's report every day, which is a lot of unnecessary calculation during reads.

Every zone has anywhere from 100 to 900 buildings and we will create a **zone** collection using the consumption data that comes in from every unit in each zone daily.

***

**Task**

To address this issue, we've decided to implement the Computed Pattern. We will create a separate collection that stores zone-based summaries on zone consumption, production, and city-supplemented energy metrics, which we will calculate and overwrite whenever new data comes into the system.

To complete this lab:

*   Modify the following schema to represent the schema we will store in our computed collection. Remove any fields we do not need to store in our computed collection:

    ```javascript
    {
      "_id": "<objectId>",
      "address": {
        "building number": "<string>",
        "street name": "<string>",
        "city": "<string>",
        "state": "<string>",
        "zip code": "<string>"
      },
      "owner": "<string>",
      "zone": "<int>",
      "date": "<date>",
      "kW per day": {
        "consumption": "<int>",
        "self-produced": "<int>",
        "city-supplemented": "<int>"
      }
    }
    ```
* Save the updated schema to a file named **pattern\_computed.json**.
*   Validate your answer on _Windows_ by running in the **CMD** shell:

    ```
    validate_m320 pattern_computed --file pattern_computed.json
    ```
*   Validate your answer on _MacOS_ and _Linux_ by running:

    ```
    ./validate_m320 pattern_computed --file pattern_computed.json
    ```

After running this script, you will either be given a validation code or an error message indicating what might be missing in your file.

When you get the validation code paste it in the text box below and click submit.

</details>

<details>

<summary>Explanation</summary>

To solve this problem, you had to use the _Computed Pattern_

Start by removing the unnecessary fields: _city_, _owner_, and _address_. These fields are irrelevant for our computations and useless for our AI.

We want to keep the rest of the fields, since they give us important information about energy consumption by zone, throughout time, which will help evaluate any fluctuations based on day of week, zone, weather, season, etc.

The document listed in the question would look like the following once transformed:

```javascript
{
  "_id": ObjectId("5c9414f25e6aff2b8870a2d0"),
  "zone": 13,
  "date": ISODate("2019-03-21T00:00:00.000Z"),
  "kW per day": {
    "consumption": 9756,
    "self-produced": 2059,
    "city-supplemented": 7700
  }
}
```

And the corresponding schema that you verified with the validator would look like this:

```javascript
{
  "_id": "<objectId>",
  "zone": "<int>",
  "date": "<date>",
  "kW per day": {
    "consumption": "<int>",
    "self-produced": "<int>",
    "city-supplemented": "<int>"
  }
}
```

</details>



### Lab2: Apply the Bucket Pattern

<details>

<summary>Problem</summary>

In this lab, we will be applying the Bucket Pattern to address an IoT use case scenario.

**Scenario:**

You've been called in to help improve the performance profile of an application that provides a dashboard and reporting information of cell tower quality service metrics.

This application collects a set of metric information sent directly from cell towers.

Each cell tower emits a message similar to the following, in 1 minute intervals:

```javascript
{
  "date": "2019-05-01T17:23:43.042Z",
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "coordinates": [
    -8.5837984085083,
    41.161823159286136
  ],
  "established_calls": 50013,
  "dropped_calls": 1231,
  "data_in_gb": 142,
  "data_out_gb": 481
}
```

The information sent in each message corresponds to:

* the current longitude and latitude of the cell tower - coordinates
* a unique identifier of the cell tower - celltower\_id
* the date of the measurement - date
* the following accumulated counters, accumulated since last reboot:
  * the number of established cell phone calls - established\_calls
  * the number of dropped cell phone calls - dropped\_calls
  * the amount of inbound data traffic - data\_in\_gb
  * the amount of outbound data traffic - data\_out\_gb

This system needs to support the following operational requirements:

* Be capable of storing measurements for at least 500 cell towers
* Be able to produce cell tower cumulative reports on one or all four of the accumulated counters, with the following specifications:
  * Each of these reports consists of a plotted graph of the last 24 hours for each metric, with a granularity of 5 minutes
  * The 95 percentile request latency expected for this report is 100ms

**Current Implementation:**

The current implementation is to store documents that are very similar to the emitted messages, having all messages stored in a measurements collection:

```javascript
db.measurements.find({"cell_tower.id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC"})
{
  "_id": ObjectId("5cd3587395f8fbc3fab3092e"),
  "date_received": ISODate("2019-05-01T17:24:00.000Z"),
  "message_date": ISODate("2019-05-01T17:23:43.042Z"),
  "cell_tower": {
    "location": {
      "type": "Point",
      "coordinates": [
          -8.5837984085083,
           41.161823159286136
      ]
    },
    "id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC"
  },
  "established_calls": 50013,
  "dropped_calls": 1231,
  "data_in_gb": 142,
  "data_out_gb": 481
}
...
```

This implementation is expected to fill out the database servers disk space in one month and there is no budget left for hardware improvements in the next 12 months.

The current implementation is unable to produce the reports within the required **100ms**.

**Your Solution:**

In order to resolve the issues that the current approach is facing, you need to come up with a schema design alternative that allows for:

* an increase in the number of IoT devices and associated workload growth
* all report generation to comply with the expected SLA of less than 100ms
* allow for the application to report status for the last 24 hours, with a granularity of 5 minutes

Using your pattern knowledge, consider the following three choices for the implementation:

**A:**

One document per hour:

```javascript
{
  "_id": ObjectId("5cd3587395f8fbc3fab3092e"),
  "date": ISODate("2019-05-01T17:00:00.000Z"),
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "established_calls": {
    "minutes": [
      98, 262, 266, 106, 254, 109, 3, 32, 257, 199,
      194, 209, 251, 269, 175, 42, 240, 169, 166, 149,
      238, 43, 128, 119, 120, 134, 267, 87, 228, 56,
      198, 9, 203, 281, 266, 91, 210, 55, 91, 118,
      203, 283, 74, 19, 222, 37, 18, 249, 149, 76,
      165, 29, 44, 94, 277, 253, 79, 100, 182, 127
    ],
    "sum": 9072
  },
  "dropped_calls": {
    "minutes": [
      0, 1, 0, 0, 1, 0, 0, 2, 0, 0,
      0, 1, 0, 0, 0, 0, 0, 0, 0, 0,
      0, 0, 0, 0, 1, 0, 0, 0, 0, 0,
      0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
      0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
      0, 0, 0, 0, 0, 0, 0, 0, 0, 0
    ],
    "sum": 6
  },
  "data_in_gb": {
    "minutes": [
      5, 12, 30, 0, 24, 12, 12, 5, 16, 5,
      4, 18, 6, 2, 13, 7, 11, 2, 8, 30, 25,
      7, 4, 27, 2, 30, 0, 17, 17, 5,
      9, 19, 10, 4, 13, 1, 4, 3, 3, 28,
      12, 8, 1, 21, 6, 4, 29, 23, 3, 16,
      0, 30, 20, 17, 2, 13, 15, 12, 16, 6
    ],
    "sum": 704
  },
  "data_out_gb": {
    "minutes":[
      43, 100, 59, 40, 7, 57, 61, 3, 94, 84, 37,
      67, 25, 80, 40, 34, 8, 20, 69, 66,
      94, 71, 85, 95, 54, 65, 35, 26, 33,
      42, 19, 42, 72, 45, 100, 17, 96, 53, 50, 91,
      34, 79, 45, 34, 51, 96, 90, 5, 12, 30, 50,
      4, 67, 21, 54, 17, 6, 91, 19, 36
    ],
    "sum": 3020
  }
}
```

**B:**

One document per day per metric:

```javascript
{
  "_id": ObjectId("5cd3587395f8fbc3fab30934"),
  "date": ISODate("2019-05-01"),
  "metric:": "established_calls",
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "measurements": {
    "0": [98, 262, 266, 106, 254, 109, 3, 32, 257, 199, ... ],
    "1": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    "2": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    ...
    "23": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
  },
  "sum": 9072
}
{
  "_id": ObjectId("5cd3587395f8fbc3fab30933"),
  "date": ISODate("2019-05-01"),
  "metric:": "dropped_calls",
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "measurements": {
    "0": [ 0, 1, 0, 0, 1, 0, 0, 2, 0, 0, 0, 1, 0, 0, 0, ... ],
    "1": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    "2": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    ...
    "23": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...]

  },
  "sum": 6
}
{
  "_id": ObjectId("5cd3587395f8fbc3fab30932"),
  "date": ISODate("2019-05-01"),
  "metric:": "data_in_gb",
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "measurements": {
    "0": [ 5, 12, 30, 0, 24, 12, 12, 5, 16, 5, 4, 18, 6 ...],
    "1": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    "2": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    ...
    "23": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
  },
  "sum": 704
}
{
  "_id": ObjectId("5cd3587395f8fbc3fab30931"),
  "date": ISODate("2019-05-01"),
  "metric:": "data_out_gb",
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "measurements": {
    "0":[ 43, 100, 59, 40, 7, 57, 61, 3, 94, 84, 37, 67 ... ],
    "1": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    "2": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
    ...
    "23": [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0 ...],
  },
  "sum": 3020
}
```

**C:**

One document per day:

```javascript
{
  "_id": ObjectId("5cd3587395f8fbc3fab3092e"),
  "date": ISODate("2019-05-01"),
  "celltower_id": "BBA87930-4A72-4D77-B238-2BA5899C9BEC",
  "established_calls": {
    "0": 9072,
    "1": 0,
    "2": 0,
    ...
    "23": 0,
  },
  "dropped_calls": {
    "0": 6,
    "1": 0,
    "2": 0,
    ...
    "23": 0
  },
  "data_in_gb": {
    "0": 704,
    "1": 0,
    "2": 0,
    ...
    "23": 0,
  },
  "data_out_gb": {
    "0": 3020,
    "1": 0,
    "2": 0,
    ...
    "23": 0,
  }
}
```

Which of the above solutions provide a valid approach for solving the problems experienced by the application?

**Attempts Remaining:** Correct Answer

Check all answers that apply:

* **A**
* **B**
* **C**

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**A**</mark> and <mark style="color:green;">**B**</mark> are valid implementations. Both documents allow for a correct plotting of all the necessary information to process the reports, the response time for reporting generation and metric updates is both predictable and fast, keeping a steady data growth.

Option **C** is the most space- and write-efficient. However it is not viable because it does not allow for the specified reporting granularity of 5 minutes.

</details>



### Lab3: Apply the Schema Versioning Pattern

<details>

<summary>Problem</summary>

Which of the following scenarios are best suited for applying the Schema Versioning Pattern?

**Scenario A:**

Your team was assigned to upgrade the current schema with additional fields and transforming the type of different fields without bringing the system down for this upgrade. However, all documents need to be updated to the new shape quickly.

**Scenario B:**

The performance of your application became suboptimal over time. Your team has identified that the most commonly used collection could profit from embedding additional information from other collections using the Subset and Computed Patterns. All documents in the commonly-used collection will need to undergo this modification. If possible, you would like the transition to be done without downtime.

**Scenario C:**

Your company was bought by its slightly more successful competitor. Thankfully both your and your new owner's applications are flexible enough to handle both document shapes well. You do not have to modify the application or your document shape, but due to the merger, you have to keep documents with different structures in the same collection.

</details>

<details>

<summary>Explanation</summary>



<mark style="color:green;">**Correct Options**</mark>

Scenario A & B both describe situations for which the schema versioning pattern is designed.

*   <mark style="color:green;">Scenario A</mark>

    Since your team has a strict **zero downtime** transition requirement, it is essential to minimize the volume of writes throughout the upgrade. As a result, applying the Schema Versioning Pattern is the right solution.
*   <mark style="color:green;">Scenario B</mark>

    Even though the modifications to the schema are significant, it is likely that you can implement them without downtime using the Schema Versioning Pattern. Having additional information in the updated documents will probably translate to a new function that will make fewer requests to the database to gather what it needs. For the documents that were migrated, the application will call this new function. For the unmigrated documents, your application can still call the old function.

**Incorrect Option**

*   Scenario C

    Since both applications can handle both schemas, there is no need to use the schema versioning pattern. However, there is still a need to somehow distinguish one document shape from another. In this case, applying the Polymorphic Pattern, which will be covered later is the more appropriate approach.

    Once you decide to come up with a new schema that could be common to both current schemas, then you can use the schema versioning pattern to make the transition.

</details>



### Lab4: Tree Patterns

<details>

<summary>Problem</summary>



In this lab, you will be selecting a variant of the _Tree Pattern_ to improve the performance of a common set of operations for a given application.

**Scenario:**

Given your expertise in schema design, you have been called in to help improve the performance of a human resources management application called _Success Factions_.

This application needs to be able to map a corporate reporting structure, an organization chart, like the one this diagram represents:

![https://university-courses.s3.amazonaws.com/M320/mongomart\_org\_chart.png](https://university-courses.s3.amazonaws.com/M320/mongomart\_org\_chart.png)

_Success Factions_ was originally designed by only taking into account the information of a single individual's data in the organization chart. Therefore, documents in the **employees** collection, where this data is stored, has the following schema:

```javascript
{
  "_id": "<objectId>",
  "name": "<string>",
  "role": "<string>",
  "department": {
    "name": "<string>",
    "id": "<objectId>"
  }
}
```

**Task:**

You have been tasked to improve the schema design of the **employees** collection to better support the following set of operations:

* Issue one database request to find the direct manager of a given employee.
* Collect all direct reports of an employee with one single and _efficient_ query.
* Issue one update operation to change the reporting structure of an employee.

Which of the following document notation examples and tree patterns implement all of these pieces of functionality?

**Attempts Remaining:** Correct Answer

Check all answers that apply:

Array of Ancestors

```javascript
{
  "_id": "<objectId>",
  "name": "<string>",
  "role": "<string>",
  "department": {
    "name": "<string>",
    "id": "<objectId>"
  },
  "reports_to": [
    { "id": "<objectId>", "name": "<string>" }
  ]
}
```

Child References

```javascript
{
  "_id": "<objectId>",
  "name": "<string>",
  "role": "<string>",
  "department": {
    "name": "<string>",
    "id": "<objectId>"
  },
  "reports": [
    { "id": "<objectId>", "name": "<string>" }
  ]
}
```

Materialized Paths

```javascript
{
  "_id": "<objectId>",
  "name": "<string>",
  "role": "<string>",
  "department": {
    "name": "<string>",
    "id": "<objectId>"
  },
  "reports_to": "<string>/<string>/<string>"
}
```

Parent References

```javascript
{
  "_id": "<objectId>",
  "name": "<string>",
  "role": "<string>",
  "department": {
    "name": "<string>",
    "id": "<objectId>"
  },
  "reports_to": {
    "id": "<objectId>",
    "name": "<string>"
  }
}
```

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**Correct Option:**</mark>

*   Parent References

    ```javascript
    {
      "_id": "<objectId>",
      "name": "<string>",
      "role": "<string>",
      "department": {
        "name": "<string>",
        "id": "<objectId>"
      },
      "reports_to": { "id": "<objectId>", "name": "<string>" }
    }
    ```

    Using the **Parent References** pattern would be most efficient and straightforward approach given the requirements.

    Using as an example _"Jon Yullin"_ and his manager _"Stuart Spencer"_, we would get the following:

    ```javascript
    {
      "_id": ObjectId("123414123"),
      "name": "Jon Yullin",
      "role": "Intern",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": { "id": ObjectId("123414123") , "name": "Stuart Spencer" },
    }
    ```

    _One single database request to find the direct manager of a given employee_

    ```javascript
    > db.employees.find({"name": "Jon Yullin"})
    {
      "_id": ObjectId("123414123"),
      "name": "Jon Yullin",
      "role": "Intern",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": { "id": ObjectId("123414124") , "name": "Stuart Spencer" }
    }
    ```

    In this schema, the _reports\_to_ field gives the application the direct manager information.

    _Collect all direct reports of an employee with one single and efficient query_

    ```javascript
    > db.employees.createIndex({"reports_to.name": 1})
    > db.employees.find({"reports_to.name": "Stuart Spencer"})
    {
      "_id": ObjectId("123414123"),
      "name": "Jon Yullin",
      "role": "Intern",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": { "id": ObjectId("123414124") , "name": "Stuart Spencer" }
    }
    ```

    In this example, querying all of our employees for anyone with the value of _"Stuart Spencer"_ for the name of their manager, _reports\_to.name_ will give us a list of all direct reports. Creating a single field index on _reports\_to.name_ makes this query efficient.

    _One single update operation to change the reporting structure of an employee_

    ```javascript
    > db.employees.updateOne(
      {"name": "Jon Yullin"},
      {"$set": {"reports_to": { "id":  ObjectId("123414122"), "name": "Jalpa Maganin" }}}
    )
    {
      "_id": ObjectId("123414123"),
      "name": "Jon Yullin",
      "role": "Intern",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": { "id":  ObjectId("123414122"), "name": "Jalpa Maganin" }
    }
    ```

    Or in the case we would like replace a manager to a set of direct

**Incorrect Options:**

*   Array of Ancestors

    ```javascript
    {
      "_id": "<objectId>",
      "name": "<string>",
      "role": "<string>",
      "department": {
        "name": "<string>",
        "id": "<objectId>"
      },
      "reports_to": [ { "id": "<objectId>", "name": "<string>" } ]
    }
    ```

    Array of Ancestors is a pattern that allows us to perform all of the requested queries in an efficient way, however the update operation would not be possible with a single update operation.

    _One single update operation to change the reporting structure of an employee_

    To exemplify this scenario let's promote _"Stuart Spencer"_ and have him report to _"Jalpa Maganin"_

    The _reports\_to_ field in "Stuart Spencer" document could be performed with a single update:

    ```javascript
    > var new_reporting_structure = [
      { "id": ObjectId("123414120") , "name": "Maria Gutierrez" },
      { "id": ObjectId("123414123") , "name": "Jalpa Maganin" }
      ]
    > db.employees.updateOne(
        {"name": "Stuart Spencer"},
        {"$set": {"reports_to": new_reporting_structure, "role": "Manager"}}
      )
    {
      "_id": ObjectId("123414123"),
      "name": "Stuart Spencer",
      "role": "Manager",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": [
        { "id": ObjectId("123414120") , "name": "Maria Gutierrez" },
        { "id": ObjectId("123414123") , "name": "Jalpa Maganin" },
      ]
    }
    ```

    However, we would also need to run several updates, to reflect this new structure, on all of "Stuart Spencer" subsequent reports, which requires at least one other update.

    That said, the _Array of Ancestors_ is often the right solution when you need to find all direct and indirect reports.
*   Materialized Paths

    ```javascript
    {
      "_id": "<objectId>",
      "name": "<string>",
      "role": "<string>",
      "department": {
        "name": "<string>",
        "id": "<objectId>"
      },
      "reports_to": "<string>/<string>/<string>"
    }
    ```

    Using _"Jon Yullin"_ example we would get:

    ```javascript
    {
      "_id": ObjectId("123414123"),
      "name": "Jon Yullin",
      "role": "Intern",
      "department": {
        "name": "Engineering",
        "id": ObjectId("988576342364")
      },
      "reports_to": "Maria Gutierrez/Jalpa Maganin/Kimi Macha/Stuart Spencer" }
    }
    ```

    The Materialized Paths approach falls short on:

    _Collect all direct reports of an employee with one single and_ **efficient** _query_

    We can use a regular expression to match all reports of a given employee. However, this regular expression query, to be efficient and use a supporting index, requires the prepending of left end side of the _reports\_to_ values. In this particular case, if we do not have the information of the root of the organization chart, the CEO name, we would not be able to create an efficient query.

    ```javascript
    db.employees.createIndex({"reports_to": 1})
    // does not use the index on `reports_to`
    db.employees.find({"reports_to": /Stuart\ Spencer/)
    // can use the index on `reports_to` field
    db.employees.find({"reports_to": /^Maria\ Gutierrez.*Stuart\ Spencer/)
    ```

</details>



### Lab5: Apply the Polymorphic Pattern

<details>

<summary>Problem</summary>

**User Story**

Our company has been selling books online for many years.

Recently, we decided to expand and acquired the rights to sell the books as ebooks and as audiobooks.

Currently, the books in our catalog look like this:

```javascript
{
  "_id": ObjectId("5caa9e799c0aa5e39686f000"),
  "product_id": 34538756,
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "description": "MongoDB explained by MongoDB champions",
  "authors": ["Shannon Bradshaw", "Eoin Brazil", "Christina Chodorow"],
  "publisher": "O'Reilly",
  "language": "English",
  "pages": 514,
  "catalogues": {
    "isbn10": "1491954469",
    "isbn13": "978-1491954461"
  }
}
```

The following two documents are respectively the representations of the above book as ebook and audiobook from our two suppliers of these formats:

```javascript
[{
  "_id": ObjectId("5caa9e799c0bb5e39687f000"),
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "desc": "MongoDB explained by MongoDB champions",
  "authors": ["Shannon Bradshaw", "Eoin Brazil", "Christina Chodorow"],
  "publisher": "O'Reilly",
  "language": "English",
  "eformats": {
    "epub": {
      "pages": 774
    },
    "pdf": {
      "pages": 502
    }
  },
  "isbn10": "1491954469"
},{
  "_id": ObjectId("5cbb9e799c1aa5e39686f000"),
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "desc": "The complete book of MongoDB by its employees",
  "author": "Eoin Brazil",
  "narrator": "Eoin Brazil",
  "publisher": "O'Reilly",
  "language": "English",
  "length_minutes": 1200
}]
```

We decided to put all the documents, one per media type, in the same collection to minimize the impact on the current applications.

As we retrieve a document, we can forward the information about the product to the right Web page to render the information about the book in a given format.

***

**Task**

To address the schema changes, we will use the Polymorphic Pattern to identify the shape of each document depending on its format.

We will make the following modifications to our current schemas to have all documents share some common elements:

* Across all documents, we will use a new field called format that will have a value of book, ebook, or audiobook.
* Across all documents, we will have a field called product\_id that will accept an integer as its value.
* The field description should be unified across all documents.
* The field authors should be unified across all documents.

To complete this task, complete the following steps:

*   Modify the following schemas to incorporate the changes stated above:

    ```javascript
    [{
      "_id": "<objectId>",
      "product_id": "<int>",
      "title": "<string>",
      "description": "<string>",
      "authors": ["<string>"],
      "publisher": "<string>",
      "language": "<string>",
      "pages": "<int>",
      "catalogues": {
        "isbn10": "<string>",
        "isbn13": "<string>"
      }
    }, {
      "_id": "<objectId>",
      "title": "<string>",
      "desc": "<string>",
      "authors": ["<string>"],
      "publisher": "<string>",
      "language": "<string>",
      "eformats": {
        "epub": {
          "pages": "<int>"
        },
        "pdf": {
          "pages": "<int>"
        }
      },
      "isbn10": "<string>"
    },{
      "_id": "<objectId>",
      "title": "<string>",
      "desc": "<string>",
      "author": "<string>",
      "narrator": "<string>",
      "publisher": "<string>",
      "language": "<string>",
      "length_minutes": "<int>"
    }]
    ```
* Save the updated schemas to a file named **pattern\_polymorphic.json**, with each schema definition separated by a comma and the full solution enclosed in array brackets. Ensure the three schemas are in the following order: book, ebook, and audiobook.
*   Validate your answer on _Windows_ by running in the **CMD** shell:

    ```
    validate_m320 pattern_polymorphic --file pattern_polymorphic.json
    ```
*   Validate your answer on _MacOS_ and _Linux_ by running:

    ```
    ./validate_m320 pattern_polymorphic --file pattern_polymorphic.json
    ```

After running this script, you will either be given a validation code or an error message indicating what might be missing in your file.

When you get the validation code, paste it in the text box below, and click submit.

</details>

<details>

<summary>Explanation</summary>

To solve this problem, you had to use the _Polymorphic Pattern_

First, and most importantly, we create a field called format. In this field, we will put one of the strings book, ebook, or audiobook.

Secondly, we need to add the field product\_id to the ebook and audiobook documents.

Third, we want to unify the description across the documents. There are two ways we can do that. The first is to rename the field desc to description for the ebook and audiobook. An alternative is to make a copy of the contents of desc into description to keep some backward compatibility. Both solutions would work.

Finally, for the authors, we need to turn the author of the audiobook documents from a string to an array of strings. It is usually a good practice to have only one type for a field. However, you could decide that the documents can accept a string or an array of strings and let the application deal with the difference of types. For this lab, we wanted to have an array of strings.

Applying the modifications to the documents listed in the question, they should now look like the following:

```javascript
[{{
  "_id": ObjectId("5caa9e799c0aa5e39686f000"),
  "format": "book",
  "product_id": 34538756,
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "description": "MongoDB explained by MongoDB champions",
  "authors": ["Shannon Bradshaw", "Eoin Brazil", "Christina Chodorow"],
  "publisher": "O'Reilly",
  "language": "English",
  "pages": 514,
  "catalogues": {
    "isbn10": "1491954469",
    "isbn13": "978-1491954461"
  }
}, {
  "_id": ObjectId("5caa9e799c0bb5e39687f000"),
  "product_id": 44538756,
  "format": "ebook",
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "desc": "MongoDB explained by MongoDB champions",
  "description": "MongoDB explained by MongoDB champions",
  "authors": ["Shannon Bradshaw", "Eoin Brazil", "Christina Chodorow"],
  "publisher": "O'Reilly",
  "language": "English",
  "eformats": {
    "epub": {
      "pages": 774
    },
    "pdf": {
      "pages": 502
    }
  },
  "isbn10": "1491954469"
},{
  "_id": ObjectId("5cbb9e799c1aa5e39686f000"),
  "format": "audiobook",
  "product_id": 54538756,
  "title": "MongoDB: The Definitive Guide: Powerful and Scalable Data Storage",
  "desc": "The complete book of MongoDB by its employees",
  "description": "MongoDB explained by MongoDB champions",
  "authors": ["Eoin Brazil"],
  "narrator": "Eoin Brazil",
  "publisher": "O'Reilly",
  "language": "English",
  "length_minutes": 1200
}]
```

And the corresponding schema that you verified with the validator would look like this:

```javascript
[{
  "_id": "<objectId>",
  "format": "<string>",
  "product_id": "<int>",
  "title": "<string>",
  "description": "<string>",
  "authors": ["<string>"],
  "publisher": "<string>",
  "language": "<string>",
  "pages": "<int>",
  "catalogues": {
    "isbn10": "<string>",
    "isbn13": "<string>"
  }
}, {
  "_id": "<objectId>",
  "format": "<string>",
  "product_id": "<int>",
  "title": "<string>",
  "desc": "<string>",
  "description": "<string>",
  "authors": ["<string>"],
  "publisher": "<string>",
  "language": "<string>",
  "eformats": {
    "epub": {
      "pages": "<int>"
    },
    "pdf": {
      "pages": "<int>"
    }
  },
  "isbn10": "<string>"
},{
  "_id": "<objectId>",
  "format": "<string>",
  "product_id": "<int>",
  "title": "<string>",
  "desc": "<string>",
  "description": "<string>",
  "authors": ["<string>"],
  "narrator": "<string>",
  "publisher": "<string>",
  "language": "<string>",
  "length_minutes": "<int>"
}]
```

</details>



## Final Exam

<details>

<summary>Question 1 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

* R2 - user views a specific item.

This is the operation, alongside its corresponding write W4, that occurs most often at peak times.

R2 has a very low expected latency of 1 millisecond.

This will drive the design to reduce the number of queries when retrieving this information. For example, embedding as much necessary information as possible in the product will help to reduce such queries.

***

**Incorrect Options**

* W4 - application records time and user info when an item is viewed.

A hint that W4 is not as important as R2 is the durability of w: 0. This write concern means that the application is not going to wait for a confirmation that any nodes completed the write successfully, meaning that the application is not very concerned with its success.

* W6 - user adds item to cart.

These writes are very important, and we will use the appropriate write concern and catch the error conditions in the code. However, they do not appear as frequently as the more important R2 reads, so we shouldn't build our system around these writes.

* R1 - user logs into the application.

This is one of the most imporant operation, because without it being fast, you may lose customers. However there are a lot more views of items, especially at peak time.

* R4 - user views their cart.

This is also another very important operation, as the customer may be ready to commit on purchasing items, however here again, this operation does not happen as often as the views of items.

</details>

<details>

<summary>Question 2 - explanation</summary>



<mark style="color:green;">**Correct Options**</mark>

*   The size of the data in the database is 530 terabytes.

    This is the amount of space needed to keep the data for a one year period.

    10,000,000 cellphones \* 100 bytes/(minute\*cellphone) \* (60 \* 24 \* 365 minutes/year)
*   The peak number is 830,000 writes/second.

    50,000,000 cellphones/minute \* 1 minute/60 seconds
*   The average write rate is 170,000 writes/second.

    10,000,000 cellphones/minute \* 1 minute/60 seconds

**Incorrect Options**

All other provided numbers are incorrect.

</details>

<details>

<summary>Question 3 - explanation</summary>

<mark style="color:green;">**Correct Options**</mark>

* stores.name and stores.city
* stores.name and store\_details.description
*   stores.address and store\_details.manager.name

    Because our entities **stores** and **store\_details** are joined by a One-to-One relationship, it does not matter in which of those two entities the fields are located.

    What matters is that a field is not within an array field, which are our only fields representing One-to-Many relationships.

    All the above options have a One-to-One relationship between the pair of fields.

**Incorrect Options**

* store\_details.name and store\_details.staff.name
* stores.\_id and store\_details.services
* store\_details.manager.employee\_id and store\_details.staff.employee\_id
* stores.name and store\_details.staff.name

</details>

<details>

<summary>Question <code>4</code> - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   A **users** collection with an extended reference to a **restaurants** document.

    Because the main focus of the queries are the **users**, we must have a collection for them.

    And because of GDPR, it is easy to delete a user with all the information about it. If the user information was also located in the restaurant document, we would have to also search for this information and delete it.

    The Extended Reference to the restaurant also allows our application to know the restaurant name without having to do an additional query when we look at a user.

**Incorrect Options**

* Only a **restaurants** collection in which all user information is embedded.
* Only a **users** collection in which all restaurant information is embedded.
* A **restaurants** collection with references to **users** documents.

</details>

<details>

<summary>Question 5 - explanation</summary>

<mark style="color:green;">**Correct Options**</mark>

*   Embedding the airlines that fly to/from a city in each of the corresponding **cities** document and keeping a separate copy of the **airlines** documents.

    This is a good representation. To be able to store an airline that is not associated with any city yet or to be able to resolve issues between the duplicated information about the airlines, it is recommended to keep a source of truth as a separate collection.
*   Referencing the **airlines** documents in each of the corresponding **cities** documents.

    This is a valid representation for a Many-to-Many relationship. This form avoids duplication, however, it may lead to more queries.

**Incorrect Option**

*   Embedding the airlines that fly to/from a city in each of the corresponding **cities** document, without having a separate collection for the airlines.

    This representation is not recommended for a Many-to-Many relationship. Deleting the only parent document in which an airline document is embedded would make the airline vanish too, which may not be the desired behavior.

    Also, because embedding a Many-to-Many relationship creates duplication, keeping a source of truth in an outside collection may prevent issues in case the duplicated information gets out of sync between documents.

</details>

<details>

<summary>Question 6 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   Referencing from the Zillions side.

    This is the preferred representation for a One-to-Zillions relationship.

**Incorrect Options**

*   Embedding as an array.

    Because we are talking about more than a billion people being born in the same country, putting these people in the document for the country would create a gigantic document, exceeding the limitation on the document size.
*   Referencing from the One side.

    Even just keeping the references to people in the document for a given country would create a document exceeding the limitation on the document size.

</details>

<details>

<summary>Question 7 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Schema Versioning Pattern_

    This pattern allows for the application to quickly identify which document structure it is dealing with, the old one or the new. This helps to minimize downtime for the application user, while allowing the database to smoothly transition to the new schema.

**Incorrect Options**

* _The Attribute Pattern_
* _The Bucket Pattern_
* _The Extended Reference Pattern_
* _The Subset Pattern_

</details>

<details>

<summary>Question 8 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Computed Pattern_

    The Computed Pattern allows your application to calculate values at write time. In this case, the sum of the number of views would be calculated in a rolling fashion by book genre.

**Incorrect Options**

* _The Bucket Pattern_
* _The Extended Reference Pattern_
* _The Polymorphic Pattern_
* _The Subset Pattern_

</details>

<details>

<summary>Question 9 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Bucket Pattern_

    The _Bucket_ pattern allows us to record data in hour interval documents, which can then be stored in yearly collections. This makes it easy to store, analyze, and purge the data within the given time requirements.

**Incorrect Options**

* _The Computed Pattern_
* _The Extended Reference Pattern_
* _The Polymorphic Pattern_
* _The Subset Pattern_

</details>

<details>

<summary>Question 10 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Polymorphic Pattern_

    The problem states that bodegas sell a variety of items from different categories, with different purposes and properties. In this case, the Polymorphic Pattern will be the best candidate to catalog this set of goods.

**Incorrect Options**

* _The Bucket Pattern_
* _The Computed Pattern_
* _The Extended Reference Pattern_
* _The Subset Pattern_

</details>

<details>

<summary>Question 11 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Extended Reference Pattern_

    In this case, the Extended Reference Pattern will easily take care of the additional queries our application is making. To implement the pattern we can modify the **inventory** item documents by adding frequently-accessed **order** data directly to them. This will result in lowering the number of related queries on the **orders** collection, since the relevant data about the orders will now be part of the **inventory** item documents.

**Incorrect Options**

* _The Bucket Pattern_
* _The Computed Pattern_
* _The Polymorphic Pattern_
* _The Schema Versioning Pattern_

</details>

<details>

<summary>Question 12 - explanation</summary>

<mark style="color:green;">**Correct Option**</mark>

*   _The Subset Pattern_

    In this case, it seems that the application is slowing down because of the amount of data that is being retrieved each time an item is accessed. To eliminate this issue, the Subset Pattern would be a perfect solution. We can store the **Material Safety Data Sheets** in a separate collection and only access them when it is necessary.

**Incorrect Options**

* _The Bucket Pattern_
* _The Computed Pattern_
* _The Extended Reference Pattern_
* _The Polymorphic Pattern_

</details>

> Written by

{% embed url="https://github.com/LakHyeonKim" %}
