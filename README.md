
# HashiCorp Vault Local Development Setup

A simple, automated Docker Compose setup for running HashiCorp Vault locally with auto-initialization and auto-unseal features using Shamir's secret sharing. Perfect for learning and testing.

---

## Prerequisites

* Docker
* Docker Compose

---

## Getting Started

### 1. Run the Container
Clone or place your `docker-compose.yml` file in your project directory, then start the service in detached mode:

```bash
docker compose up -d

```

The container will automatically:

* Generate self-signed SSL certificates.
* Create the configuration file (`vault.hcl`).
* Initialize Vault (if not already initialized) and save the keys to `./data/vault-init.json`.
* Automatically unseal the Vault server.

### 2. Check Logs

To monitor the startup logs and verify the Vault status (`Sealed: false`), run:

```bash
docker compose logs -f vault

```

### 3. Stop and Clean Up

To stop the container and completely wipe all data (resetting Vault to a clean state), run:

```bash
docker compose down -v --remove-orphans
rm -rf data/*

```

---

## Basic Vault CLI Operations

To run Vault CLI commands, you can either enter the container shell or execute commands directly via `docker exec`.

### Setup Environment inside Container

First, access the running container shell:

```bash
docker exec -it vault-server sh

```

Export the required environment variables inside the container:

```bash
export VAULT_ADDR='[https://127.0.0.1:8200](https://127.0.0.1:8200)'
export VAULT_SKIP_VERIFY='true'

```

Login using your Root Token (you can find your root token inside `./data/vault-init.json` on your host machine):

```bash
vault login <your-root-token>

```
enable
```bash
vault secrets enable -path=secret kv-v2
```
---

### CRUD Operations (KV Secrets Engine)

By default, Vault has a KV secrets engine enabled at `secret/`.

#### 1. Create / Write a Secret

Create a new secret named `secret/my-app` with key-value pairs:

```bash
vault kv put secret/my-app username="admin" password="mysecurepassword"

```

#### 2. Get / Read a Secret

Retrieve the secret data:

```bash
vault kv get secret/my-app

```

*(To output in JSON format: `vault kv get -format=json secret/my-app`)*

#### 3. Update a Secret

Updating is done by writing to the same path (this creates a new version in KV v2):

```bash
vault kv put secret/my-app username="admin" password="newpassword123"

```

#### 4. Delete a Secret

To delete the latest version of the secret:

```bash
vault kv delete secret/my-app

```

```

```
