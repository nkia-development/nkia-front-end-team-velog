---
description: >-
  Lab에서 제공하는 IDE 별도의 경량 os (linux) 이다. 해당 config 파일을 추가, 수정, 삭제를 할 수 있다 (로컬 테스트
  환경이라고 생각하면 됨) mongosh는 없고 mongo, mongod, mongos, mongo-tools는 설치되어있음, 질문에 대한
  mongo 구성을 완료 해야 test가 통과함
---

# ☝ M103 : Basic Cluster Administration

## Chapter 1: The Mongod

### Lab 1: Launching Mongod

<details>

<summary>Explanation</summary>

Launch your own mongod process in the IDE terminal:

1.  From your IDE terminal, launch a mongod instance that listens for connections on port **27000**.

    If you run into any issues, you can always type Ctrl-C in the terminal where mongod is running to stop it. Then you can restart it with a new configuration.

    When the process is running, you can open a new tab in the IDE to use the Mongo shell, but this is optional for this assignment.
2. Once you've run the proper commands, click "Run Tests" to run a suite of tests that will check the configuration of mongod. The results of these tests will let you know which steps you've yet to complete.

\
<mark style="color:green;">**Answer**</mark>

In order to complete this lab, we first must properly configure our mongod. Here is a command that fulfills the requirements of the lab:

```
mongod --port 27000
```

\--port 27000 tells mongod to run on port 27000, which means we have to connect to mongo on that port.

After configuring mongod, we can connect to mongo by specifying a port:

```
mongo --port 27000
```

</details>



### Lab 2: Configuration File

<details>

<summary>Explanation</summary>

Launch a mongod instance in the IDE terminal with a configuration file:

1.  Write the configuration file. There should be an empty configuration file in your IDE File Editor, where you can specify options in YAML.

    As a reminder, here are the requirements of your mongod instance:

    * run on port **27000**
    * authentication is enabled
2.  When your config file is complete, launch mongod with the --config command line option:

    ```
    mongod --config mongod.conf
    ```

    or using the -f option:

    ```
    mongod -f mongod.conf
    ```
3.  Once mongod is running, open a new Terminal window and use the following command to create an admin user. **You will need to create this user in order to validate your work.**

    ```javascript
    mongo admin --host localhost:27000 --eval '
      db.createUser({
        user: "m103-admin",
        pwd: "m103-pass",
        roles: [
          {role: "root", db: "admin"}
        ]
      })
    '
    ```
4. Click "Run Tests" to run a suite of tests that will check the configuration of your mongod. The results of these tests will let you know which steps you've yet to complete.



<mark style="color:green;">**Answer**</mark>

The configuration for this mongod is the same as the previous lab, but the settings should be passed in a configuration file instead of the command line. Here is an example of a valid config file for this lab:

```
net:
  port: 27000
security:
  authorization: enabled
```

</details>



### Lab 3: Change the Default DB Path

<details>

<summary>Explanation</summary>

Use a configuration file to store database files in a new directory:

1.  Create a new folder /var/mongodb/db/:

    ```
    mkdir -p /var/mongodb/db
    ```
2.  Edit your config file to use this new directory as the dbpath.

    Here are the updated requirements for your mongod instance:

    * runs on port **27000**
    * stores its data files in /var/mongodb/db/
    * listens to connections from localhost
    * uses authentication
3. Launch mongod with the new configuration.
4.  Use the following command to connect to the Mongo shell and create the following user. **You will need to create this user in order to validate your work.**

    ```javascript
    mongo admin --host localhost:27000 --eval '
      db.createUser({
        user: "m103-admin",
        pwd: "m103-pass",
        roles: [
          {role: "root", db: "admin"}
        ]
      })
    '
    ```
5. Click "Run Tests" to run a test that will check the updated dbpath.

<mark style="color:green;">**Answer**</mark>

First, we have to create the directory /var/mongodb/db:

```
mkdir -p /var/mongodb/db/
```

Now we can edit our configuration file to use this new directory as the DB path:

```
storage:
  dbPath: /var/mongodb/db/
net:
  bindIp: localhost
  port: 27000
security:
  authorization: enabled
```

Start the mongod process with:

```
mongod -f mongod.conf
```

The process of creating this user is identical to the previous labs, but we have to repeat it for our new DB path:

```javascript
use admin
db.createUser({
  user: "m103-admin",
  pwd: "m103-pass",
  roles: [
    {role: "root", db: "admin"}
  ]
})
```

</details>



### Lab 4: Logging to a Different Facility

<details>

<summary>Explanation</summary>

Use a configuration file to store log files in a new location:

1.  Update your configuration file such that:

    * mongod sends logs to /var/mongodb/logs/mongod.log
    * mongod is forked and run as a daemon (this will not work without specifying logpath)

    You will still have access to the terminal window after launching mongod with these options. For help on forking the mongod process, please refer to the docs on [Managing Mongod Processes](https://docs.mongodb.com/manual/tutorial/manage-mongodb-processes/#start-mongod-as-a-daemon).
2.  Use the following command to connect to the Mongo shell and create the following user. **You will need this user in order to validate your work.**

    COPY

    ```
    mongo admin --host localhost:27000 --eval '
      db.createUser({
        user: "m103-admin",
        pwd: "m103-pass",
        roles: [
          {role: "root", db: "admin"}
        ]
      })
    '
    ```
3. Click "Run Tests" to run a test that will check the updated logpath.



<mark style="color:green;">**Answer**</mark>

There are two different sections to add in the config file in order to complete this lab. The first is the systemLog section:

```
systemLog:
  destination: file
  path: /var/mongodb/logs/mongod.log
  logAppend: true
```

This tells mongod to send logs to a file, and specifies that file as /var/mongodb/logs/mongod.log. logAppend: true tells mongod to append new entries to the existing log file when the mongod instance restarts.

The second section is processManagement:

```
processManagement:
  fork: true
```

This enables a daemon that runs mongod in the background, or "forks" the process. This frees up the terminal window where mongod is launched from.

</details>



### Lab 5: Creating First Application User

<details>

<summary>Explanation</summary>

Create a new user for an application that has the readWrite role:

1.  Connect to a mongod instance that is **already running in the background on port 27000**. You can find the options used to launch mongod in the configuration file in your file editor.

    The m103-admin user has also already been created for you with password m103-pass.
2.  Use the db.createUser() command to create a user for a CRUD application.

    The requirements for this new user are:

    * Role: readWrite on applicationData database
    * Authentication source: admin
    * Username: m103-application-user
    * Password: m103-application-pass
3. Click "Run Tests" to run a suite of tests that will check the configuration of m103-application-user. The results of these tests will let you know which steps you've yet to complete.



<mark style="color:green;">**Answer**</mark>

In order to create this new user, we have to be logged into your MongoDB server as a user with the privilege to create other users. Luckily, the m103-admin user from previous labs has the root role, and therefore has this privilege.

As a reminder, authenticating to MongoDB as this user can be done with the following command:

```
mongo --port 27000 -u "m103-admin" -p "m103-pass" --authenticationDatabase "admin"
```

Once we are logged in, we can create our new user with the following command:

```
use admin
db.createUser({
  user: "m103-application-user",
  pwd: "m103-application-pass",
  roles: [
    {role: "readWrite", db: "applicationData"}
  ]
})
```

Notice that we created our user on the admin database, but we gave that user readWrite access to the applicationData database. These are two separate actions - the user **authenticates** against admin and is **authorized** on applicationData.

</details>



### Lab 6: Importing a Dataset

<details>

<summary>Explanation</summary>

Import a dataset into MongoDB using mongoimport:

1.  Run a mongoimport command on a MongoDB instance running in the background.

    The requirements for this command are:

    * connect to a mongod process running on port **27000**
    * import the data from /dataset/products.json
    * import the data to applicationData.products
    * use m103-application-user to authenticate to the database - this user has already been created for you on the admin database with password m103-application-pass
2. Click "Run Tests" to run a test that will check applicationData.products for the new data. The results of these tests will let you know which steps you've yet to complete.

<mark style="color:green;">**Answer**</mark>

The import can be properly completed with the following command:

```
mongoimport --drop --port 27000 -u "m103-application-user" \
-p "m103-application-pass" --authenticationDatabase "admin" \
--db applicationData --collection products /dataset/products.json
```

We authenticate to the database the same way with mongoimport as we did with mongo. The flags --db and --collection are used to designate a target database and collection for our data. The flag --drop is used to drop the existing collection, so we don't create duplicates by running this command twice.

</details>



## Chapter 2: Replication

### Lab 1: Deploy a Replica Set

<details>

<summary>Explanation</summary>

Launch a replica set with three members:

1.  There are three configuration files in your IDE workspace, but they are incomplete.

    Update these configuration files so that all three mongod processes:

    * authenticate internally using the keyfile /var/mongodb/pki/m103-keyfile
    * belong to the replica set m103-repl
2. Once your configuration files are complete, start a mongod process using **one** of them.
3. When this node is running, use the mongo shell to connect and initiate your replica set with rs.initiate(). The shell prompt will read PRIMARY when the election is complete.
4.  Because the replica set uses a keyfile for internal authentication, clients must authenticate before performing any actions.

    While still connected to the primary node, create the admin user for your replica set:

    ```javascript
    db.createUser({
      user: "m103-admin",
      pwd: "m103-pass",
      roles: [
        {role: "root", db: "admin"}
      ]
    })
    ```
5. Once m103-admin is created, exit the mongo shell and start the other two mongod processes with their respective configuration files.
6. Reconnect to your primary node as m103-admin and add the other two nodes to your replica set using rs.add().
7. Once your other two members have been successfully added, run rs.status() to check that the members array has three nodes - one labeled PRIMARY and two labeled SECONDARY.
8. Click "Run Tests" to run a suite of tests that will check the configuration of your replica set. The results of these tests will let you know which steps you've yet to complete.



<mark style="color:green;">**Answer**</mark>

1. The following options need to be included in **each** configuration file:
   *   _Enable replication for replica set_ m103-repl

       ```
       replication:
         replSetName: m103-repl
       ```
   *   _Use_ m103-keyfile _for internal authentication_

       ```
       security:
          keyFile: /var/mongodb/pki/m103-keyfile
       ```
2.  Once each configuration file has been updated, use them to start 3 mongod processes:

    ```
    mongod -f mongod_1.conf
    mongod -f mongod_2.conf
    mongod -f mongod_3.conf
    ```
3.  Connect to one of the mongod processes, initiate replica set, and create the m103-admin user:

    ```
    mongo --port 27001 -eval 'rs.initiate()'

    mongo admin --port 27001 -eval 'db.createUser({
      user: "m103-admin",
      pwd: "m103-pass",
      roles: [
        {role: "root", db: "admin"}
      ]
    })'
    ```
4.  Connect to the mongod process where you ran rs.initiate() and add the other two nodes:

    ```
    mongo admin --host m103-repl/localhost:27001 \
                --username m103-admin --password m103-pass \
                --eval 'rs.add("localhost:27002")
                        rs.add("localhost:27003")'
    ```

    We should receive a response that says { "ok" : 1 } from both of these rs.add() commands.
5.  Now, running rs.status() should give us a members list with three healthy nodes:

    ```
    mongo admin --host m103-repl/localhost:27001 \
                --username m103-admin --password m103-pass \
                --eval 'rs.status()'
    ```

</details>



### Lab 2: Reconfigure a Replica Set

* priority = 0 이면 primary 선출 후보에 있을 수 없
* 아래 4-node를 분석용 서버로 두는 이유는 실제 클러스터에 분석쿼리를 날리면 운영 중 과한 쿼리로 성능에 영향을 줄 수 있기 때문에 따로 노드를 붙여 사

<details>

<summary>Explanation</summary>

A **4-node** replica set has already been launched. You can find the configuration files used to start each server in the IDE.

Reconfigure the replica set to make one node more suitable for analytics queries:

1.  Connect to the replica set m103-repl and retrieve the configuration of the node localhost:27004. This is the node you will reconfigure.

    Remember to authenticate as m103-admin with password m103-pass.
2.  Store this configuration in a variable and use this variable to update the following fields in the configuration document for localhost:27004:

    * the number of votes should be 0
    * the hidden field should be true
    * the priority should be 0

    **You should not need to update the configuration files or restart mongod.**
3. Reconfigure the replica set using your new configuration document.
4. Once you've run the proper commands, click "Run Tests" to run a suite of tests that will check the configuration of m103-repl. The results of these tests will let you know which steps you've yet to complete.

<mark style="color:green;">**Answer**</mark>

We can't make this change by updating the config files of our mongod instances, sop we have to do this update with rs.reconfig().

1.  Connect to the replica set from the Mongo shell - remember to authenticate as m103-admin:

    ```
    mongo --host m103-repl/localhost:27001 \
          --username m103-admin --password m103-pass
    ```
2.  Store the replica set configuration in a variable from the Mongo shell:

    ```
    cfg = rs.conf()
    ```
3.  Then update the configuration of localhost:27004 from the Mongo shell:

    ```
    cfg.members[3].votes = 0
    cfg.members[3].hidden = true
    cfg.members[3].priority = 0
    ```
4.  Apply the new configuration to m103-repl from the Mongo shell:

    ```
    rs.reconfig(cfg)
    ```

Once you run rs.reconfig(), the new replica set configuration will take effect.

</details>



### Lab 3: Writes with Failovers

<details>

<summary>Problem</summary>

Evaluate the effect of using a write concern with a replica set where one node has failed.

Consider a 3-node replica set with only 2 healthy nodes, that receives the following insert() operation:

```javascript
use payroll
db.employees.insert(
  { "name": "Aditya", "salary_USD": 50000 },
  { "writeConcern": { "w": 3, "wtimeout": 1000 } }
)
```

Which of the following is true about this write operation?

</details>

<details>

<summary>Explanation</summary>

_When a_ writeConcernError _occurs, the document is still written to the healthy nodes._

This is correct.

The WriteResult object simply tells us whether the writeConcern was successful or not - it will not undo successful writes from any of the nodes.

_The unhealthy node will have the inserted document when it is brought back online._

This is correct.

When the unhealthy node comes back online, it rejoins the replica set and its oplog is compared with the other nodes' oplogs. Any missing operations will be replayed on the newly healthy node.

w: "majority" _would also cause this write operation to return with an error._

w: "majority" requests acknowledgement that a _majority_ of nodes in a replica set have registered the write. In a three-node replica set, only two nodes are required for a majority, so the two healthy nodes are sufficient to satisfy this writeConcern.

_The write operation will always return with an error, even if_ wtimeout _is not specified._

If wtimeout is not specified, the write operation will be retried for an indefinite amount of time until the writeConcern is successful. If the writeConcern is impossible, like in this example, it may never return anything to the client.

</details>



### Lab 4: Read Preferences

<details>

<summary>Problem</summary>

Consider a 3-node replica set that experiences a network outage.

Two of the three nodes were unreachable during the outage, leaving one node remaining.

Which of these readPreferences will allow you to read data from this node?

</details>

<details>

<summary>Explanation</summary>

The key concept to understand here is that when two nodes go down in a three-node replica set, the third node becomes a secondary regardless of whether it started as a primary.

Therefore, connecting to the third node is the same as connecting to a secondary node, and any readPreference will work except for primary, which requires all operations to read from the primary node.

</details>



## Chapter 3: Sharding

### Lab 1: Deploy a Sharded Cluster

<details>

<summary>Explanation</summary>



Deploy a sharded cluster with 1 shard.

Some components have already been configured for you:

* the Configuration Server Replica Set csrs is running on csrs/localhost:27004,localhost:27005,localhost:27006
* a replica set shard1 is running on shard1/localhost:27001,localhost:27002,localhost:27003
* the m103-admin user has been created on both replica sets with password m103-pass

**You will only need to edit the \`\`mongos\`\` configuration file.**

1.  Start up a mongos process to provide an interface to your sharded cluster. You can find an unfinished configuration file for mongos in the IDE file browser.

    The configuration servers should already be running on the csrs replica set. Remember that mongos inherits database users from the CSRS.
2. Add shard1 as the first shard in the cluster.
3. Once you've run the proper commands, click "Run Tests" to run a suite of tests that will check the configuration of mongos. The results of these tests will let you know which steps you've yet to complete.



<mark style="color:green;">**Answer**</mark>

1.  Update the configuration of mongos so it knows where the configuration servers are located:

    ```
    sharding:
      configDB: csrs/localhost:27004
    ```
2.  Once this section has been added to the mongos configuration, start up the process:

    ```
    mongos -f mongos.conf
    ```
3.  When mongos boots up for the first time, it inherits database users from the CSRS. Authenticate to mongos using the m103-admin user:

    ```
    mongo --port 26000 --username m103-admin --password m103-pass
    ```
4.  Add shard1 as the first shard:

    ```javascript
    sh.addShard("shard1/localhost:27001")
    ```

    Once the shard has been successfully added, it will be reflected in the output of sh.status().

</details>



### Lab 2: Shard a Collection

<details>

<summary>Explanation</summary>

Import and shard a collection of data on your cluster:

1. Use mongoimport to import the data in /dataset/products.json:
   * import this dataset onto m103.products
   * use mongos as the target for mongoimport - you can find the configuration details for this process in mongos.conf
   * authenticate to mongos as m103-admin (with password m103-pass)
2.  Enable sharding on the m103 database.

    Two shards have already been added to your cluster, shard1 and shard2. For more information, run sh.status() on mongos.
3.  Choose a shard key for the m103.products collection.

    Review the qualities of a good shard key in the [docs](https://docs.mongodb.com/manual/core/ranged-sharding/) and the following information about the products collection:

    * \_id is a serial number for each product in this collection, rarely used in queries but important for internal MongoDB usage
    * sku (Stock Keeping Unit) is a randomly generated integer unique to each product - this is commonly used to refer to specific products when updating stock quantities
    * name is the name of the product as it appears in the store and on the website
    * type is the type of product, with the possible values "Bundle", "Movie", "Music" and "Software"
    * regularPrice is the regular price of the product, when there is no sale this price changes every season
    * salePrice is the price of a product during a sale - this price changes arbitrarily based on when sales occur
    * shippingWeight is the weight of the product in kilograms, ranging between 0.01 and 1.00 - this value is not known for every product in the collection
4. Create an index on your shard key and shard the collection.
5.  Once you've run the proper commands, click "Run Tests" to run a suite of tests that will check the configuration of your sharded cluster. The results of these tests will let you know which steps you've yet to complete.

    If you chose the wrong shard key, clicking "Run Tests" will give you an error. However, if you already imported the dataset, you must drop the collection:

    ```javascript
    use m103
    db.products.drop()
    ```

    Then reimport the dataset, and shard it using a different key.

\
<mark style="color:green;">**Answer**</mark>

1.  Import data onto mongos:

    ```
    mongoimport /dataset/products.json --port 26000 -u "m103-admin" \
    -p "m103-pass" --authenticationDatabase "admin" \
    --db m103 --collection products
    ```
2.  Enable sharding on the m103 database:

    ```javascript
    sh.enableSharding("m103")
    ```
3.  Choose a shard key:

    We can look at all potential shard keys with findOne():

    ```javascript
    use m103
    db.products.findOne()
    ```

    The output of this command should give us something like this:

    ```javascript
    {
      "_id" : ObjectId("573f706ff29313caab7d7395"),
      "sku" : 1000000749,
      "name" : "Gods And Heroes: Rome Rising - Windows [Digital Download]",
      "type" : "Software",
      "regularPrice" : 39.95,
      "salePrice" : 39.95,
      "shippingWeight" : "0.01"
    }
    ```

    One way to determine the correct shard key is process of elimination. We can rule out the potential shard keys which don't follow the rules of cardinality, frequency, rate of change, and query patterns.

    We can rule out \_id because it is rarely used in queries, and we would therefore be wasting an index by sharding on it. In addition, it is monotonically increasing, so it will continue to increase forever and cause hotspotting in our cluster.

    We can rule out type because this field does not have high cardinality. In fact, it only has four possible values - we can see this by running the following command on m103.products:

    > ```javascript
    > db.products.distinct("type")
    > ```

    We can rule out regularPrice and salePrice because they are both subject to change and the shard key is immutable. If we sharded on one of these fields, any future updates to that field would result in an error.

    We can rule out shippingWeight because every document in the collection must have the shard key, and not every document here has a shippingWeight.

    From this, we have only two good shard keys:

    * name
    * sku

    Both of these fields have **high cardinality**, **low frequency** and **non-monotonically increasing values**. They are also commonly used in queries.

    The validation script will accept either solution.
4.  Then, we must create an index on the shard key (in this example, name):

    ```javascript
    db.products.createIndex({ "name": 1 })
    ```
5.  Then shard the collection using the chosen shard key:

    ```javascript
    db.adminCommand({ "shardCollection": "m103.products", "key": { "name": 1 } })
    ```

To choose a different shard key, the collection must be dropped and the dataset must be reimported.

From the mongos shell, we can drop the products collection with the following command:

```javascript
use m103
db.products.drop()
```

Now we exit the mongos shell and reimport the dataset:

```
mongoimport /dataset/products.json --port 26000 -u "m103-admin" \
-p "m103-pass" --authenticationDatabase "admin" \
--db m103 --collection products
```

Now we can shard the collection again.

</details>



### Lab 3: Documents in Chunks

<details>

<summary>Problem</summary>

Consider the following document:

```
{
  "_id" : ObjectId("573f7197f29313caab89b3a4"),
  "sku" : 20005012,
  "name" : "Complete Hit Singles A's & B's - CD",
  "type" : "Music",
  "regularPrice" : 14.99,
  "salePrice" : 14.99,
  "shippingWeight" : "0.25"
}
```

Which of the following chunks would contain this document?

</details>

<details>

<summary>Explanation</summary>

```javascript
{
  "_id" : "m103.products-sku_20000000",
  "shard" : "shard2",
  "min" : {
    "sku" : 20000000
  },
  "max" : {
    "sku" : 25000000
  }
}
```

This is correct - { sku: 20005012 } falls into this chunk's range.

```javascript
{
  "_id" : "m103.products-sku_MinKey",
  "shard" : "shard1",
  "min" : {
    "sku" : 0
  },
  "max" : {
    "sku" : 5000000
  }
}
```

This is incorrect - { sku: 20005012 } is outside this chunk's range.

```javascript
{
  "_id" : "m103.products-sku_5000000",
  "shard" : "shard1",
  "min" : {
    "sku" : 5000000
  },
  "max" : {
    "sku" : 10000000
  }
}
```

This is incorrect - { sku: 20005012 } is outside this chunk's range.

```javascript
{
  "_id" : "m103.products-sku_10000000",
  "shard" : "shard1",
  "min" : {
    "sku" : 10000000
  },
  "max" : {
    "sku" : 15000000
  }
}
```

This is incorrect - { sku: 20005012 } is outside this chunk's range.

```javascript
{
  "_id" : "m103.products-sku_15000000",
  "shard" : "shard2",
  "min" : {
    "sku" : 15000000
  },
  "max" : {
    "sku" : 20000000
  }
}
```

This is incorrect - { sku: 20005012 } is outside this chunk's range.

</details>



### Lab 4: Detect Scatter Gather Queries

<details>

<summary>Problem</summary>

Which of the following is **required** in order for a query to be targeted to a subset of shards?

</details>

<details>

<summary>Explanation</summary>

**The query uses the shard key**

This is correct - in order for a query to be targeted to a subset of shards, the query must use the shard key. This is because the data itself is divided on the shard key, so without that parameter the server cannot locate data without doing a Scatter Gather query.

**An index exists on the shard key**

This is correct - in order for a query to be targeted to a subset of shards, an index must exist on the shard key. This is required before the collection can be sharded.

**The shards are running on same data center**

This is incorrect - shards are designed such that they can be distributed throughout the world.

</details>



## Final Exam

<details>

<summary>Question 1 - explanation</summary>

The correct answers are:

*

    ```
    mongod --logpath /var/log/mongo/mongod.log --dbpath /data/db --fork
    ```
*

    ```
    mongod -f /etc/mongod.conf
    ```

The following choices are incorrect:

*

    ```
    mongod --dbpath /data/db --fork
    ```

This is incorrect because a --logpath must be specified in order to fork the process.

*

    ```
    mongod --log /var/log/mongo/mongod.log --authentication
    ```

This is incorrect because both --log and --authentication are invalid flags - instead, they should say --logpath and --auth.

</details>

<details>

<summary>Question 2 - explanation</summary>

/data/db/

This answer is <mark style="color:green;">**correct**</mark><mark style="color:green;">.</mark>

MongoDB must be able to access the data directory /data/db/.

/var/log/

This answer is <mark style="color:green;">**correct**</mark><mark style="color:green;">.</mark>

MongoDB must be able to access the log file mongod.log in [\`\`](https://university.mongodb.com/mercury/M103/2022\_October\_18/chapter/Final\_Exam/lesson/5aa921ef1d9bd78ce17f6db0/answer#id1)/var/log/[\`](https://university.mongodb.com/mercury/M103/2022\_October\_18/chapter/Final\_Exam/lesson/5aa921ef1d9bd78ce17f6db0/answer#id3).

/var/pki/

This answer is <mark style="color:green;">**correct**</mark><mark style="color:green;">.</mark>

MongoDB must be able to access the keyfile keyfile in /var/pki/.

/tmp/

This answer is **incorrect**.

We've been asked to disregard this directory.

</details>

<details>

<summary>Question 3 - explanation</summary>

The correct answer is <mark style="color:green;">**5**</mark><mark style="color:green;">.</mark>

Because this is failover, the node that was primary is now unreachable. We can also see from the output that the member with \_id: 4 is also unreachable. That means 2 of the 7 nodes were unavailable, and that leaves us with 5 nodes eligible to become primary.

All other answers are incorrect. While the replica set is configured with 7 members, looking closely at the output we can see that one member is unreachable, specifically the member with \_id: 4.

</details>

<details>

<summary>Question 4 - explanation</summary>

<mark style="color:green;">**Correct Answers**</mark>

* You cannot specify the same host information among multiple members.

Host information must be unique for each member in a replica set.

* You cannot specify two members with the same \_id.

\_id must be unique for each member in a replica set.

**Incorrect Answers**

* You can only specify a priority of 0 or 1, member "\_id": 0 is incorrectly configured.

Replica set members can have priority greater than 1, and this will increase the likelihood that a member becomes primary during an election (however, it cannot guarantee a node will become primary).

* You cannot have three members in a replica set.

Replica sets can have any number of members, although an odd number is recommended.

</details>

<details>

<summary>Question 5 - explanation</summary>

The correct answer is:

* It serves as a "hot" backup of data in case of accidental data loss on the other members, like a DBA accidentally dropping the database.

The node with "\_id": 3 is delayed by 1 hour. So if a DBA drops a database, that database will be dropped from all secondary nodes _without_ a delay, but it will still be present on the node with "\_id": 3 for 1 hour.

All other answers are incorrect.

</details>

<details>

<summary>Question 6 - explanation</summary>

The correct answers are:

*

    ```
    db.customers.find({"country": "Norway", "_id": 54})
    ```

This specifies both indexes used in the shard key.

*

    ```
    db.customers.find({"country": { $gte: "Portugal", $lte: "Spain" }})
    ```

This specifies a prefix of the indexes used in the shard key, **"country"**, and will be routed to shards containing the necessary information.

*

    ```
    db.customers.find({"_id": 914, "country": "Sweden"})
    ```

Although the indexes are specified in reverse order, this is a routed query. Any document matching {"\_id": 914, "country": "Sweden"} **must** be identical to {"country": "Sweden", "\_id": 914}. The query planner will take advantage of this and reorder the fields.

The incorrect answer is:

*

    ```
    db.customers.find({"_id": 455})
    ```

Because the neither a prefix nor the full shard key is provided, mongos has no way to determine how to appropriately route this query. Instead, it will send this query to all shards in the cluster in a _scatter-gather_ operation.

</details>

> Written by

{% embed url="https://github.com/LakHyeonKim" %}
