# aem-mongodb-replica

This repo is used to share sample configurations for implementing Aodbe Experience Manager with Mongodb.

## When to use MongoDB with AEM

MongoDB will typically be used for supporting AEM author deployments where one of the following criteria is met:

    More than 1000 unique users per day;
    More than 100 concurrent users;
    High volumes of page edits;
    Large rollouts or activations.

The criteria above are only for the author instances and not for any publish instances which should all be TarMK based. The number of users refers to authenticated users, as author instances do not allow unauthenticated access.

If the criteria are not met, then a TarMK active/standby deployment is recommended to address availability. Generally, MongoDB should be considered in situations where the scaling requirements are more than what can be achieved with a single item of hardware.



## Prerequisites
* cq-quickstart-6.3.0.jar
* licence.properties (Adobe Licence)
* Mongodb binary

## Configuration
- Create a direcorty named 'aem-mongodb-replica'

- Copy 'q-quickstart-6.3.0.jar' and 'licence.properties' to the directory

- Download Mongodb binary from https://www.mongodb.com/download-center#community
  or 
  copy from installed directory (bin)

  eg:- 'C:\Program Files\MongoDB\Server\3.4'

- Create a folder named 'mongodb' inside the directory 'aem-mongodb-replica'

- Copy the 'bin' directory to the 'mongodb' 
  eg:- C:\Program Files\MongoDB\Server\3.4\bin

- Copy 'mongod.cfg','mongod_arb0','mongod_rp0','mongod_rp1' to the'mongodb' directory

- Create following directory for saving logs
  mongodb/logs/arb0 
  mongodb/logs/rp0 
  mongodb/logs/rp1 

- Create following data directory  
  mongodb/aem6_cluster/arb0
  mongodb/aem6_cluster/node0
  mongodb/aem6_cluster/node1

- Open CMD on 'mongodb/bin' directory and run the following command
  >mongod --config ../mongod_rp0.cfg 

- Open CMD on 'mongodb/bin' directory and run the following command
  >mongod --config ../mongod_rp1.cfg

- Open CMD on 'mongodb/bin' directory and run the following command
  >mongod --config ../mongod_arb0.cfg

- Open CMD on 'mongodb/bin' directory and run the following command
  >mongo
    >rs.initiate(rsconf = {_id: "aem6", members: [{_id: 0, host: "localhost:27017"}]})
    >rs.add("localhost:27018")
    >rs.addArb("localhost:27019")
    >rs.status()

- Open CMD on 'aem-mongodb-replica' and enter the following command
 >java -XX:MaxPermSize=512M  -jar cq-quickstart-6.3.0.jar -p 4502 -r crx3,crx3mongo -Doak.mongo.uri=mongodb://localhost:27017,localhost:27018

## Validation

To test out the fault tolerance, just ctl+c the mongo instance and watch the AEM driver switch to the elected PRIMARY.

Mongo replica sets must have an odd number of members. An arbiter is not required, but it allows you to have nicely divisible replica sets.

- Open CMD on 'mongodb/bin' directory and run the following command
  >mongo
  >rs.status()

  You can see the similar output.
    ```
       ...
        "members" : [

         {

                 "_id" : 0,

                 "name" : "localhost:27017",

                 "health" : 1,

                 "state" : 1,

                 "stateStr" : "PRIMARY",

                 "uptime" : 151,

                 "optime" : {

                         "ts" : Timestamp(1523365971, 1),

                         "t" : NumberLong(11)

                 },

                 "optimeDate" : ISODate("2018-04-10T13:12:51Z"),

                 "infoMessage" : "could not find member to sync from",

                 "electionTime" : Timestamp(1523365862, 1),

                 "electionDate" : ISODate("2018-04-10T13:11:02Z"),

                 "configVersion" : 3,

                 "self" : true

         },

         {

                 "_id" : 1,

                 "name" : "localhost:27018",

                 "health" : 1,

                 "state" : 2,

                 "stateStr" : "SECONDARY",

                 "uptime" : 98,

                 "optime" : {

                         "ts" : Timestamp(1523365971, 1),

                         "t" : NumberLong(11)

                 },

                 "optimeDurable" : {

                         "ts" : Timestamp(1523365971, 1),

                         "t" : NumberLong(11)

                 },

                 "optimeDate" : ISODate("2018-04-10T13:12:51Z"),

                 "optimeDurableDate" : ISODate("2018-04-10T13:12:51Z"),

                 "lastHeartbeat" : ISODate("2018-04-10T13:12:52.526Z"),

                 "lastHeartbeatRecv" : ISODate("2018-04-10T13:12:52.541Z"),

                 "pingMs" : NumberLong(0),

                 "syncingTo" : "localhost:27017",

                 "configVersion" : 3

         },

         {

                 "_id" : 2,

                 "name" : "localhost:27019",

                 "health" : 1,

                 "state" : 7,

                "stateStr" : "ARBITER",

                 "uptime" : 145,

                 "lastHeartbeat" : ISODate("2018-04-10T13:12:52.827Z"),

                 "lastHeartbeatRecv" : ISODate("2018-04-10T13:12:49.103Z"),

                 "pingMs" : NumberLong(0),

                 "configVersion" : 3

         }
    ```
