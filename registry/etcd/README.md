# Etcd as registry

This example shows dubbo-go's service discovery feature with Etcd as registry.

## How to run

### Start Etcd server
Follow this instruction to [install and start Etcd server](https://etcd.io/docs/v3.5/quickstart/).

Quick start with docker:
```shell
$ docker run -d --name etcd-server \
    --publish 2379:2379 \
    --publish 2380:2380 \
    --env ALLOW_NONE_AUTHENTICATION=yes \
    --env ETCD_ADVERTISE_CLIENT_URLS=http://0.0.0.0:2379 \
    quay.io/coreos/etcd:v3.5.0 \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name node1 \
    --initial-advertise-peer-urls http://0.0.0.0:2380 \
    --listen-peer-urls http://0.0.0.0:2380 \
    --advertise-client-urls http://0.0.0.0:2379 \
    --listen-client-urls http://0.0.0.0:2379 \
    --initial-cluster node1=http://0.0.0.0:2380
```

### Run server
```shell
$ go run ./go-server/cmd/server.go
```

test rpc server work as expected:
```shell
$ curl \
    --header "Content-Type: application/json" \
    --data '{"name": "Dubbo"}' \
    http://localhost:20000/greet.GreetService/Greet
```

You can check service registration in Etcd:
```shell
$ docker exec etcd-server /usr/local/bin/etcdctl get --prefix /dubbo
```

### Run client
```shell
$ go run ./go-client/cmd/client.go
hello world
```
