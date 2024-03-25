# CCC (Containerized Cassandra Cluster)

Clean and simple containerized Apache Cassandra cluster for local testing. A modern alternative
to [ccm](https://github.com/digitalis-io/dcc).

This approach is based on [official image](https://hub.docker.com/_/cassandra/) (maintained
by [Docker Hub](https://docs.docker.com/docker-hub/official_images/) ), but still lets you control all the config files
without a need to build a custom image. Which is described in more details in
this [blog post](https://digitalis.io/blog/containerized-cassandra-cluster-for-local-testing/)

## Quick start

```
./setup-config.sh
docker-compose up -d

# WARNING:
# ./setup-config.sh doesn't work on MacOS atm, because Mac version of docker command line doesn't allow passing variables
# in order to fix it, replace all instances of ${CASSANDRA_VERSION} in the script with an explicit version number
# works fine on Linux

# Check the cluster status
docker exec cass1  nodetool status
```

- That will bring up a 3 node Cassandra cluster. You can configure the number of Cassandra containers
  in [docker-compose.yml](docker-compose.yml)
- Cassandra data is stored under `./data/` on the host and kept even if the cluster is destroyed
- Cassandra configuration files are under `./etc/`.
- You can access the cluster from your app on `localhost:9042`
- Or can add a container with your app to [docker-compose.yml](docker-compose.yml)

If you want to start fresh again (no data, vanilla config):

```
sudo rm -r ./data/* ./etc/*
```

## Destroying the cluster

```
docker-compose down
```

## nodetool

```
docker exec cass1  nodetool ...
```

## cqlsh

```
docker exec cass1 cqlsh -e "DESCRIBE keyspaces"

# Or in interactive shell mode
docker exec -it cass1 cqlsh
```

## Cassandra logs

```
docker logs -f cass1
```

## Basic configuration

Edit [docker-compose.yml](docker-compose.yml) for your needs, keeping in mind the following:

- An [official docker image](https://hub.docker.com/_/cassandra/) for cassandra must be used
- You should specify an explicit version tag
- Each cassandra container needs to have two volumes configured, one for data and one for config files
- Some basic Cassandra configuration can be done through `CASSANDRA_` environment variables
- Check the syntax with `docker-compose config` before running `./setup-config.sh`

## Cassandra configuration

If you need more advanced configuration, you can edit config files (eg. cassandra.yaml) for each node
under `etc/<node>/` and simply run

```
docker-compose restart
```

A good example can be found in
this [blog post](https://digitalis.io/blog/containerized-cassandra-cluster-for-local-testing/)
