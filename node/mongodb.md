mongodb, node, nodejs, data, database, db, object, document

https://mongodb.github.io/node-mongodb-native/api-articles/nodekoarticle1.html

# Install

Install on mac
    brew install mongodb

In the project directory, create a data directory

    mkdir -p mongo/data

Copy and edit the configuration file

    cp /usr/local/etc/mongod.conf .

Adjust  paths

    systemLog:
      destination: file
      path: mongo/system.log
      logAppend: true
    storage:
      dbPath: mongo/data
    net:
      bindIp: 127.0.0.1

Start Mongo DB

     mongod --config mongod.conf

# Connect and setup

# CRUD
