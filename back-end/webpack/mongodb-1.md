# ☝ M121 : Aggreation Framework

## Chapter 1: Basic Aggregation - $match and $project

### Lab 1: Changing Document Shape with $project

<details>

<summary>Problem</summary>

Our first movie night was a success. Unfortunately, our ISP called to let us know we're close to our bandwidth quota, but we need another movie recommendation!

Using the same $match stage from the previous lab, add a $project stage to only display the title and film rating (title and rated fields).

* Assign the results to a variable called pipeline.

```javascript
var pipeline = [{ $match: {. . .} }, { $project: { . . . } }]
```

* Load validateLab2.js which was included in the same handout as validateLab1.js and execute validateLab2(pipeline)?

```javascript
load('./validateLab2.js')
```

* And run the validateLab2 validation method

```javascript
validateLab2(pipeline)
```

What is the answer?

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**15**</mark>

Remember that when using $project to be selective on which fields you pass further, the only field you must specify to remove is \_id. When you specify a field to retain (title: 1), $project assumes that all other fields you haven't specified to retain should be removed.

```javascript
var pipeline = [
  {
    $match: {
      "imdb.rating": { $gte: 7 },
      genres: { $nin: [ "Crime", "Horror" ] } ,
      rated: { $in: ["PG", "G" ] },
      languages: { $all: [ "English", "Japanese" ] }
    }
  },
  {
    $project: { _id: 0, title: 1, "rated": 1 }
  }
]
```

</details>



### Lab 2: Computing Fields

<details>

<summary>Problem</summary>

Our movies dataset has a lot of different documents, some with more convoluted titles than others. If we'd like to analyze our collection to find movie titles that are composed of only one word, we **could** fetch all the movies in the dataset and do some processing in a client application, but the Aggregation Framework allows us to do this on the server!

Using the Aggregation Framework, find a count of the number of movies that have a title composed of one word. To clarify, "Cinderella" and "3-25" should count, where as "Cast Away" would not.

Make sure you look into the [_$split_ String expression ](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#string-expressions)and the [_$size_ Array expression](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#array-expressions)

To get the count, you can append itcount() to the end of your pipeline

```javascript
db.movies.aggregate([...]).itcount()
```

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**8066**</mark>

```javascript
db.movies.aggregate([
  {
    $match: {
      title: {
        $type: "string"
      }
    }
  },
  {
    $project: {
      title: { $split: ["$title", " "] },
      _id: 0
    }
  },
  {
    $match: {
      title: { $size: 1 }
    }
  }
]).itcount()
```

We begin with a $match stage, ensuring that we only allow movies where the title is a string

```javascript
db.movies.aggregate([
  {
    $match: {
      title: {
        $type: "string"
      }
    }
  },
```

Next is our $project stage, splitting the title on spaces. This creates an array of strings

```javascript
{
  $project: {
    title: { $split: ["$title", " "] },
    _id: 0
  }
},
```

We use another $match stage to filter down to documents that only have one element in the newly computed title field, and use itcount() to get a count

```javascript
  {
    $match: {
      title: { $size: 1 }
    }
  }
]).itcount()
```

</details>



## Chapter 2: Basic Aggregation - Utility Stages

### Lab 1: Using Cursor-like Stages

<details>

<summary>Problem</summary>

MongoDB has another movie night scheduled. This time, we polled employees for their favorite actress or actor, and got these results

```javascript
favorites = [
  "Sandra Bullock",
  "Tom Hanks",
  "Julia Roberts",
  "Kevin Spacey",
  "George Clooney"]
```

For movies released in the **USA** with a tomatoes.viewer.rating greater than or equal to 3, calculate a new field called num\_favs that represets how many **favorites** appear in the cast field of the movie.

Sort your results by num\_favs, tomatoes.viewer.rating, and title, all in descending order.

What is the title of the **25th** film in the aggregation result?

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**The Heat**</mark>

```javascript
var favorites = [
  "Sandra Bullock",
  "Tom Hanks",
  "Julia Roberts",
  "Kevin Spacey",
  "George Clooney"]

db.movies.aggregate([
  {
    $match: {
      "tomatoes.viewer.rating": { $gte: 3 },
      countries: "USA",
      cast: {
        $in: favorites
      }
    }
  },
  {
    $project: {
      _id: 0,
      title: 1,
      "tomatoes.viewer.rating": 1,
      num_favs: {
        $size: {
          $setIntersection: [
            "$cast",
            favorites
          ]
        }
      }
    }
  },
  {
    $sort: { num_favs: -1, "tomatoes.viewer.rating": -1, title: -1 }
  },
  {
    $skip: 24
  },
  {
    $limit: 1
  }
])
```

We store our favorites in a variable for easy reference within the pipeline

```javascript
var favorites = [
 "Sandra Bullock",
 "Tom Hanks",
 "Julia Roberts",
 "Kevin Spacey",
 "George Clooney"]
```

We start by matching films that include at least one of our favorites in their cast

```javascript
{
  $match: {
    "tomatoes.viewer.rating": { $gte: 3 },
    countries: "USA",
    cast: {
      $in: favorites
    }
  }
}
```

Then, we will be projecting the num\_favs value by calculating the $size of the array intersection, between the given set of favorites and the film cast:

```javascript
{
  $project: {
    _id: 0,
    title: 1,
    "tomatoes.viewer.rating": 1,
    starPower: {
      $size: {
        $setIntersection: favorites
      }
    }
  }
}
```

After that, we call the $sort stage and $skip + $limit in the result to the element requested:

```javascript
  {
    $sort: { num_favs: -1, "tomatoes.viewer.rating": -1, title: -1 }
  },
  {
    $skip: 24
  },
  {
    $limit: 1
  }
])
```

</details>

###

### Lab 2: Bringing it all together

<details>

<summary>Problem</summary>

Calculate an average rating for each movie in our collection where English is an available language, the minimum imdb.rating is at least 1, the minimum imdb.votes is at least 1, and it was released in **1990** or after. You'll be required to [rescale (or _normalize_) ](https://en.wikipedia.org/wiki/Feature\_scaling)imdb.votes. The formula to rescale imdb.votes and calculate normalized\_rating is included as a handout.

What film has the lowest normalized\_rating?

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**The Christmas Tree**</mark>

One possible solution is below.

```javascript
db.movies.aggregate([
  {
    $match: {
      year: { $gte: 1990 },
      languages: { $in: ["English"] },
      "imdb.votes": { $gte: 1 },
      "imdb.rating": { $gte: 1 }
    }
  },
  {
    $project: {
      _id: 0,
      title: 1,
      "imdb.rating": 1,
      "imdb.votes": 1,
      normalized_rating: {
        $avg: [
          "$imdb.rating",
          {
            $add: [
              1,
              {
                $multiply: [
                  9,
                  {
                    $divide: [
                      { $subtract: ["$imdb.votes", 5] },
                      { $subtract: [1521105, 5] }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  },
  { $sort: { normalized_rating: 1 } },
  { $limit: 1 }
])
```

We start by applying the $match filtering:

```javascript
{
  $match: {
    year: { $gte: 1990 },
    languages: { $in: ["English"] },
    "imdb.votes": { $gte: 1 },
    "imdb.rating": { $gte: 1 }
  }
}
```

And within the $project stage we apply the scaling and normalizating calculations:

```javascript
{
  $project: {
    _id: 0,
    title: 1,
    "imdb.rating": 1,
    "imdb.votes": 1,
    normalized_rating: {
      $avg: [
        "$imdb.rating",
        {
          $add: [
            1,
            {
              $multiply: [
                9,
                {
                  $divide: [
                    { $subtract: ["$imdb.votes", 5] },
                    { $subtract: [1521105, 5] }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  }
},
```

in a new computed field normalized\_rating.

The first element of the result, after sorting by normalized\_rating is **The Christmas Tree**, the expected correct answer.

</details>



## Chapter 3: Core Aggregation - Combining Information

### Lab 1: $group and Accumulators

<details>

<summary>Problem</summary>

In the last lab, we calculated a normalized rating that required us to know what the minimum and maximum values for imdb.votes were. These values were found using the $group stage!

For all films that won at least 1 Oscar, calculate the standard deviation, highest, lowest, and average imdb.rating. Use the **sample** standard deviation expression.

**HINT** - All movies in the collection that won an Oscar begin with a string resembling one of the following in their awards field

```
Won 13 Oscars
Won 1 Oscar
```

Select the correct answer from the choices below. Numbers are truncated to 4 decimal places.

</details>

<details>

<summary>Explanation</summary>

```javascript
{ 
  "highest_rating" : 9.2, 
  "lowest_rating" : 4.5, 
  "average_rating" : 7.5270, 
  "deviation" : 0.5988 
}
```

```javascript
db.movies.aggregate([
  {
    $match: {
      awards: /Won \d{1,2} Oscars?/
    }
  },
  {
    $group: {
      _id: null,
      highest_rating: { $max: "$imdb.rating" },
      lowest_rating: { $min: "$imdb.rating" },
      average_rating: { $avg: "$imdb.rating" },
      deviation: { $stdDevSamp: "$imdb.rating" }
    }
  }
])
```

We start by applying the now familiar $match filtering, searching documents for the appropriate text stating they won an Oscar

```javascript
{
  $match: {
    awards: /Won \d{1,2} Oscars?/
  }
},
```

Next, we have our $group stage. By convention, we group all documents together by specifying null\` to \`\`\_id. We use the [group accumulators ](https://docs.mongodb.com/manual/reference/operator/aggregation-group/#group-accumulator-operators)$min, $max, $avg, and $stdDevSamp to get our results

```javascript
{
  $group: {
    _id: null,
    highest_rating: { $max: "$imdb.rating" },
    lowest_rating: { $min: "$imdb.rating" },
    average_rating: { $avg: "$imdb.rating" },
    deviation: { $stdDevSamp: "$imdb.rating" }
  }
}
```

</details>



### Lab 2: $unwind

<details>

<summary>Problem</summary>

Let's use our increasing knowledge of the Aggregation Framework to explore our movies collection in more detail. We'd like to calculate how many movies every **cast** member has been in and get an average imdb.rating for each cast member.

What is the name, number of movies, and average rating (truncated to one decimal) for the cast member that has been in the most number of movies with **English** as an available language?

Provide the input in the following order and format

```javascript
{ "_id": "First Last", "numFilms": 1, "average": 1.1 }
```

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">`{ "_id" : "John Wayne", "numFilms" : 107, "average" : 6.4 }`</mark>



The solution we used is below.

```javascript
db.movies.aggregate([
  {
    $match: {
      languages: "English"
    }
  },
  {
    $project: { _id: 0, cast: 1, "imdb.rating": 1 }
  },
  {
    $unwind: "$cast"
  },
  {
    $group: {
      _id: "$cast",
      numFilms: { $sum: 1 },
      average: { $avg: "$imdb.rating" }
    }
  },
  {
    $project: {
      numFilms: 1,
      average: {
        $divide: [{ $trunc: { $multiply: ["$average", 10] } }, 10]
      }
    }
  },
  {
    $sort: { numFilms: -1 }
  },
  {
    $limit: 1
  }
])
```

We start with a familiar $match stage, looking for movies that include "English" as a language

```javascript
{
  $match: {
    languages: "English"
  }
},
```

Next, we use a $project stage, keeping only the data necessary for the aggregation stages that follow

```javascript
{
  $project: { _id: 0, cast: 1, "imdb.rating": 1 }
}
```

$unwind follows next, extracting every entry in the **cast** array and creating a document for each one

```javascript
{
  $unwind: "$cast"
}
```

Our $group stage groups cast members together by their name, totals the number of documents, and gets the average **imdb.rating**

```javascript
{
  $group: {
    _id: "$cast",
    numFilms: { $sum: 1 },
    average: { $avg: "$imdb.rating" }
  }
}
```

We then use a $project stage to truncate the imdb.rating to one decimal. This is done by first multiplying by 10, truncating the number, then dividing by 10

```javascript
{
  $project: {
    numFilms: 1,
    average: {
      $divide: [
        { $trunc: { $multiply: ["$average", 10] } }
        , 10
      ]
    }
  }
}
```

Lastly, we $sort in descending order so the result with the greatest number of movies comes first, and then $limit our result to 1 document, giving the expected answer

```javascript
{ "_id" : "John Wayne", "numFilms" : 107, "average" : 6.4 }
```

</details>



### Lab 3: Using $lookup

<details>

<summary>Problem</summary>

Which alliance from air\_alliances flies the most **routes** with either a Boeing 747 or an Airbus A380 (abbreviated 747 and 380 in air\_routes)?

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**SkyTeam**</mark>

```javascript
db.air_routes.aggregate([
  {
    $match: {
      airplane: /747|380/
    }
  },
  {
    $lookup: {
      from: "air_alliances",
      foreignField: "airlines",
      localField: "airline.name",
      as: "alliance"
    }
  },
  {
    $unwind: "$alliance"
  },
  {
    $group: {
      _id: "$alliance.name",
      count: { $sum: 1 }
    }
  },
  {
    $sort: { count: -1 }
  }
])
```

We begin by aggregating over our air\_routes collection to allow for filtering of documents containing the string "747" or "380". If we started from air\_alliances we would have to do this **after** the lookup!

```javascript
{
  $match: {
    airplane: /747|380/
  }
},
```

Next, we use the $lookup stage to match documents from air\_alliances on the value of their airlines field against the current document's airline.name field

```javascript
{
  $lookup: {
    from: "air_alliances",
    foreignField: "airlines",
    localField: "airline.name",
    as: "alliance"
  }
},
```

We then use $unwind on the alliance field we created in $lookup, creating a document with each entry in alliance

```javascript
{
  $unwind: "$alliance"
},
```

We end with a $group and $sort stage, grouping on the name of the alliance and counting how many times it appeared

```javascript
{
  $group: {
    _id: "$alliance.name",
    count: { $sum: 1 }
  }
},
{
  $sort: { count: -1 }
}
```

This produces the following output

```javascript
{ "_id" : "SkyTeam", "count" : 16 }
{ "_id" : "OneWorld", "count" : 15 }
{ "_id" : "Star Alliance", "count" : 11 }
```

</details>



### Lab 4: $graphLookup

<details>

<summary>Problem</summary>

Now that you have been introduced to $graphLookup, let's use it to solve an interesting need. You are working for a travel agency and would like to find routes for a client! For this exercise, we'll be using the **air\_airlines**, **air\_alliances**, and **air\_routes** collections in the **aggregations** database.

*   The **air\_airlines** collection will use the following schema:

    ```javascript
    {
        "_id" : ObjectId("56e9b497732b6122f8790280"),
        "airline" : 4,
        "name" : "2 Sqn No 1 Elementary Flying Training School",
        "alias" : "",
        "iata" : "WYT",
        "icao" : "",
        "active" : "N",
        "country" : "United Kingdom",
        "base" : "HGH"
    }
    ```
*   The **air\_routes** collection will use this schema:

    ```javascript
    {
        "_id" : ObjectId("56e9b39b732b6122f877fa31"),
        "airline" : {
                "id" : 410,
                "name" : "Aerocondor",
                "alias" : "2B",
                "iata" : "ARD"
        },
        "src_airport" : "CEK",
        "dst_airport" : "KZN",
        "codeshare" : "",
        "stops" : 0,
        "airplane" : "CR2"
    }
    ```
*   Finally, the **air\_alliances** collection will show the airlines that are in each alliance, with this schema:

    ```javascript
    {
        "_id" : ObjectId("581288b9f374076da2e36fe5"),
        "name" : "Star Alliance",
        "airlines" : [
                "Air Canada",
                "Adria Airways",
                "Avianca",
                "Scandinavian Airlines",
                "All Nippon Airways",
                "Brussels Airlines",
                "Shenzhen Airlines",
                "Air China",
                "Air New Zealand",
                "Asiana Airlines",
                "Copa Airlines",
                "Croatia Airlines",
                "EgyptAir",
                "TAP Portugal",
                "United Airlines",
                "Turkish Airlines",
                "Swiss International Air Lines",
                "Lufthansa",
                "EVA Air",
                "South African Airways",
                "Singapore Airlines"
        ]
    }
    ```

Determine the approach that satisfies the following question in the most efficient manner:

Find the list of all possible distinct destinations, with at most one layover, departing from the base airports of airlines from Germany, Spain or Canada that are part of the "OneWorld" alliance. Include both the destination and which airline services that location. As a small hint, you should find **158** destinations.

Select the correct pipeline from the following set of options:

</details>

<details>

<summary>Explanation</summary>

```javascript
db.air_alliances.aggregate([{
  $match: { name: "OneWorld" }
}, {
  $graphLookup: {
    startWith: "$airlines",
    from: "air_airlines",
    connectFromField: "name",
    connectToField: "name",
    as: "airlines",
    maxDepth: 0,
    restrictSearchWithMatch: {
      country: { $in: ["Germany", "Spain", "Canada"] }
    }
  }
}, {
  $graphLookup: {
    startWith: "$airlines.base",
    from: "air_routes",
    connectFromField: "dst_airport",
    connectToField: "src_airport",
    as: "connections",
    maxDepth: 1
  }
}, {
  $project: {
    validAirlines: "$airlines.name",
    "connections.dst_airport": 1,
    "connections.airline.name": 1
  }
},
{ $unwind: "$connections" },
{
  $project: {
    isValid: { $in: ["$connections.airline.name", "$validAirlines"] },
    "connections.dst_airport": 1
  }
},
{ $match: { isValid: true } },
{ $group: { _id: "$connections.dst_airport" } }
])
```

For this lab the correct answer would be

```javascript
db.air_alliances.aggregate([
  {
    $match: { name: "OneWorld" }
  },
  {
    $graphLookup: {
      startWith: "$airlines",
      from: "air_airlines",
      connectFromField: "name",
      connectToField: "name",
      as: "airlines",
      maxDepth: 0,
      restrictSearchWithMatch: {
        country: { $in: ["Germany", "Spain", "Canada"] }
      }
    }
  },
  {
    $graphLookup: {
      startWith: "$airlines.base",
      from: "air_routes",
      connectFromField: "dst_airport",
      connectToField: "src_airport",
      as: "connections",
      maxDepth: 1
    }
  },
  {
    $project: {
      validAirlines: "$airlines.name",
      "connections.dst_airport": 1,
      "connections.airline.name": 1
    }
  },
  { $unwind: "$connections" },
  {
    $project: {
      isValid: {
        $in: ["$connections.airline.name", "$validAirlines"]
      },
      "connections.dst_airport": 1
    }
  },
  { $match: { isValid: true } },
  {
    $group: {
      _id: "$connections.dst_airport"
    }
  }
])
```

This pipeline takes the most selective collection first, **air\_alliances**, matching the document refering to the _OneWorld_ alliance.

```javascript
db.air_alliances.aggregate([
{
  $match: { name: "OneWorld" }
}
```

It then iterates, with maxDepth 0 on the **air\_airlines** collection to collect the details on the airlines, specially their base airport, but restricting that $lookup to airlines of the requested countries _\[Spain, Germany, Canada]_, using restrictSearchWithMatch.

```javascript
{
  $graphLookup: {
    startWith: "$airlines",
    from: "air_airlines",
    connectFromField: "name",
    connectToField: "name",
    as: "airlines",
    maxDepth: 0,
    restrictSearchWithMatch: {
      country: { $in: ["Germany", "Spain", "Canada"] }
    }
  }
}
```

We then iterate over all routes up to maximum of one layover by setting our maxDepth to 1. We find all possible destinations when departing from the _base_ airport of each carrier by specify **$airlines.base** in startWith

```javascript
{
  $graphLookup: {
    startWith: "$airlines.base",
    from: "air_routes",
    connectFromField: "dst_airport",
    connectToField: "src_airport",
    as: "connections",
    maxDepth: 1
  }
}
```

We now have a document with a field named **connections** that is an array of all routes that are within 1 layover. We use a $project here to remove unnecessary information from the documents. We also need to include information about valid airlines that match our initial restriction and the name of the current airline.

```javascript
{
  $project: {
    validAirlines: "$airlines.name",
    "connections.dst_airport": 1,
    "connections.airline.name": 1
  }
}
```

After this, we'll unwind our **connections** array, and then use $project to add a field representing whether this particular route is valid, meaning it is a route flown by one of our desired carriers.

```javascript
{ $unwind: "$connections" },
{
  $project: {
    isValid: {
      $in: ["$connections.airline.name", "$validAirlines"]
    },
    "connections.dst_airport": 1
  }
}
```

Lastly, we use $match to filter out invalid routes, and then $group them on the destination.

```javascript
{ $match: { isValid: true } },
{
  $group: {
    _id: "$connections.dst_airport"
  }
}
```

An important aspect to this pipeline is that the first $graphLookup will act as a regular $lookup since we are setting a maxDepth to zero. The reason why we are taking this approach is due to the match restriction that $graphLookup allows, which can make this stage more efficient. Think back to the earlier lab on $lookup, can you think of a way to simplify the aggregation using $graphLookup instead?

</details>



## Chapter 4: Core Aggregation - Multidimensional Grouping

### Lab 1: $facets

<details>

<summary>Problem</summary>

How many movies are in both the top ten highest rated movies according to the imdb.rating and the metacritic fields? We should get these results with exactly one access to the database.

**Hint:** What is the _intersection_?

</details>

<details>

<summary>Explanation</summary>

<mark style="color:green;">**1**</mark>

The solution we used follows, following the requirement that we use only one database access

```javascript
db.movies.aggregate([
  {
    $match: {
      metacritic: { $gte: 0 },
      "imdb.rating": { $gte: 0 }
    }
  },
  {
    $project: {
      _id: 0,
      metacritic: 1,
      imdb: 1,
      title: 1
    }
  },
  {
    $facet: {
      top_metacritic: [
        {
          $sort: {
            metacritic: -1,
            title: 1
          }
        },
        {
          $limit: 10
        },
        {
          $project: {
            title: 1
          }
        }
      ],
      top_imdb: [
        {
          $sort: {
            "imdb.rating": -1,
            title: 1
          }
        },
        {
          $limit: 10
        },
        {
          $project: {
            title: 1
          }
        }
      ]
    }
  },
  {
    $project: {
      movies_in_both: {
        $setIntersection: ["$top_metacritic", "$top_imdb"]
      }
    }
  }
])
```

We begin with a $match and $project stage to only look at documents with the relevant fields, and project away needless information

```javascript
{
  $match: {
    metacritic: { $gte: 0 },
    "imdb.rating": { $gte: 0 }
  }
},
{
  $project: {
    _id: 0,
    metacritic: 1,
    imdb: 1,
    title: 1
  }
},
```

Next follows our $facet stage. Within each facet, we need sort in descending order for metacritic and imdb.ratting and ascending for title, limit to 10 documents, then only retain the title

```javascript
{
  $facet: {
    top_metacritic: [
      {
        $sort: {
          metacritic: -1,
          title: 1
        }
      },
      {
        $limit: 10
      },
      {
        $project: {
          title: 1
        }
      }
    ],
    top_imdb: [
      {
        $sort: {
          "imdb.rating": -1,
          title: 1
        }
      },
      {
        $limit: 10
      },
      {
        $project: { title: 1 }
      }
    ]
  }
},
```

Lastly, we use a $project stage to find the intersection of top\_metacritic and top\_imdb, producing the titles of movies in both categories

```javascript
{
  $project: {
    movies_in_both: {
      $setIntersection: ["$top_metacritic", "$top_imdb"]
    }
  }
}
```

This results in the following output

```javascript
{ "movies_in_both" : [ { "title" : "The Godfather" } ] }
```



</details>



## Final Exam

<details>

<summary>Question 1 - explanation</summary>

The correct statements are the following:

* **Pipeline 3** fails because $indexStats must be the first stage in a pipeline and may not be used within a $facet

$indexStats must be the first stage in an aggregation pipeline and cannot be used within a $facet stage.

* **Pipeline 1** fails since $out is required to be the last stage of the pipeline

$out is required to be the last stage of the pipeline.

* **Pipeline 2** is incorrect because $geoNear needs to be the first stage of our pipeline

$geoNear is required to be the first stage of a pipeline.

All other statements are incorrect.

</details>

<details>

<summary>Question <strong>2</strong> - explanation</summary>

The correct answers are the following:

* **Pipeline 1** is incorrect because you cannot use an accumulator expression on $match stage.

We cannot use accumulator expressions within $match. Only query expressions are allowed within $match

* **Pipeline 3** is correct and will execute with no error

This is correct. Although we may argue that $ROOT variable is totally unnecessary, since \_id field will be projected by default from the first $project stage of this pipeline, there are no observable errors with the use of this expression variable

* **Pipeline 2** fails because $divide operator only supports numeric types

This is true, $divide operator will only supports expressions that represent numeric value types.

All the other statements are not true.

</details>

<details>

<summary>Question 3 - explanation</summary>

The correct pipeline is the following:

```javascript
var pipeline = [{
    "$project": {
      "surname_capital": { "$substr": [{"$arrayElemAt": [ {"$split": [ "$name", " " ] }, 1]}, 0, 1 ] },
      "name_size": {  "$add" : [{"$strLenCP": "$name"}, -1]},
      "name": 1
    }
  },
  {
    "$group": {
      "_id": "$name_size",
      "word": { "$push": "$surname_capital" },
      "names": {"$push": "$name"}
    }
  },
  {
    "$project": {
      "word": {
        "$reduce": {
          "input": "$word",
          "initialValue": "",
          "in": { "$concat": ["$$value", "$$this"] }
        }
      },
      "names": 1
    }
  },
  {
    "$sort": { "_id": 1}
  }
]
```

For this lab we picked the first letter of each person surname, surname\_capital, by splitting the name into an array

```javascript
{"$split": [ "$name", " " ] }
```

And by gathering the first letter of the surname using $substr and $arrayElemAt:

```javascript
{ "$substr": [{"$arrayElemAt": [ {"$split": [ "$name", " " ] }, 1]}, 0, 1 ] }
```

We've also captured the number of all alphanumeric characters of the name field, except " ":

```javascript
"name_size": {  "$add" : [{"$strLenCP": "$name"}, -1]}
```

After grouping all first capital letters into word array, and all name into names values by the name\_size:

```javascript
{
  "$group": {
    "_id": "$name_size",
    "word": { "$push": "$surname_capital" },
    "names": {"$push": "$name"}
  }
},
```

We then $reduced the resulting word array into a single string:

```javascript
{
  "$project": {
    "word": {
      "$reduce": {
        "input": "$word",
        "initialValue": "",
        "in": { "$concat": ["$$value", "$$this"] }
      }
    },
    "names": 1
  }
}

And finally sort the result:
```

```javascript
{
  "$sort": { "_id": 1}
}
```

</details>

<details>

<summary>Question 4 - explanation</summary>

The following options are not true:

* a $multiply expression takes a document as input, not an array.

This is not true, a $multiply expression does take as input an array of expressions.

* a $type expression does not take a string as its value; only the BSON numeric values can be specified to identify the types.

We can use either the numeric BSON representation, as well as a string alias to evaluate a field type.

* $sortByCount cannot be used within $facet stage.

$facet does accept $sortByCount as a sub-pipeline stage.

The correct answers, that reflect problems with the pipeline, are the following:

* can not nest a $facet stage as a sub-pipeline.

This is correct. $facet does not accept all sub-pipelines that include other $facet stages

* facet\_2 uses the output of a parallel sub-pipeline, facet\_1, to compute an expression

Each sub-pipeline are completely independent of one another. The output of one sub-pipeline cannot be used as the input for different sub-pipelines.

</details>

<details>

<summary>Question 5 - explanation</summary>

The correct answer is the following:

```javascript
var pipeline = [
    {"$match": { "country": "USA"}},
    {"$addFields": { "mean": {"$avg": "$sunnydays"}}},
    {"$match": { "mean": {"$gte": 220}, "sunnydays": {"$not": {"$lt": 200 }}}},
    {"$sort": {"city": 1}}
]
```

In this case, we try to remove as much data as possible upfront, all cities not matching the right country, using the available index.

We then calculate the mean number of sunny days.

The $match stage then filters out documents where the mean isn't greater than or equal to 220, and there are no entries in the **sunnydays** vector less than 200.

We are left with a sort in memory, however the number should be small enough to not take much resources. There are 285 cities with 100,000 habitants in the USA, and some are likely not to match the number of sunny days criteria.

Another answer provides the desired results, but will not improve the performance as much:

```javascript
var pipeline = [
  {"$sort": {"city": 1}},
  {"$addFields": { "min": {"$min": "$sunnydays"}}},
  {"$addFields": { "mean": {"$avg": "$sunnydays" }}},
  {"$match": { "country": "USA", "min": {"$gte": 200}, "mean": {"$gte": 220}}}
]
```

The above approach uses the index to sort, however it performs an unnecessary calculation to get the minimum value within **sunnydays**. Because the $match stage did not come prior to these $addFields stages, all source documents will pass through them, a wasteful computation.

The pipeline:

```javascript
var pipeline = [
    {"$sort": {"city": 1}},
    {"$addFields": { "min": {"$min": "$sunnydays"}}},
    {"$match": { "country": "USA", "min": {"$gte": 200}}}
]
```

does not satisfy the query requirements.

The last 2 queries are doing a $match on mean before it is calculated, making them also invalid.

</details>

<details>

<summary>Question 6 - explanation</summary>

The correct answer is:

```javascript
var pipeline = [
  {
    "$sort": {"name": 1}
  },
  {
    "$project": {"name":1,
    "phone": {
      "$concat": [
        {"$arrayElemAt": [{"$split": ["$phone", " "]}, 0]} ,
        "*********"  ]
      },
    "ssn": {
      "$concat": [
        "********",
        {"$arrayElemAt": [{"$split": ["$ssn", "-"]}, 2]}
      ]
    }
  }
}
];
db.createView("people_contacts", "people", pipeline);
```

**people\_contacts** view was created using an initial $sort stage. We can see this when comparing the find results between **people** collection and the view.

After sorting the results the **people\_contacts** presents the documents with two computed (redacted) fields, phone and ssn.

```javascript
{
  "$project": {"name":1,
  "phone": {
    "$concat": [
      {"$arrayElemAt": [{"$split": ["$phone", " "]}, 0]} ,
      "*********"  ]
    },
  "ssn": {
    "$concat": [
      "********",
      {"$arrayElemAt": [{"$split": ["$ssn", "-"]}, 2]}
    ]
  }
}
```

And finally, to create the view using command createView

```javascript
db.createView("people_contacts", "people", pipeline);
```

All other options are incorrect, either because they do not use the correct pipeline or due to the fact that the view creation command is incorrect.

</details>

<details>

<summary>Question 7 - explanation</summary>

The correct answer is **OneWorld, with 5 carriers**

A pipeline that can be used to get these results is

```javascript
db.air_routes.aggregate([
  {
    $match: {
      src_airport: { $in: ["LHR", "JFK"] },
      dst_airport: { $in: ["LHR", "JFK"] }
    }
  },
  {
    $lookup: {
      from: "air_alliances",
      foreignField: "airlines",
      localField: "airline.name",
      as: "alliance"
    }
  },
  {
    $match: { alliance: { $ne: [] } }
  },
  {
    $addFields: {
      alliance: { $arrayElemAt: ["$alliance.name", 0] }
    }
  },
  {
    $group: {
      _id: "$airline.id",
      alliance: { $first: "$alliance" }
    }
  },
  {
    $sortByCount: "$alliance"
  }
])
```

We begin with a $match stage and fetch routes that originate or end at either **LHR** and **JFK**

```javascript
{
  $match: {
    src_airport: { $in: ["LHR", "JFK"] },
    dst_airport: { $in: ["LHR", "JFK"] }
  }
},
```

We then $lookup into the air\_alliances collection, matching member airline names in the airlines field to the local airline.name field in the route

```javascript
{
  $lookup: {
    from: "air_alliances",
    foreignField: "airlines",
    localField: "airline.name",
    as: "alliance"
  }
},
```

We follow with a $match stage to remove routes that are not members of an alliance. We use $addFields to cast just the name of the alliance and extract a single element in one go

```javascript
{
  $addFields: {
    alliance: { $arrayElemAt: ["$alliance.name", 0] }
  }
},
```

Lastly, we $group on the airline.id, since we don't want to count the same airline twice. We take the $first alliance name to avoid duplicates. Then, we use $sortByCount to get our answer from the results

```javascript
{
  $group: {
    _id: "$airline.id",
    alliance: { $first: "$alliance" }
  }
},
{
  $sortByCount: "$alliance"
}
```

This produces the following output

```javascript
{ "_id": "OneWorld", "count": 5 }
{ "_id": "SkyTeam", "count": 2 }
```

</details>



> Written by

{% embed url="https://github.com/LakHyeonKim" %}

