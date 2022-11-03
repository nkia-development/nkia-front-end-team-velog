---
description: >-
  atlas cluster에 mflix 프로젝트를 연결필요 spring boot 프레임워크로 되어있고 프론트 소스코드는 이미 작성이 다되어있음
  DAO (Data Access Object) 로직만 작성하여 각 티켓의 코드를 답으로 작성하면 된다.
---

# ☝ M220J : MongoDB for Developers

* Atlas cluster dummy data 중 sample\_mflix 데이터베이스 사용
* Mac 에서 5000 포트 사용 중이면 다른 포트로 변경 필요 application.properties 에서 server.port 변경!
* 티켓 발급은 project 실행 후 root path로 접속시 mflix 화면이 뜨고 status 화면에서 가

<details>

<summary>Setting mflix project</summary>

**Download Course Materials**Handouts (1)

* [m220/mflix-java.zip](https://s3.amazonaws.com/edu-downloads.10gen.com/M220J/2022/October/static/handouts/m220/mflix-java.zip)

## Table of Contents

**Setting Up mflix:**

1. Project Structure
2. Local Environment Dependencies
3. Java Project (Mflix) Installation
4. Running the Application
5. Running the Unit Tests

In order to run properly, the Mflix software project has some installation requirements and environmental dependencies.

These requirements and dependencies are defined in this lesson, and they can also be found in the **README.rst** file from the **mflix-java** project, which you will download shortly. This lesson serves as a guide for setting up these necessary tools. After following this README, you should be able to successfully run the Mflix application. First, you will need to download the **mflix-java** project, as described below.

## Download the mflix-java.zip file

You can download the **mflix-java.zip** file by clicking the link in the "Handouts" section of this page. Downloading this handout may take a few minutes. When the download is complete, unzip the file and cd into the project's root directory, **mflix-java**.

```
cd ~/Downloads
unzip mflix-java.zip
cd mflix-java
```

## Project Structure

Mflix is composed of two main components:

* _Frontend_: All the UI functionality is already implemented for you, which includes the built-in React application that you do not need to worry about.
* _Backend_: _Java Spring Boot_ project that provides the necessary service to the application. The code is managed by a Maven project definition file that you will have to load into your Java IDE.

Most of what you will implement is located in the src/main/java/mflix/api/daos directory, which contains all database interfacing methods.

The unit tests in src/test/java/mflix/api/daos will test these database access methods directly, without going through the API.

The UI will run these methods as part of the integration tests, and therefore they are required for the full application to be running.

By default the application will run on port 5000, but if you need it to run on a port other than 5000, you can edit the **index.html** file in the build directory to modify the value of **window.host**. You can find index.html in the src/main/resources/build directory.

We're using _Spring Boot_ for the API. Maven will download this library for you. More on that below.

## Local Environment Dependencies

There are two main system dependencies in this course:

1. Java 1.8

* The Java version this course is built against is Java 1.8. You can download the appropriate version for your operating system by clicking [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

1. Maven

* We use Maven to manage dependencies for the Mflix project. Click here to download [Maven](https://maven.apache.org/download.cgi). You can find detailed installation instructions on [the Apache website](https://maven.apache.org/install.html).

## Java Project (Mflix) Installation

The Mflix project is supported by a _Maven_ POM file that deals with all the dependencies required, as well as providing the test and run commands to control our project. This means that you can run all the tests and deploy the Mflix backend from the command line with _Maven_.

However, we recommend you use a Java IDE to follow along with the lessons and to accomplish the **Tickets** assigned to you in the course.

You can use any IDE that you like, as you do not need to have a specific product to complete the course. It would be better if your IDE supports _Maven POM_ files, so it can set the dependencies correctly, otherwise you will need to download and install manually the different libraries and drivers used by the project.

That said, all the lectures and examples of this course have been produced using IntelliJ IDEA CE edition. You will find a lesson dedicated to setting up and exploring this IDE for the course.

Once you downloaded and unzipped the mflix-java.zip file, you will find the project folder. The project folder contains the application code, the pom.xml file that you would import into your IDE, and the dataset required that you will have to import to Atlas.

```
$ ls
mflix README.rst
$ cd mflix
$ ls src pom.xml data
```

## Running the Application

In the mflix/src/main/resources directory you can find a file called application.properties.

Open this file and enter your _Atlas SRV_ connection string as directed in the comment. This is the information the driver will use to connect. Make sure **not** to wrap your _Atlas SRV_ connection between quotes:

```
spring.mongodb.uri=mongodb+srv://m220student:m220password@<YOUR_CLUSTER_URI>
```

To run Mflix, run the following command:

```
cd mflix
mvn spring-boot:run
```

And then point your browser to [http://localhost:5000/](http://localhost:5000/).

It is recommended you use an IDE for this course. Ensure you choose an IDE that supports importing a Maven project. We recommend IntelliJ [Community](https://www.jetbrains.com/idea/download) but you can use the product of your choice.

The first time running the application might take a little longer due to the initial setup process.

## Running the Unit Tests

To run the unit tests for this course, you will use JUnit. Each course lab contains a module of unit tests that you can call individually with a command like the following:

```
cd mflix
mvn -Dtest=<TestClass> test
```

For example to run the ConnectionTest test your shell command will be:

```
cd mflix
mvn -Dtest=ConnectionTest test
```

Alternatively, if using an IDE, you should be able to run the Unit Tests individually by clicking on a green play button next to them. You will see this demonstrated in the course as we will be using IntelliJ.

Each ticket will contain the command to run that ticket's specific unit tests. When running the Unit Tests or the Application from the shell, make sure that you are in the same directory as the pom.xml file.

</details>



### Ticket 1: Database Connection

<details>

<summary>Explanation</summary>

**Task**

MFlix will use MongoDB as a storage layer, so for this ticket you'll be required to perform some application setup.

1. First, make sure you've created a user on your Atlas cluster with read and write access to any database.

* The user name should be m220student and the password should be m220password
* Don't forget to whitelist your IP address!

1. Copy the connection string by clicking on **Connect** in the Atlas cluster. Select that you'd like to connect with **MongoDB Compass**, and select _Compass 1.12 or later_ - this will give you the **srv** connection string. Make sure this URI string contains your username and password!
2.  Locate the file called **src/main/resources/application.properties** within the **mflix** java project, and replace the information within with your own **mongodb uri srv** connection string, set in property value spring.mongodb.uri:

    ```
    spring.mongodb.uri=mongodb+srv://YOUR_CLUSTER_URI
    ```

*   It's highly recommended you also change the jwtSecret to some very long, very random string. While this application is only meant for local use during this course, software has a strange habit of living a long time:

    ```
    jwtSecret=SUPER_SECRET_KEY_YOU_WANT_TO_REPLACE_THIS
    ```

***

**Testing and Running the Application**

In order to reinforce good development practices, everything asked of you in this course is backed up by unit tests. Reading through the tests for a specific exercise will tell you exactly what is expected.

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=ConnectionTest
```

from the mflix-java/mflix directory, or running the Connection test from your IDE in the src/test/java/mflix/api/daos directory.

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run it from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Connection**?



<mark style="color:green;">**Answer**</mark>&#x20;

```properties
jwtSecret="" # jwt private key
jwtExpirationInMs=604800000 # jwt 만료 기 
spring.mongodb.uri=mongodb+srv://<id>:<pw>@<domain>/<database> # Atlas cluster srv url
spring.mongodb.database=<database> # database 이
server.port=8080 # server port
server.error.whitelabel.enabled=false # 스프링 화이트라벨 에러페이지 비활성
logging.level.api.controllers=DEBUG 
api.movies.movies_per_page=20 # page당 데이터 크
spring.resources.static-locations=classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/build
```



</details>



### Ticket 2: Projection

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I'd like to be able to search movies by country and see a list of movie titles. I should be able to specify a comma-separated list of countries to search multiple countries."

***

**Task**

Implement the **getMoviesByCountry** method in MovieDao.java to search movies by country and use projection to return the title and \_id field. The \_id field will be returned by default.

***

**MFlix Functionality**

Once you complete this ticket, the UI will allow movie searches by one or more countries.

***

**Testing and Running the Application**

Make sure to look at the tests in ProjectionTest.java to understand what is expected.

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=ProjectionTest
```

Or run the tests from your IDE.

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant unit tests, what is the validation code for **Projection**?





<mark style="color:green;">**Answer**</mark>

```
public List<Document> getMoviesByCountry(String... country) {

    Bson queryFilter = Filters.in("countries", country);
    Bson projection = Projections.include("title");
    List<Document> movies = new ArrayList<>();
    moviesCollection
        .find(queryFilter)
        .projection(projection)
        .iterator()
        .forEachRemaining(movies::add);
    return movies;
}
```

</details>



### Ticket 3: Subfield Text Search

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I'd like to be able to search movies by cast members, genre, or perform a text search of the plot summary, full plot, and title."

***

**Task**

For this ticket, you will need to modify the method **getMoviesByCast** in MovieDao.java to allow the following movie search criteria:

* cast: finds all movies that match the cast members of a movie

Already, MovieDao.java has implementation methods that is able to return results for two different types of movie search criteria:

* text : **getMoviesByText** - performs a text search in the movies collection
* genres: **getMoviesByGenre** - finds movies that include any of the wanted genres.

You just need to construct the query that retrieves the movies collection by cast.

A text index was created for you when you restored the collections with **mongorestore**, so these queries will be efficient once they are implemented.

**Hint**

Check the implementation of similar formats of search criteria - the cast query should be similarly sorting **movies** using the sort key.

***

**MFlix Functionality**

Once you complete this ticket, the UI will allow movie searches by criteria of the cast, movie genres, movie title, and plot summary.

***

**Testing and Running the Application**

Make sure to look at the tests in TextAndSubfieldTest.java to understand what is expected.

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=TextAndSubfieldTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Text and Subfield Search**?



<mark style="color:green;">**Answer**</mark>

```java
public List<Document> getMoviesByCast(String sortKey, int limit, int skip,   String... cast) {
  Bson castFilter = null;
  Bson sort = null;
  castFilter = Filters.in("cast", cast);
  sort = Sorts.descending(sortKey);
  List<Document> movies = new ArrayList<>();
  moviesCollection
      .find(castFilter)
      .sort(sort)
      .limit(limit)
      .skip(skip)
      .iterator()
      .forEachRemaining(movies::add);
  return movies;
}
```



</details>



### Ticket 4: Faceted Search

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I want to be able to filter cast search results by one facet, **metacritic** rating."

***

**Task**

For this ticket, you'll be required to modify the **getMoviesCastFaceted** method in MovieDao.java, so the MFlix application can perform faceted searches. You will find a more detailed description of the task as a comment in the MovieDao.java file.

***

**MFlix Functionality**

Once the change is implemented for this ticket, the user interface will reflect this change when you search for cast (e.g. "Tom Hanks"), then additional search parameters will be added as shown below:

![https://university-courses.s3.amazonaws.com/m220/facetedSearchScreenshot.png](https://university-courses.s3.amazonaws.com/m220/facetedSearchScreenshot.png)

_What is a Faceted Search?_

Faceted search is a way of narrowing down search results as search parameters are added. For example, let's say MFlix allows users to filter movies by a rating from 1 to 10, but Kate Winslet has only acted in movies that have a rating of 6 or higher.

If we didn't specify any other search parameters, MFlix would allow us to choose a rating between 1 and 10. But if we first search for Kate Winslet, MFlix would only let us choose a rating between 6 and 10, because none of the movie documents in the result set have a rating below 6.

If you're curious, you can read more about Faceted Search [here](https://en.wikipedia.org/wiki/Faceted\_search).

_Faceted Search in MFlix_

Faceted searches on the MFlix site cannot be supported with the basic search method **getMovies**. For faceted searches, the application must use the Aggregation Framework.

The method **getMoviesCastFaceted** uses the Aggregation Framework, and the individual _stages_ in the pipeline have already been completed. Follow instructions in the MovieDao.java file to add the required stages to the pipeline object.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=FacetedSearchTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Faceted Search**?



<mark style="color:green;">**Answer**</mark>

```
public List<Document> getMoviesCastFaceted(int limit, int skip, String... cast) {
    List<Document> movies = new ArrayList<>();
    String sortKey = "tomatoes.viewer.numReviews";
    Bson skipStage = Aggregates.skip(skip);
    Bson matchStage = Aggregates.match(Filters.in("cast", cast));
    Bson sortStage = Aggregates.sort(Sorts.descending(sortKey));
    Bson limitStage = Aggregates.limit(limit);
    Bson facetStage = buildFacetStage();
    // Using a LinkedList to ensure insertion order
    List<Bson> pipeline = new LinkedList<>();

    pipeline.add(matchStage);
    pipeline.add(sortStage);
    pipeline.add(skipStage);
    pipeline.add(limitStage);
    pipeline.add(facetStage);
    moviesCollection.aggregate(pipeline).iterator().forEachRemaining(movies::add);
    return movies;
}
```

</details>



### Ticket <mark style="color:green;">****</mark>** 5**: Paging

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I'd like to get the next page of results for my query by scrolling down in the main window of the application."

***

**Task**

For this ticket, you'll be required to modify the **getMoviesByGenre** method in MovieDao.java, to allow for paging.

***

**MFlix Functionality**

The UI is already asking for infinite scroll! You may have noticed a message stating "paging not implemented" when scrolling to the bottom of the page.

Once this ticket is completed, this message will go away, and scrolling to the bottom of the page will result in a new page of movies.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=PagingTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Paging**?



<mark style="color:green;">**Answer**</mark>

```
/*
* @param sortKey - sorting key string.
* @param limit - number of documents to be returned.
* @param skip - number of documents to be skipped
* @param genres - genres matching string vargs.
* @return List of matching Document objects.
*/
public List<Document> getMoviesByGenre(String sortKey, int limit, int skip, String... genres) {
    // query filter
    Bson castFilter = Filters.in("genres", genres);
    // sort key
    Bson sort = Sorts.descending(sortKey);
    List<Document> movies = new ArrayList<>();
    moviesCollection
      .find(castFilter)
      .sort(sort)
      .limit(limit)
      .skip(skip)
      .iterator()
      .forEachRemaining(movies::add);
    return movies;
}
```

</details>



### Ticket 6: User Management

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I should be able to register for an account, log in, and logout."

***

**Task**

For this Ticket, you'll be required to implement all the methods in UserDao.java that are marked for the **User Management** ticket. Specifically, you'll implement:

* **createUserSession**
* **getUser**
* **getUserSession**
* **deleteUserSession**
* **deleteUser**

Registering should create an account and log the user in, ensuring an entry is made in the **sessions** collection. There is a [unique index](https://docs.mongodb.com/manual/core/index-unique/?jmp=university) on the user\_id field in **sessions**, so we can efficiently query on this field.

***

**MFlix Functionality**

Once this ticket is completed, users will be able to register for a new account, log in, logout, and delete their account.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=UserTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **User Management**?



<mark style="color:green;">**Answer**</mark>

```
public class UserDao extends AbstractMFlixDao {

  private final MongoCollection<User> usersCollection;
  private final MongoCollection<Session> sessionsCollection;
  private final Logger log;

  @Autowired
  public UserDao(
    MongoClient mongoClient, @Value("${spring.mongodb.database}") String databaseName) {
    super(mongoClient, databaseName);
    CodecRegistry pojoCodecRegistry =
        fromRegistries(
          MongoClientSettings.getDefaultCodecRegistry(),
          fromProviders(PojoCodecProvider.builder().automatic(true).build()));

    usersCollection = db.getCollection("users", User.class).withCodecRegistry(pojoCodecRegistry);
    log = LoggerFactory.getLogger(this.getClass());
    sessionsCollection =
      db.getCollection("sessions", Session.class).withCodecRegistry(pojoCodecRegistry);
    }
  /*
  * @param user - User object to be added
  * @return True if successful, false otherwise.
  */
  public boolean addUser(User user) {
      usersCollection.withWriteConcern(WriteConcern.MAJORITY).insertOne(user);
      return true;
    }

  /*
  * Creates session using userId and jwt token.
  *
  * @param userId - user string identifier
  * @param jwt - jwt string token
  * @return true if successful
  */
  public boolean createUserSession(String userId, String jwt) {
    Bson updateFilter = new Document("user_id", userId);
    Bson setUpdate = Updates.set("jwt", jwt);
    UpdateOptions options = new UpdateOptions().upsert(true);
    sessionsCollection.updateOne(updateFilter, setUpdate, options);
    return true;
    }

  /*
  * Returns the User object matching the an email string value.
  *
  * @param email - email string to be matched.
  * @return User object or null.
  */
  public User getUser(String email) {
    return usersCollection.find(new Document("email", email)).limit(1).first();
  }

  /*
  * Given the userId, returns a Session object.
  *
  * @param userId - user string identifier.
  * @return Session object or null.
  */
  public Session getUserSession(String userId) {
    return sessionsCollection.find(new Document("user_id", userId)).limit(1).first();
    }

  public boolean deleteUserSessions(String userId) {
    Document sessionDeleteFilter = new Document("user_id", userId);
    DeleteResult res = sessionsCollection.deleteOne(sessionDeleteFilter);
    if (res.getDeletedCount() < 1) {
      log.warn("User `{}` could not be found in sessions collection.", userId);
      }

      return res.wasAcknowledged();
    }

  /**
  * Removes the user document that match the provided email.
  *
  * @param email - of the user to be deleted.
  * @return true if user successfully removed
  */
  public boolean deleteUser(String email) {
  // remove user sessions
    if (deleteUserSessions(email)) {
      Document userDeleteFilter = new Document("email", email);
      DeleteResult res = usersCollection.deleteOne(userDeleteFilter);

      if (res.getDeletedCount() < 0) {
        log.warn("User with `email` {} not found. Potential concurrent operation?!");
      }

      return res.wasAcknowledged();
    }
    return false;
  }
}
```

</details>



### Ticket 7: Durable Writes

<details>

<summary>Problem</summary>

For this ticket, you'll be required to increase the durability of the addUser method. Unless you have already changed this method to do so, it should be using the default write concern of w: 1.

When a new user registers for MFlix, their information must be added to the database before they can do anything else. For this reason, we want to make sure that the data written by the addUser method will not be rolled back.

We can decrease the chances of a rollback by increasing the write durability of the addUser method.

Which of the following write concerns are more durable than the default?

</details>

<details>

<summary>Explanation</summary>

**Correct answers:**

WriteConcern.W2 and WriteConcern.MAJORITY

In a 3-node replica set, these two write concerns will both wait until 2 nodes have applied a write. This is because 2 out of 3 nodes is a majority, and waiting for 2 nodes to apply a write is **more durable** than only waiting for 1 node to apply it.

**Incorrect answers:**

WriteConcern.W1

This is already the default write concern in MongoDB, so it does not represent a higher durability than the default.

WriteConcern.UNACKNOWLEDGED

This will not wait for any nodes to apply a write before sending an acknowledgement, so it is a _less_ durable write than the default value of WriteConcern.W1.

**Updated** addUser **method (using** w: majority **):**

```
/**
* @return Success or failure
*/
public boolean addUser(User user) {
    // MongoClient 빈으로 전역 설정 가능    
    usersCollection.withWriteConcern(WriteConcern.MAJORITY).insertOne(user);
    return true;
  }
```

</details>



### Ticket 8: User Preferences

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I want to be able to store preferences such as my favorite cast member and preferred language."

***

**Task**

For this Ticket, you'll be required to implement the **updateUserPreferences** method in UserDao.java. This method allows updates to be made to the "preferences" field in the users collection.

***

**MFlix Functionality**

Once this ticket is completed, users will be able to save preferences in their account information.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=UserPreferencesTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **User Preferences**?\


<mark style="color:green;">**Answer**</mark>

```
public boolean updateUserPreferences(String email, Map<String, ?> userPreferences){

    // make sure to check if userPreferences are not null. If null, return false immediately.
    if(userPreferences == null){
        throw new IncorrectDaoOperation(
          "userPreferences cannot be set to null");
    }
    // create query filter and update object.
    Bson updateFilter = new Document("email", email);
    Bson updateObject = Updates.set("preferences", userPreferences);
    // update one document matching email.
    // replaceOne 사용하면 해당도큐먼트를 대체함 따라서 원하는 결과를 얻을 수 없다
    UpdateResult res = usersCollection.updateOne(updateFilter, updateObject);
    if(res.getModifiedCount() < 1){
        log.warn("User `{}` was not updated. Trying to re-write the same `preferences` field: `{}`",
                email, userPreferences);
    }
    return true;
}
```

</details>



### Ticket 9: Get Comments

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I want to be able to view comments for a movie when I look at the movie detail page."

***

**Task**

For this ticket, you'll be required to extend the **getMovie** method in MovieDao.java so that it also fetches the comments for a given movie.

The comments should be returned in order from most recent to least recent using the date key.

Movie comments are stored in the comments collection, so this task can be accomplished by performing a $lookup. Refer to the Aggregation [Quick Reference](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/?jmp=university#join-conditions-and-uncorrelated-sub-queries) for the specific syntax.

***

**MFlix Functionality**

Once this ticket is completed, each movie's comments will be displayed on that movie's detail page.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=GetCommentsTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Get Comments**?

Hint: We need to sort the comments in the $lookup stage.



<mark style="color:green;">**Answer**</mark>

```
private Bson buildLookupStage(){
  List<Variable<String>> let = new ArrayList<>();
  let.add(new Variable<String>("id", "$_id"));

  // lookup pipeline
  Bson exprMatch = Document.parse("{'$expr': {'$eq': ['$movie_id', '$$id']}}");

  Bson lookupMatch = Aggregates.match(exprMatch);
  List<Bson> lookUpPipeline = new ArrayList<>();
  // lookup sort stage
  Bson sortLookup = Aggregates.sort(Sorts.descending("date"));

  lookUpPipeline.add(lookupMatch);
  lookUpPipeline.add(sortLookup);
  return Aggregates.lookup("comments", let, lookUpPipeline, "comments");
}


public Document getMovie(String movieId){

  if (!validIdValue(movieId)) {
   return null;
 }

 List<Bson> pipeline = new ArrayList<>();
 // match stage to find movie
 Bson match = Aggregates.match(Filters.eq("_id", new ObjectId(movieId)));
 pipeline.add(match);

 // comments lookup stage
 Bson lookup = buildLookupStage();
 if(lookup != null) {
   pipeline.add(lookup);
 }

 Document movie = moviesCollection.aggregate(pipeline)
         .batchSize(1)
         .iterator().tryNext();
 return movie;
}
```

</details>



### Ticket 10: Create/Update Comments

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I want to be able to post comments to a movie page as well as edit my own comments."

***

**Task**

For this ticket, you'll be required to implement the **addComment** and **updateComment** methods in CommentDao.

Ensure that **updateComment** only allows users to update their own comments, and no one else's comments.

***

**MFlix Functionality**

Once this ticket is completed, users will be able to post comments on their favorite (and least favorite) movies, and edit comments they've posted.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=UpdateCreateCommentTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Our launch the Application from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant unit tests, what is the validation code for **Create/Update Comments**?



<mark style="color:green;">**Answer**</mark>

```
/**
 * Adds a new Comment to the collection.
 * The equivalent instruction in the mongo shell would be:
 * <p>
 *     db.comments.insertOne({comment})
 * <p/>
 * @param comment - Comment object.
 * @return Null if the insert fails, otherwise returns the resulting Comment object.
 */
public Comment addComment(Comment comment){

    if ( comment.getId()==null || comment.getId().isEmpty()) {
        throw new IncorrectDaoOperation("Comment objects need to have an id field set.");
    }
     commentCollection.insertOne(comment);
     return comment;
}


/**
 * Updates the comment text matching commentId and user email.
 * This method would be equivalent to running the following mongo shell command:
 * <p>
 *     db.comments.update({_id: commentId}, {$set: { "text": text, date: ISODate() }})
 * <p/>
 * @param commentId - comment id string value.
 * @param text - comment text to be updated.
 * @param email - user email.
 * @return true if successfully updates the comment text.
 */
public boolean updateComment(String commentId, String text, String email){

    Bson filter = Filters.and(
            Filters.eq("email", email),
            Filters.eq("_id", new ObjectId(commentId)));
    Bson update = Updates.combine(Updates.set("text", text),
                                  Updates.set("date", new Date())) ;
    UpdateResult res = commentCollection.updateOne(filter, update);

    if(res.getMatchedCount() > 0){

        if (res.getModifiedCount() != 1){
            log.warn("Comment `{}` text was not updated. Is it the same text?");
        }

        return true;
    }
    log.error("Could not update comment `{}`. Make sure the comment is owned by `{}`",
               commentId, email);
    return false;
}
```

</details>



### Ticket 11: Delete Comments

<details>

<summary>Explanation</summary>

**User Story**

"As a user, I want to be able to delete my own comments."

***

**Task**

For this ticket, you'll be required to modify the **deleteComment** method in CommentDao.java. Ensure the delete operation is limited so only the user can delete their own comments, but not anyone else's comments.

***

**MFlix Functionality**

Once this ticket is completed, users will be able to delete their own comments, but they won't be able to delete anyone else's comments.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=DeleteCommentTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Delete Comments**?



<mark style="color:green;">**Answer**</mark>

```
public boolean deleteComment(String commentId, String email){
      // Create a delete filter that includes the commentId and owner email
      Bson filter = Filters.and(
              Filters.eq("email", email),
              Filters.eq("_id", new ObjectId(commentId))
      );
      // Call deleteOne()
      DeleteResult res = commentCollection.deleteOne(filter);
      // in case the delete count is different than one the document
      // either does not exist or it does not match the email + _id filter.
      if (res.getDeletedCount()!=1){
          log.warn("Not able to delete comment `{}` for user `{}`. User" +
                  " does not own comment or already deleted!",
                  commentId, email);
          return false;
      }
      return true;
  }
```

</details>



### Ticket 12: User Report

<details>

<summary>Explanation</summary>

**User Story**

"As an administrator, I want to be able to view the top 20 users by their number of comments."

***

**Task**

For this ticket, you'll be required to modify the **mostActiveCommenters** method in CommentDao.java. This method produces a report of the 20 most frequent commenters on the MFlix site.

**Hint**

This report is meant to be run from the backend by a manager who is very particular about the accuracy of data. Ensure that the [read concern](https://docs.mongodb.com/manual/reference/read-concern/index.html) used in this read, avoids any potential document rollback.

Remember to add the necessary changes in the pipeline to meet the requirements. More information can be found in the comments of the method.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=UserReportTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **User Report**?



<mark style="color:green;">**Answer**</mark>

```
public List<Critic> mostActiveCommenters(){
    List<Critic> mostActive = new ArrayList<>();

    /**
     * In this method we can use the $sortByCount stage:
     * https://docs.mongodb.com/manual/reference/operator/aggregation/sortByCount/index.html
     * using the $email field expression.
     */
    Bson groupByCountStage = Aggregates.sortByCount("$email");
    // Let's sort descending on the `count` of comments
    Bson sortStage = Aggregates.sort(Sorts.descending("count"));
    // Given that we are required the 20 top users we have to also $limit
    // the resulting list
    Bson limitStage = Aggregates.limit(20);
    // Add the stages to a pipeline
    List<Bson> pipeline = new ArrayList<>();
    pipeline.add(groupByCountStage);
    pipeline.add(sortStage);
    pipeline.add(limitStage);

    // We cannot use the CommentDao class `commentCollection` object
    // since this returns Comment objects.
    // We need to create a new collection instance that returns
    // Critic objects instead.
    // Given that this report is required to be accurate and
    // reliable, we want to guarantee a high level of durability, by
    // ensuring that the majority of nodes in our Replica Set
    // acknowledged all documents for this query. Therefore we will be
    // setting our ReadConcern to "majority"
    // https://docs.mongodb.com/manual/reference/method/cursor.readConcern/
    MongoCollection<Critic> commentCriticCollection =
            this.db.getCollection("comments", Critic.class)
                    .withCodecRegistry(this.pojoCodecRegistry)
                    .withReadConcern(ReadConcern.MAJORITY);

    // And execute the aggregation command output in our collection object.
    commentCriticCollection.aggregate(pipeline).into(mostActive);
    return mostActive;
}
```

</details>



### Ticket 13: Migration

<details>

<summary>Explanation</summary>

**Task**

For this ticket, you'll be completing a script that performs field data type migrations. The script main class implementation is **src/main/java/mflix/Migrator.java**.

Things always change, and a requirement has come down that the lastupdated value in each document of the movies collection needs to be stored as an **ISODate** rather than a **String**.

Apart from the lastupdated field, we also want to clean up the different data types that currently define the field imdb.rating, where in some cases it is set as of **Number** type, and in other cases set as **String**. Given that this field represents a numeric value, this field should be set as a number in all documents.

Complete the script so it updates the values using the [bulk API](http://mongodb.github.io/mongo-java-driver/3.9/driver/tutorials/bulk-writes/).

To perform the migration, run the following command:

```
mvn clean compile exec:java -Dexec.mainClass="mflix.Migrator"
```

or run the Migrator.java class from your IDE.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=MigrationTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Migration**?



<mark style="color:green;">**Answer**</mark>

```
package mflix;

import com.mongodb.bulk.BulkWriteResult;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.*;
import org.bson.Document;
import org.bson.conversions.Bson;

import java.text.DateFormat;
import java.text.MessageFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.List;

public class Migrator {

  /**
   * Creates and UpdateOneModel object for each Document that contains an "imdb.rating" field of
   * non-numerical type into a parsable
   *
   * @param doc - Document object to be updated
   * @return UpdateOneModel operation response object
   */
  private static UpdateOneModel<Document> transformRating(Document doc) {
    try {
      String imdbRating = doc.get("imdb", Document.class).getString("rating");

      if (imdbRating == null) {
        return null;
      }

      int rating = 0;
      if (!"".equals(imdbRating)) {
        rating = Integer.valueOf(imdbRating);
      }
      // Update the document based on his _id field
      return new UpdateOneModel<>(
          Filters.eq("_id", doc.getObjectId("_id")), Updates.set("imdb.rating", rating));
    } catch (NumberFormatException e) {
      System.out.println(
          MessageFormat.format(
              "Could not parse {0} into " + "number: {1}", doc.get("imdb.rating", e.getMessage())));
    }
    return null;
  }

  /**
   * Creates an UpdateOneModel for each Document object field `lastupdated` of type string into an
   * update $set to Date type. db.movies.update({_id: doc._id}, {$set: {lastupdated:
   * ISODate(doc.lastupdated)}})
   *
   * @param doc - Document object to get the date transformation applied to
   * @return UpdateOneModel object or null if no change is required.
   */
  private static UpdateOneModel<Document> transformDates(Document doc, DateFormat dateFormat) {

    String lastUpdated = doc.getString("lastupdated");

    try {
      if (lastUpdated != null) {
        return new UpdateOneModel<>(
            Filters.eq("_id", doc.getObjectId("_id")),
            Updates.set("lastupdated", dateFormat.parse(lastUpdated)));
      }

    } catch (ParseException e) {
      System.out.println(
          MessageFormat.format(
              "String date {0} cannot be parsed using {1} " + "format: {2}",
              lastUpdated, dateFormat, e.getMessage()));
    }

    return null;
  }

  public static void main(String[] args) {

    System.out.println("Dataset cleanup migration");

    // set your MongoDB Cluster connection string
    String mongoUri = "<YOUR ATLAS CLUSTER URI>";

    // instantiate database and collection objects
    MongoDatabase mflix = MongoClients.create(mongoUri).getDatabase("sample_mflix");
    MongoCollection<Document> movies = mflix.getCollection("movies");
    Bson dateStringFilter =null;
    String datePattern = "";
    // use the same filters as expressed in the `MigrationTest` unit test
    // to find all documents that need to be updated
    dateStringFilter = Filters.type("lastupdated", "string");
    // define the string pattern to be parsed
    datePattern = "yyyy-MM-dd HH:mm:ss";
    SimpleDateFormat dateFormat = new SimpleDateFormat(datePattern);

    // create list of bulkWrites to be applied.
    List<WriteModel<Document>> bulkWrites = new ArrayList<>();

    // iterate over the documents and apply the transformations.
    for (Document doc : movies.find(dateStringFilter)) {

      // Apply lastupdate string to date conversion
      WriteModel<Document> updateDate = transformDates(doc, dateFormat);
      if (updateDate != null) {
        bulkWrites.add(updateDate);
      }
    }

    // same filter has the one found in the unit test for the rating field.
    Bson ratingStringFilter = Filters.not(Filters.type("imdb.rating", "number"));

    for (Document doc : movies.find(ratingStringFilter)) {
      // Apply "imdb.rating" string to number conversion
      WriteModel<Document> updateRating = transformRating(doc);
      if (updateRating != null) {
        bulkWrites.add(updateRating);
      }
    }

    // execute the bulk update
    // in this case we don't need the operations to ordered
    BulkWriteOptions bulkWriteOptions = new BulkWriteOptions().ordered(false);
    if (bulkWrites.isEmpty()) {
      System.out.println("Nothing to update!");
      System.exit(0);
    }

    BulkWriteResult bulkResult = movies.bulkWrite(bulkWrites, bulkWriteOptions);
    // output the number of updated documents
    System.out.println(
        MessageFormat.format("Updated {0} documents", bulkResult.getModifiedCount()));
  }
}
```

</details>



### Ticket 14: Connection Pooling

<details>

<summary>Explanation</summary>

**Task**

For this ticket, you'll be required to modify the configuration of option that defines our maxPoolSize in the **application.properties** file, and set the maximum size of the connection pool to **50** connections.

By changing the properties file, the MongoClient should be configured to use no more than 50 connections. Revise the [ConnectionString](http://mongodb.github.io/mongo-java-driver/3.6/javadoc/com/mongodb/ConnectionString.html) java class api.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=ConnectionPoolingTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or deploy the application from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Connection Pooling**?



<mark style="color:green;">**Answer**</mark>

To solve this lab you would need to append to your spring.data.mongodb.uri property the maxPoolSize=50 option.

```
spring.mongodb.uri=mongodb+srv://YOUR_CLUSTER_URI?maxPoolSize=50
```

</details>



### Ticket 15: Handling Timeouts

<details>

<summary>Explanation</summary>

**Task**

For this ticket, you'll be required to modify the connection information for MongoClient to set a write concern timeout of **2500** milliseconds.

The MongoClient in mflix.config.MongoDBConfiguration is initialized in the **mongoClient** bean method. There are a few other details in the [Mongo Client section of the Java Driver documentation](http://mongodb.github.io/mongo-java-driver/3.9/driver/tutorials/connect-to-mongodb/) for your reference.

Aside from the write concern timeout, you are also tasked to set the connectTimeoutMS configuration option to **2000** milliseconds. This option should be set in the connection string. Check [MongoDB URI options reference](https://docs.mongodb.com/manual/reference/connection-string/#urioption.connectTimeoutMS) for more information.

The unit test TimeoutsTest.java will be asserting that these two configuration options are correctly set.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=TimeoutsTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

Or run the Application.java class from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Timeouts**?



<mark style="color:green;">**Answer**</mark>

To complete this lab we could add the wtimeout and maxTimeMS configuration options in our connection uri by setting the spring.mongodb.uri string properties.

```
spring.mongodb.uri=mongodb+srv://m220user:m220password@<YOUR_CLUSTER_HOSTS>/sample_mflix?maxPoolSize=50&wtimeout=2500&connectTimeoutMS=2000
```

Another option would be to set the timeout value in the MongoClient instance in the mflix.config.MongoDBConfiguration class.

```
public MongoClient mongoClient(@Value("${spring.mongodb.uri}") String connectionString) {

      this.connectionString = new ConnectionString(connectionString);

      WriteConcern wc = WriteConcern.MAJORITY.withWTimeout(2500,
              TimeUnit.MILLISECONDS);
      MongoClientSettings settings =
              MongoClientSettings.builder()
                      .applyConnectionString(this.connectionString)
                      .writeConcern(wc)
                      .build();
      mongoClient = MongoClients.create(settings);

      return mongoClient;
}
```

</details>



### Ticket16: Handling Errors

<details>

<summary>Explanation</summary>

**Task**

For this ticket, you'll be required to modify the following methods:

MovieDao.java

* **validIdValue**

CommentDao.java

* **addComment**
* **deleteComment**
* **updateComment**

UserDao.java

* **addUser**
* **createUserSession**
* **deleteUser**
* **updateUserPreferences**

Ensure that all of these methods are more robust and account for potential exceptions when executed.

***

**MFlix Functionality**

Once this ticket is completed, the app will be able to handle incorrect movie id values and various write exceptions without breaking or throwing an error within the application.

***

**Testing and Running the Application**

If the application is already running, **stop the application** and run the unit tests for this ticket by executing the following command:

```
mvn test -Dtest=HandlingErrorsTest
```

Once the unit tests are passing, run the application with:

```
mvn spring-boot:run
```

or run the Application.java from your IDE.

Now proceed to the [status page](http://localhost:5000/status) to run the full suite of integration tests and get your validation code.

To have the application use the changes that you implemented for this ticket, make sure to **restart the application** after you completed those changes. Also, only refresh the status page to see the new results of the tests, after the application has been restarted.

After passing the relevant tests, what is the validation code for **Error Handling**?



<mark style="color:green;">**Answer**</mark>

Here are possible implementations for the methods that required changes.

MovieDao possible validIdValue implementation:

```
private boolean validIdValue(String movieId){
    try{
        new ObjectId(movieId);
    } catch (IllegalArgumentException e){
        // value cannot be transformed into mongodb ObjectID
        return false;
    }
    return true;
}
```

CommentDao possible addComment implementation

```
public Comment addComment(Comment comment) {

  if (comment.getId() == null || comment.getId().isEmpty()) {
    throw new IncorrectDaoOperation("Comment objects need to have an id field set.");
  }

  try {
    commentCollection.insertOne(comment);
    return comment;
  } catch (MongoWriteException e) {
    String errorMessage =
        MessageFormat.format(
            "Error occurred while adding a new Comment `{}`: {}", comment, e.getMessage());
    throw new IncorrectDaoOperation(errorMessage);
  }
}
```

CommentDao possible updateComment implementation

```
public boolean updateComment(String commentId, String text, String email) {

  Bson filter =
      Filters.and(Filters.eq("email", email), Filters.eq("_id", new ObjectId(commentId)));
  Bson update = Updates.combine(Updates.set("text", text), Updates.set("date", new Date()));
  try {

    UpdateResult res = commentCollection.updateOne(filter, update);

    if (res.getMatchedCount() > 0) {

      if (res.getModifiedCount() != 1) {
        log.warn("Comment `{}` text was not updated. Is it the same text?");
      }

      return true;
    }
    log.error(
        "Could not update comment `{}`. Make sure the comment is owned by `{}`",
        commentId,
        email);
    return false;

  } catch (MongoWriteException e) {
    String messageError =
        MessageFormat.format(
            "Error occurred while updating comment `{}`: {}", commentId, e.getMessage());
    throw new IncorrectDaoOperation(messageError);
  }
}
```

CommentDao possible deleteComment implementation

```
public boolean deleteComment(String commentId, String email) {

  Bson filter =
      Filters.and(Filters.eq("email", email), Filters.eq("_id", new ObjectId(commentId)));

  try {
    DeleteResult res = commentCollection.deleteOne(filter);
    if (res.getDeletedCount() != 1) {
      log.warn(
          "Not able to delete comment `{}` for user `{}`. User"
              + " does not own comment or already deleted!",
          commentId,
          email);
      return false;
    }
    return true;
  } catch (MongoWriteException e) {
    String errorMessage =
        MessageFormat.format("Error deleting comment " + "`{}`: {}", commentId, e);
    throw new IncorrectDaoOperation(errorMessage);
  }
}
```

UserDao possible addUser implementation

```
public boolean addUser(User user) {
  try {
    usersCollection.withWriteConcern(WriteConcern.MAJORITY).insertOne(user);
    return true;

  } catch (MongoWriteException e) {
    log.error(
        "Could not insert `{}` into `users` collection: {}", user.getEmail(), e.getMessage());
    throw new IncorrectDaoOperation(
        MessageFormat.format("User with email `{0}` already exists", user.getEmail()));
  }
}
```

UserDao possible createUserSession implementation

```
public boolean createUserSession(String userId, String jwt){
    try{
        Bson updateFilter = new Document("user_id", userId);
        Bson setUpdate = Updates.set("jwt", jwt);
        UpdateOptions options = new UpdateOptions().upsert(true);
        sessionsCollection.updateOne(updateFilter, setUpdate, options);
        return true;
    } catch (MongoWriteException e){
      String errorMessage =
      MessageFormat.format(
          "Unable to $set jwt token in sessions collection: {}", e.getMessage());
      throw new IncorrectDaoOperation(errorMessage, e);
    }
}
```

UserDao possible deleteUser implementation

```
public boolean deleteUser(String email) {
  // remove user sessions
  try {
    if (deleteUserSessions(email)) {
      Document userDeleteFilter = new Document("email", email);
      DeleteResult res = usersCollection.deleteOne(userDeleteFilter);

      if (res.getDeletedCount() < 0) {
        log.warn("User with `email` {} not found. Potential concurrent operation?!");
      }

      return res.wasAcknowledged();
    }
  } catch (Exception e) {
    String errorMessage = MessageFormat.format("Issue caught while trying to " +
          "delete user `{}`: {}",
      email,
      e.getMessage());
    throw new IncorrectDaoOperation(errorMessage);

  }
  return false;
}
```

UserDao possible updateUserPreferences implementation

```
public boolean updateUserPreferences(String email, Map<String, ?> userPreferences) {

  // make sure to check if userPreferences are not null. If null, return false immediately.
  if (userPreferences == null) {
    throw new IncorrectDaoOperation("userPreferences cannot be set to null");
  }
  // create query filter and update object.
  Bson updateFilter = new Document("email", email);
  Bson updateObject = Updates.set("preferences", userPreferences);
  try {
    // update one document matching email.
    UpdateResult res = usersCollection.updateOne(updateFilter, updateObject);
    if (res.getModifiedCount() < 1) {
      log.warn(
          "User `{}` was not updated. Trying to re-write the same `preferences` field: `{}`",
          email,
          userPreferences);
    }
    return true;
  } catch (MongoWriteException e) {
    String errorMessage =
        MessageFormat.format(
            "Issue caught while trying to update user `{}`: {}", email, e.getMessage());
    throw new IncorrectDaoOperation(errorMessage);
  }
}
```

</details>



## Final Exam

<details>

<summary>Question 1 - explanation</summary>

<mark style="color:green;">**Correct Answer:**</mark>

```
Bson query = and(eq("winner_party", "Republican"),
                 gte("winner_electoral_votes", 160));
```

This will find the documents whose winner\_party is Republican, and whose winner\_electoral\_votes is greater than or equal to 160.

**Incorrect Answers:**

```
Bson query = and(eq("winner_party", "Republican"),
                 lt("winner_electoral_votes", 160));
```

This will find all Republican winners with _less than_ 160 electoral votes.

```
Bson query = gte("winner_electoral_votes", 160);
```

This will find all election winners with at least 160 votes, including those who are not Republicans.

```
Bson query = and(eq("winner_party", "Republican"),
                 lte("winner_electoral_votes", 160));
```

This will find all Republican winners with **at most** 160 electoral votes.

```
Bson query = and(gte("total_electoral_votes", 160),
                 eq("winner_party", "Republican"));
```

This will find all Republican winners who ran for election at a time when the total number of electoral votes was **160 or greater**.

</details>

<details>

<summary>Question 2 - explanation</summary>

<mark style="color:green;">**Correct Answer:**</mark>

```
Bson query = lt("software_version", 4.0);
Bson modification = set("needs_to_update", true);
phonesCollection.updateMany(query, modification);
```

This will find all phones with a software\_version below 4.0, and set the needs\_to\_update field to true for those documents.

**Incorrect Answers:**

```
Bson query = lte("software_version", 4.0);
Bson modification = set("needs_to_update", true);
phonesCollection.updateMany(query, modification);
```

This will find all phones with a software\_version below _or equal to_ 4.0, and set the needs\_to\_update field to true for those documents. This means that phones using version 4.0 will be marked as needs\_to\_update.

```
Bson query = lt("software_version", 4.0);
Bson modification = inc("needs_to_update", true);
phonesCollection.updateMany(query, modification);
```

This will actually error out, because the update operator $inc can only increment numeric arguments.

```
Bson query = lt("software_version", 4.0);
Bson modification = set("needs_to_update", true);
phonesCollection.updateOne(query, modification);
```

This will find _one_ phone with a software\_version below 4.0, and set the needs\_to\_update field to true for that one phone. It will stop execution after one update.



```
Bson query = gt("software_version", 4.0);
Bson modification = set("needs_to_update", true);
phonesCollection.updateMany(query, modification);
```

This will find all phones with a software\_version _above_ 4.0, and set the needs\_to\_update field to true for those documents.

</details>

<details>

<summary>Question 3 - explanation</summary>

The variable representing our client, mongoClient, will:

_automatically retry writes that fail._

This is correct.

This MongoClient object will retry writes, because it was initialized with the .retryWrites(true).

_use Write Concern_ { w: majority } _by default._

This is correct.

This MongoClient object will use { w: majority }, because it was initialized a WriteConcern.MAJORITY object .

_use Read Concern_ { r: majority } _by default._

This is incorrect.

This MongoClient object will use Read Concern { r: 1 }, which is the default Read Concern.

_allow a maximum of 50 connections in the connection pool._

This is incorrect.

This MongoClient object will have a maximum connection pool size of **100**, which is the default value.

</details>

<details>

<summary>Question 4 - explanation</summary>

The answer is w: majority.

Sending a write with w: majority will cause MongoDB to wait for the write to be applied by a **majority** of nodes in the set. In a 3-node replica set, a majority is constituted by 2 nodes, so MongoDB will send an acknowledgement back to the client when 2 nodes have applied the write.

</details>

<details>

<summary>Question 5 - explanation</summary>

<mark style="color:green;">**Correct Answers:**</mark>

Inserts of doc1, doc2, and doc3 will succeed.

These writes do not conflict with each other, and they should all succeed if there are no network errors.

**Incorrect Answers:**

Inserts of doc4 and doc5 will not succeed.

doc4 has the same \_id as doc1, and the server will throw a duplicate key error on this insert operation.

The default behavior for bulk writes is an _ordered_ execution of the batch. So because the insert of doc4 fails, the insert of doc5 will not be executed.

</details>

<details>

<summary>Question 6 - explanation</summary>

<mark style="color:green;">**Correct Answer:**</mark>

com.mongodb.MongoTimeoutException is thrown when an operation does not complete within a period of time. Those scenarios include not getting an answer from the server, not finding a _Primary_ to perform a write, etc.

In this case, it is not finding a _Primary_ within 30 seconds. You can change the value of the period through the setting serverSelectionTimeoutMS.

So a MongoTimeoutError Exception would be raised after that time.

**Incorrect Answers:**

* com.mongodb.MongoServerException is not an exception per se, it is a base class for many exceptions. Errors discovered by the MongoDB server, like write exceptions, will be returned to the driver and be thrown by the Java driver as sub-classes of this base Exception.
* com.mongodb.MongoCursorNotFoundException is an example of error that is a subclass of com.mongodb.MongoServerException. An error is reported by the server and then thrown by the client application.
* com.mongodb.MongoConfigurationException is usually thrown at the earlier stage of a connection when invalid configuration options are discovered.
* org.bson.BsonInvalidOperationException is usually thrown when the contents of the document is missing fields or has unexpected types for the provided fields.

[\
](https://university.mongodb.com/mercury/M220J/2022\_October\_18/chapter/Final\_Exam/lesson/5be4bcc4b0d7305eaefd1ec0/problem)

</details>

<details>

<summary>Question 7 - explanation</summary>

<mark style="color:green;">**Correct Answer**</mark>

```
coll.find().sort(orderBy(descending("height"))).skip(3).limit(2)
```

This will sort on height, and then skip the top 3 tallest people to get the 4th- and 5th-tallest people.

**Incorrect Answers**

```
coll.find().sort(orderBy(descending("height"))).limit(5).skip(3)
```

MongoDB will rearrange the order and execute the skip before the limit. So this query, will sort on height, skip the first 3 and them limit to the top 5 tallest people. It will still return 5 documents after the skip.

```
coll.find().sort(orderBy(descending("height"))).skip(3).limit(5)
```

This will sort on height, skip the top 3 tallest people, and then return the 4th, 5th, 6th, 7th and 8th-tallest people.

```
coll.find().sort(orderBy(descending("height"))).skip(5).limit(3)
```

This will sort on height, skip the top 5 tallest people, and then return the 6th, 7th, and 8th-tallest people.

</details>

> Written by

{% embed url="https://github.com/LakHyeonKim" %}
