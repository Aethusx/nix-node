# About

All releases of nodejs built and accessible under a single flake.

It's like `nvm` but for nix! 

## Setup

### Setting up the registry

Via the CLI

```sh
nix registry add node 'github:andyrichardson/nix-node'
```

In `configuration.nix`

```nix
nix.registry."node".to = {
  type = "github";
  owner = "Aethusx";
  repo = "nix-node";
};
```

### Setting up the binary cache

Via the CLI

```sh
cachix use nix-node
```

In `configuration.nix`

```nix
nix.binaryCaches = [ "https://cache.nixos.org/" "https://nix-node.cachix.org/" ]
```

## Usage

Using latest major releases

```sh
nix shell node#16
```

Using explicit release versions

```sh
nix shell node#16.3.0
```

## Contributing

Open to contributions for supporting more versions, architectures, etc.

### Adding new versions

Get the checksum for the new version.

```sh
version="1.0.0"
nix-prefetch-url --type sha256 "https://nodejs.org/dist/v$version/node-v$version.tar.xz"
```

Update `flake.nix` with new version and checksum.

Update [CI build](https://github.com/andyrichardson/nix-node/blob/master/.github/workflows/build.yml#L10) to build new version.

## Garbage Collector cleans up builds

When using 
```sh
nix-collect-garbage -d
```
it also cleans all our node builds, to prevent we can do a 'root' syslink.

First find the node-js instance in /nix/store/, you can use this command:

```sh
find /nix/store -maxdepth 1 -name '*node*'
```

Then use this command to create root syslink

```sh
nix-store --add-root /opt/nodejs-root-16 --indirect --realise /nix/store/b2xyc0fwax961h6bja0j371zg7b0mvnr-nodejs-16.20.2
```

``/opt/nodejs-root-16`` can be anything, remember it must be unique for every node version ``/nix/store/b2xyc0fwax961h6bja0j371zg7b0mvnr-nodejs-16.20.2`` is our nodejs path, replace it with whatever nodejs instance you want to keep from getting cleaned by garbage collector

