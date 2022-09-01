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

You have to start the magento_db service, then you can run the installer

```bash
docker-compose up -d magento_db
docker-compose run magento_installer
```
