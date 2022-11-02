# ☝ M320 : MongoDB Data Modeling

## Chapter 2: Relationships

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

### Lab3: Apply the Attribute Pattern

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



### Lab4: Apply the Subset Pattern

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

> Written by

{% embed url="https://github.com/LakHyeonKim" %}
