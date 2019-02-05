# Gentics Mesh Docker Compose Stack 

This repository contains an example docker compose stack for the [Headless CMS Gentics Mesh](https://getmesh.io)

## Clustering / HA Example

The cluster stack contains the following components:

* Gentics Mesh (Three instances)
* Elasticsearch
* Nginx (Loadbalancer over three Gentics Mesh instances)

Gentics Mesh forms a cluster using the three instances. Nginx will form a loadbalancer over the three instances.

The first instance will initialize the cluster and the other instances will join the cluster and replicate the database.

Once the setup is up and running you can log in the Admin UI using `admin/admin`
* http://localhost:8080/mesh-ui

After that you will be able to request on the status endpoints. Note that the node name will alter for each request since Nginx will select a different mesh server to redirect the request to.

* http://localhost:8080/api/v1/

You can also check the overall cluster status using the status endpoint.
* http://localhost:8080/api/v1/admin/cluster/status

## Usage

```bash
git clone git@github.com:gentics/mesh-compose.git
cd mesh-compose
git checkout clustering
docker-compose up -d
```

## Problems / Solutions

### Elasticsearch does not start.

On some system the Elasticsearch server does not start due to the `vm.max_map_count` kernel limit.

```
mesh-elasticsearch | [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

Solution see: https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode

TLDR: Run `sysctl -w vm.max_map_count=262144` on your docker Host system.

## Online Backup

Invoking the backup of Gentics Mesh will cause the mesh instance in the cluster to block any inbound requests. It is thus advised to launch a dedicated instance (`backup-node`) which will join the cluster and replicate the data. Once the instance has joined the backup process can be invoked. After that step the optional `backup-node` can be shut down.

1. Launch backup node

```docker-compose  -f backup-node-compose.yml  up```

2. Invoke backup

The [Gentics Mesh CLI](https://getmesh.io/docs/cli/) can be used to invoke the backup on the `backup-node`.


```bash
$ mesh configure
? Endpoint http://localhost:8088
? Generate a new API key? Yes
? Enter username admin
? Enter password [hidden]
New key saved

$ mesh admin backup
Invoked server side backup process.
```

3. Terminate backup node

The backup will be stored in the `backup` folder of this repository.

Note that the binary files need to be backuped as well. Read our [backup documentation](https://getmesh.io/docs/administration-guide/#_backup_recovery) for more information.

## Suggestions

Placing the Elasticsearch data directory on NFS should be avoided since using NFS with Elasticsearch can lead to an unstable and slow search server.


