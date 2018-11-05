# MongoDB Cluster Demo
MongoDB Cluster Demo


## Launch servers

```sh
$> docker-compose up -d
```

## Create default network

```sh
$> docker network create --driver=bridge --subnet=192.168.200.0/24 --gateway=192.168.200.1 local-network
```

## Setup MongoDB Cluster

### Init config server

```sh
$> docker exec -it mongo-config1 bash -c "echo 'rs.initiate({_id: \"mongoconf\",configsvr: true, members: [{ _id : 0, host : \"mongo-config1\" }, { _id : 1, host : \"mongo-config2\" }]})' | mongo"

$> docker exec -it mongo-config1 bash -c "echo 'rs.status()' | mongo"
```

### Init shard1 servers

```sh
$> docker exec -it mongo-shard1-node1 bash -c "echo 'rs.initiate({_id : \"shard1\", members: [{ _id : 0, host : \"mongo-shard1-node1\" },{ _id : 1, host : \"mongo-shard1-node2\" },{ _id : 2, host : \"mongo-shard1-node3\" }]})' | mongo"

$> docker exec -it mongo-shard1-node1 bash -c "echo 'rs.status()' | mongo"
```

### Init shard2 servers

```sh
$> docker exec -it mongo-shard2-node1 bash -c "echo 'rs.initiate({_id : \"shard2\", members: [{ _id : 0, host : \"mongo-shard2-node1\" },{ _id : 1, host : \"mongo-shard2-node2\" },{ _id : 2, host : \"mongo-shard2-node3\" }]})' | mongo"

$> docker exec -it mongo-shard2-node1 bash -c "echo 'rs.status()' | mongo"
```

### Init router server

```sh
$> docker exec -ti mongo-router1 bash -c "echo 'sh.addShard(\"shard1/mongo-shard1-node1\")' | mongo"

$> docker exec -ti mongo-router1 bash -c "echo 'sh.addShard(\"shard2/mongo-shard2-node1\")' | mongo"

$> docker exec -it mongo-router1 bash -c "echo 'sh.status()' | mongo"
```

## Create DB & Collection

### Create DB

```sh
$> docker exec -it mongo-router1 bash -c "echo 'use user' | mongo"

$> docker exec -it mongo-router1 bash -c "echo 'sh.enableSharding(\"user\")' | mongo"
```

### Create Collection

```sh
$> docker exec -it mongo-router1 bash -c "echo 'db.createCollection(\"user.people\")' | mongo"

$> docker exec -it mongo-router1 bash -c "echo 'sh.shardCollection(\"user.people\", {\"_id\" : \"hashed\"})' | mongo"
```

### Check

```sh
$> docker exec -it mongo-router1 bash -c "echo 'sh.status()' | mongo"
$> docker exec -it mongo-router1 bash -c "echo 'db.people.stats()' | mongo"
```
