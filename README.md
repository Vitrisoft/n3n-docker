# n3n Docker

This repository provides Docker images and configurations to easily run [n3n](https://github.com/n42n/n3n) supernodes and edge nodes. n3n is a lightweight Peer-to-Peer VPN that creates virtual networks.

## What is n3n?

n3n is a VPN solution that allows edge nodes to discover each other via a supernode and establish direct peer-to-peer connections. If direct connections are not possible (e.g., due to NAT restrictions), the supernode can also relay traffic. Nodes within the same virtual network belong to a "community" and can use an encryption key for secure communication.

For more details, please refer to the [official n3n repository](https://github.com/n42n/n3n).

## Prerequisites

- Docker installed on your system.
- Docker Compose (for Docker Compose methods).

## Configuration

Both supernodes and edge nodes rely on configuration files (typically `.conf` files within the `/etc/n3n` directory in the container). The `./example/config` directory in this repository contains example configurations.

**Important:** The provided configurations (e.g., `edge.conf`, `supernode.conf` in `./example/config`) are examples. You should create your own configurations based on your specific needs. Always refer to the [official n3n documentation](https://github.com/n42n/n3n/tree/main/doc) for comprehensive details on all available configuration options and best practices, especially concerning security (e.g., community names, keys).

## Running a Supernode

A supernode allows edge nodes to announce and discover other nodes. It needs a publicly accessible port. Here are several ways to run an n3n supernode:

**Method 1: Using `docker-compose` (Standard)**

Uses the `example/supernode.dockercompose.yaml` file. This is often convenient for managing services.

```bash
docker compose -f ./example/supernode.dockercompose.yaml up
```
This command will start the supernode service defined in the compose file. Ensure your `supernode.conf` (or other relevant config) is in `./example/config` as the compose file likely mounts this directory.

**Method 2: Direct `docker run`**

This method uses the pre-built image `vitrisoft/n3n:latest` from Docker Hub.

```bash
docker run -v $(pwd)/example/config:/etc/n3n:ro -p 7777:7777 -p 7777:7777/udp vitrisoft/n3n:latest n3n-supernode start
```
- `-v $(pwd)/example/config:/etc/n3n:ro`: Mounts your local `./example/config` directory (read-only) to `/etc/n3n` in the container.
- `-p 7777:7777 -p 7777:7777/udp`: Maps TCP and UDP port 7777 on the host to the container.
- `vitrisoft/n3n:latest`: Specifies the Docker image to use.
- `n3n-supernode start`: The command to start the supernode. It will look for configuration files in `/etc/n3n`.

**Method 3: Using `docker-compose` (Local Development/Custom)**

Uses the `example/supernode.local.dockercompose.yaml` file. This might be pre-configured for local testing or specific development needs (e.g., using a locally built image).

```bash
docker compose -f ./example/supernode.local.dockercompose.yaml up
```
Review the `supernode.local.dockercompose.yaml` file to understand its specific configuration (e.g., image name, volume mounts).

**Method 4: Build and Run Custom Image**

If you want to use a custom image built from the `Dockerfile` in this repository:

```bash
# 1. Build the image
docker build -t my-custom-n3n-image .

# 2. Run the built image
docker run -v $(pwd)/example/config:/etc/n3n:ro -p 7777:7777 -p 7777:7777/udp my-custom-n3n-image n3n-supernode start
```
Replace `my-custom-n3n-image` with a name and tag of your choice.

## Running an Edge Node

Edge nodes are the clients that connect to the n3n virtual network. They require configuration for community, key, and supernode address(es).

**Method 1: Using `docker-compose` (Standard)**

Uses the `example/edge.dockercompose.yaml` file.

```bash
docker compose -f ./example/edge.dockercompose.yaml up
```
This will start the edge node service. Ensure your edge configuration file (e.g., `mynetwork.conf` or `edge.conf`) is in `./example/config`.

**Method 2: Direct `docker run`**

This method uses the pre-built image `vitrisoft/n3n:latest` from Docker Hub.

```bash
docker run --cap-add=NET_ADMIN --device=/dev/net/tun:/dev/net/tun -v $(pwd)/example/config:/etc/n3n:ro vitrisoft/n3n:latest n3n-edge start mynetwork
```
- `--cap-add=NET_ADMIN`: Grants necessary network administration capabilities.
- `--device=/dev/net/tun:/dev/net/tun`: Makes the TUN device available.
- `-v $(pwd)/example/config:/etc/n3n:ro`: Mounts your local `./example/config` directory (read-only) to `/etc/n3n` in the container.
- `vitrisoft/n3n:latest`: Specifies the Docker image to use.
- `n3n-edge start mynetwork`: Starts the edge node. `mynetwork` refers to the configuration file name (e.g., `mynetwork.conf`) located in `/etc/n3n`.

**Method 3: Using `docker-compose` (Local Development/Custom)**

Uses the `example/edge.local.dockercompose.yaml` file.

```bash
docker compose -f ./example/edge.local.dockercompose.yaml up
```
Review `edge.local.dockercompose.yaml` for its specific settings, including the image used and how configuration is handled (e.g. if it specifies a config file for n3n-edge).

**Method 4: Build and Run Custom Image**

If you want to use a custom image built from the `Dockerfile` in this repository:

```bash
# 1. Build the image
docker build -t my-custom-n3n-image .

# 2. Run the built image
docker run --cap-add=NET_ADMIN --device=/dev/net/tun:/dev/net/tun -v $(pwd)/example/config:/etc/n3n:ro my-custom-n3n-image n3n-edge start mynetwork
```
Replace `my-custom-n3n-image` with a name and tag of your choice.

## Source

The official n3n source code can be found at: [https://github.com/n42n/n3n](https://github.com/n42n/n3n)

## License

The code in this repository (for Docker setup) is licensed under the [MIT](./LICENSE) license.
The n3n software itself has its own licensing terms, as detailed in its repository. 

## Disclaimer
*This README.md was generated with the assistance of an AI. However, all Docker commands and setup instructions have been tested by a human to ensure accuracy and functionality.*