# RabbitMQ Cluster

Docker Compose setup for a 3-node RabbitMQ cluster.

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/jerbao/rabbitmq
cd rabbitmq
```

### 2. Create the `.env` file

```bash
cp .env.example .env
```

### 3. Generate the Erlang cookie

The `RABBITMQ_ERLANG_COOKIE` **must be identical across all 3 nodes** for the cluster to form. Generate it once:

```bash
openssl rand -base64 32
```

Paste the output into the `RABBITMQ_ERLANG_COOKIE` field of every `.env` on every VM.

### 4. Configure each VM

On each VM, edit `.env` and set:

| Variable              | VM 1   | VM 2   | VM 3   |
|-----------------------|--------|--------|--------|
| `RABBITMQ_HOSTNAME`   | `rmq1` | `rmq2` | `rmq3` |

The `IP_RMQ1`, `IP_RMQ2`, and `IP_RMQ3` values must be the **same** on all 3 VMs and point to the physical IPs of each machine.

### 5. Start the node

```bash
docker compose up -d
```

Repeat on each VM.

## Ports

| Port     | Purpose                                  |
|----------|------------------------------------------|
| `5672`   | AMQP — application traffic               |
| `15672`  | Management UI (http://<host>:15672)      |
| `4369`   | EPMD — Erlang discovery                  |
| `25672`  | Internal cluster communication (Erlang)  |

## Files

- `docker-compose.yml` — service definition
- `.env.example` — template for the `.env` file (commit this)
- `.gitignore` — ignores the real `.env`
- `rabbitmq.conf` — RabbitMQ broker config (identical on all VMs, no edits required)

## Security Notes

- Never commit the `.env` file. It contains credentials and the Erlang cookie.
- Change the default `admin` password before exposing the management UI to a network.
- The Erlang cookie grants full cluster access — treat it as a secret.

## Useful Commands

Check cluster status (run inside the container):

```bash
docker exec rabbitmq rabbitmqctl cluster_status
```

List nodes:

```bash
docker exec rabbitmq rabbitmqctl status
```

View logs:

```bash
docker compose logs -f rabbitmq
```
