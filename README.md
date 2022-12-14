# Magento2 Docker

When getting started I noticed there was not much in the way of easily using docker to install Magento.

This repository aims to address that, so there is a quick and simple way to get a local dev environment configured and running on docker.

## Requirements

Due to the Magento2 composer package being protected by API keys, you will have to create these for yourself and create [per the docs](https://developer.adobe.com/commerce/marketplace/guides/eqp/v1/access-keys/).

Then include these as env vars, either in a 

```bash
.env
```

file in the root of this document, or pass them directly in to docker-compose [per the docs](https://docs.docker.com/compose/environment-variables/)

```bash
docker-compose build magento_installer -e MAGENTO_PUBLIC_KEY=abc MAGENTO_PRIVATE_KEY=abc
```

## Magento2 Installer

There are two parts to this.  First of is to download the installer.  As mentioned above, this requires the API keys.

The Dockerfile will install the Magento2 package to the ```/magento``` folder inside the docker container.

## Magento2 Installation

Now the installer is ready, time to perform the installation.

You have to start the magento_db service and magento_elastic_search service, then you can run the installer.

We must run in compatibility mode to avoid memory limits.

## Maria DB

First make sure the correct env vars are set.

Run it like this:

```bash
docker-compose up magento_db
```

## Elastic Search

Spin up the container first:

```bash
docker-compose up magento_elastic_search
```

### If you want auth

NOTE - could not get auth working.

Then get the container name using ```docker ps```.

Then jump into that container with an sh terminal:

```bash
docker exec -ti [CONTAINER_ID] sh
```

And run this command:

```
./bin/elasticsearch-reset-password -u elastic
```

And copy that password into your env file.

## Installation

Once all the services are set up and configured, you can run the installer like so:

```bash
docker-compose up magento_db magento_elastic_search magento_adminer
docker-compose --compatibility run magento
```

## Extract Magento

Now you can extract the files.  Edit the docker-compose file to expose the mounts:

```yaml
  command: tar cvf /tmp/magento/magento.tar /magento
  volumes:
    - ./magento:/tmp/magento
```

And boom, you have a fresh magento install as a tarball in this directory.

## Mount your source code into a php container

Magento setup process is done, so we can now edit the source code, and mount this into the deployment container to see it working.

To do this, extract the tarball into the ./src folder:

```bash
tar xvf ./magento/magento.tar -C ./src
```

```yaml
  volumes:
    - ./src/magento:/var/www/html
```

Don't need a command now, apache entrypoint will do.

You can also start the entire folder with this command:

```bash
docker-compose up -d
```

Must use daemon mode or apache will complain
