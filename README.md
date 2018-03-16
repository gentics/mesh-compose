# Gentics Mesh Docker Compose Stack 

This repository contains an example docker compose stack for the [Headless CMS Gentics Mesh](https://getmesh.io)

The stack contains the following components:

* Gentics Mesh
* Elasticsearch

## Usage

```bash
git clone git@github.com:gentics/mesh-compose.git
cd mesh-compose
docker-compose up -d
```

## Problems / Solutions

### Elasticsearch does not start.

On some system the Elasticsearch server does not start due to the `vm.max_map_count` kernel limit.

```
mesh-elasticsearch | [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

Solution see: https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode

## Suggestions

Placing the Elasticsearch data directory on NFS should be avoided since using NFS with Elasticsearch can lead to an unstable and slow search server.


