# Pre-Requisite

## cardano-db-sync

If you intend to run `cardano-submit-api` or `cardano-explorer-api`, you'll need to connect to either a running a `cardano-node` or a postgresql database filled with data from `cardano-db-sync`. 

Instructions on how to build `cardano-node` and `cardano-db-sync` can be found [here](https://github.com/input-output-hk/cardano-db-sync/blob/master/doc/building-running.md#building-and-running-the-cardano-db-sync-node).

Alternatively, docker images are also provided for these components and available on DockerHub:

```
$ docker pull inputoutput/cardano-node:latest
$ docker pull inputoutput/cardano-db-sync:latest
```

See also [docker-compose.yaml](https://github.com/input-output-hk/cardano-db-sync/blob/master/docker-compose.yml) for an example setup wiring these components together.

# Nix build (recommended)

Use the [Nix](https://nixos.org) build if:

1. You don't have Haskell development tools installed, but you do have Nix 
1. You would like to cross-compile a build for Windows, or run the tests under 
1. You would like to quickly grab a build of another branch from the Hydra cache, without needing to build it yourself.

> :bulb: Follow the instructions in
[iohk-nix/docs/nix.md](https://github.com/input-output-hk/iohk-nix/blob/master/docs/nix.md)
to install Nix and set up the IOHK binary cache.

To build the `cardano-explorer-api` for your current platform, use: 

```
nix-build -A scripts.<network>.explorer-api
```

To build the `cardano-submit-api` for your current platform, use:

```
nix-build -A scripts.<network>.submit-api
```

Where `<network>` can be `staging | testnet | mainnet`

If you have no local changes in your git repo, then this will download the build from the Hydra cache rather than building locally.

# Stack Build 

> :warning: Make sure you have the following system dependencies installed and available:
>
> - libsystemd-dev
> - libz-dev
> - libpq-dev
> - libssl-dev

Using [Haskell Stack](https://haskellstack.org/) to build the project: 

```
stack build
```

Alternatively, you can build only a subset of the available executables. In particular:

```
stack build cardano-explorer-api
stack build cardano-submit-api
```

for building the explorer API and the transaction submission API respectively. 

# Cabal Build

> :warning: Make sure you have the following system dependencies installed and available:
>
> - libsystemd-dev
> - libz-dev
> - libpq-dev
> - libssl-dev

Using [Cabal](https://www.haskell.org/cabal/) to build the project: 

```
cabal build
```