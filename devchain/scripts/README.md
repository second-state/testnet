# How to use

It's assumed that you have already [setup docker](https://docs.docker.com/install/) on your computer.

Travis docker image is stored on [Docker Hub](https://hub.docker.com/r/secondstate/devchain/tags/).

```sh
docker pull secondstate/devchain
```

You can also pull a image with tag(mapping to different git branch), then tag it to `latest`, to make it default in your local:

```sh
docker pull secondstate/devchain:develop
docker tag secondstate/devchain:develop secondstate/devchain:latest
```

Note: Configuration and data will be stored at `/devchain` in the container. This directory will also be exposed as a volume. The ports `8545`, `26656` and `26657` will be exposed to connect.

## Create and start a single node

```sh
# prepare volume folder
mkdir -p ~/volumes/local

# initialize
docker run --rm -v ~/volumes/local:/devchain secondstate/devchain node init --home=/devchain

# start
docker run --rm -v ~/volumes/local:/devchain -p 26657:26657 -p 8545:8545 secondstate/devchain node start --home=/devchain
```

The chain's status is at the /status end-point:

```sh
curl http://localhost:26657/status
```

## Setup a local cluster

### Initialize

Run `./cluster.sh [chain_id] [count_of_all_nodes] [count_of_validators]` to initialize a cluster:

```sh
mkdir -p ~/volumes
git clone https://github.com/secondstate/testnet.git ~/volumes/testnet
cd ~/volumes/testnet/devchain/scripts
git checkout master

# this will initialize a cluster for the next section.
# Notes: please add `sudo` if you got error like "can't read XXX file or directory ..."
./cluster.sh test 6 4
```

### Start up

```sh
# start with 4 nodes which are all validators
docker-compose up -d validators
# then start 2 normal nodes and join the chain
docker-compose up -d normal

# or start all 6 nodes together
docker-compose up -d all
```

### Status and logs

```sh
# check status of these containers
docker-compose ps

# check logs
docker-compose logs -f

# specify a node to check
docker-compose logs -f -t node-1

# chain's status is at the `/status` end-point
curl http://localhost:26657/status
```

### Stops containers and cleanup

```sh
docker-compose down
```
