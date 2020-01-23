# Gentics Mesh Docker Compose Stack 

This repository contains an example docker compose stack for the [Headless CMS Gentics Mesh](https://getmesh.io)

The stack contains the following components:

* Gentics Mesh

## Usage

```bash
git clone git@github.com:gentics/mesh-compose.git
cd mesh-compose
docker-compose up -d

# http://localhost:8080
# Login: admin/admin

```

## Language Handling

This example shows how an additional language (Klingon [**tlh**]) can be added to Gentics Mesh.

The language first needs to be added to the `lang.json` file. This file will be read by Mesh to create the extra language/s.

Please note that Mesh already contains all the default ISO 639-1 languages. (Two letter codes only).

https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes

The `mesh-ui2-config.js` file additionally lists the `contentLanguages` that are available in the UI. The added language codes have to be added here as well.