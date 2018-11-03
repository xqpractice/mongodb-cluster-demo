# MongoDB Cluster Demo
MongoDB Cluster Demo

## Init config server

```sh
$> docker exec -it <config-container> bash -c "echo 'rs.initiate({_id: \"mongoconf\",configsvr: true, members: [{ _id : 0, host : \"mongo-config\" }]})' | mongo"

$> docker exec -it <config-container> bash -c "echo 'rs.status()' | mongo"
```

## Init shard servers

```sh
$> docker exec -it <shard-container> bash -c "echo 'rs.initiate({_id : \"shard1\", members: [{ _id : 0, host : \"mongo-shard1-node1\" },{ _id : 1, host : \"mongo-shard1-node2\" },{ _id : 2, host : \"mongo-shard1-node3\" }]})' | mongo"

$> docker exec -it <shard-container> bash -c "echo 'rs.status()' | mongo"
```

## Init router server

```sh
$> docker exec -ti <router-container> bash -c "echo 'sh.addShard(\"shard1/mongo-shard1-node1\")' | mongo"

$> docker exec -it <router-container> bash -c "echo 'sh.status()' | mongo "
```
