Docker builds of [cardano-explorer-api](https://hub.docker.com/repository/docker/inputoutput/cardano-explorer-api) and [cardano-submit-api](https://hub.docker.com/repository/docker/inputoutput/cardano-submit-api) are available on Docker Hub.

Every release is tagged using semantic versioning and pushed to Docker Hub. For instance, images for the release 2.0.0 can be pulled from Docker Hub via:

``` 
$ docker pull inputoutput/cardano-explorer-api:2.0.0
$ docker pull inputoutput/cardano-submit-api:2.0.0
```

The [docker-compose.yaml](https://github.com/input-output-hk/cardano-rest/blob/master/docker-compose.yml) in this repository provides a working reference, and can be used as-is **with the addition of database configuration files** to manage the stack of Cardano services.

## How To Configure

An example configuration is given in the [config]() folder at the root of the repository:

```
config/
├── postgres_db.example
├── postgres_password.example
└── postgres_user.example
```

To get started, simply remove the `.example` suffix from each file. On a real setup, you'd provision these credentials yourself. 

:warning: As of today, it is absolutely **mandatory** for the `postgres_user` to be defined as `cexplorer`. :warning: 

## How To Build / Run

```
NETWORK=testnet docker-compose up
```

The `NETWORK` environment variable is required and can be set to either `mainnet` or `testnet`. Be careful when submitting transactions through `mainnet`!

## How To Use

By default,

- explorer-api will listen to http://localhost:8100 
- submit-api will listen to http://localhost:8101 

Have a look at the [API Documentations](https://github.com/input-output-hk/cardano-rest/wiki#user-guides) to get started. For example:

```
$ curl http://localhost:8100/api/txs/last 
```

Will give you the last few transactions known on the network. 

## Start the full stack in _detached_ mode, then view output from containers

```
docker-compose up -d --build && docker-compose logs -f
```
The initial sync will take some time, but you can stop and resume at any point if **retaining the data volumes**. 

## List containers
```
docker-compose ps -a
```
Show logs from a specific service
```
docker-compose logs -f cardano-explorer-api
```
## Display running processes
```
docker-compose top
```
## Stop the stack
```
docker-compose stop
```
## Rebuild database during version upgrade
When instructed to rebuild the DB, retaining the `cardano-node` data volume is important to minimize the sync time:
``` console
docker-compose stop
docker volume ls
# copy the volume ending in *_postgres-data to the clipboard
docker volume rm [PASTE]
docker-compose pull # this will fetch the update if the `latest` tag is present in your compose file
docker-compose start --force-recreate
```

## Stop and remove containers, networks, images, and volumes
:warning: Only do this if you need to wipe all state from the host.
```
docker-compose down -v
```

## Troubleshooting
If you have a port clash on the host, change the mapping in the [docker-compose](https://github.com/input-output-hk/cardano-rest/blob/master/docker-compose.yml)

### Building the Docker image locally

Ensure that you have [Nix](https://nixos.org/) installed and the IOHK binary cache enabled
([instructions](https://github.com/input-output-hk/iohk-nix/blob/master/docs/nix.md)).

Then run these commands from the `cardano-rest` git repository:

```
docker load -i $(nix-build -A dockerImages.submitApi --no-out-link)
docker load -i $(nix-build -A dockerImages.explorerApi --no-out-link)
```

If you have no local changes, the build should be downloaded from
the [IOHK binary cache](https://hydra.iohk.io/jobset/Cardano/cardano-rest)
then loaded into the local Docker registry.
