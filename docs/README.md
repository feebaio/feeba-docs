Prod: [![PROD Deployment](https://github.com/codingoperations/feeba-service/actions/workflows/deployment_prod.yml/badge.svg)](https://github.com/codingoperations/feeba-service/actions/workflows/deployment_prod.yml), Dev: [![DEV deployment](https://github.com/codingoperations/feeba-service/actions/workflows/deployment_dev.yml/badge.svg?branch=main)](https://github.com/codingoperations/feeba-service/actions/workflows/deployment_dev.yml)
# Publisher

## Getting Started

To get started, git clone this project or pull Docker image from Docker Hub.


## Running with source code

You need to install [NVM](https://github.com/nvm-sh/nvm) and run the following commands.

Run the app by installing lerna and dependencies for front end.
```shell
./runner-cold.sh
```

To clean all node_modules
```shell
./runner-clean.sh
```

Just running the application assuming that all dependencies were already installed
```shell
./runner.sh
```

Building docker image
 ```shell
 ./img_build.sh
 ```

## Environment Variable Configuration

**Configuration Database**

We currently support [MongoDB](https://mongodb.com/) as persistent configuration store. You can configure connection using the following configs. On the first connection, we populate default configurations, which you can 

```
CONFIG_MONGO_HOST
CONFIG_MONGO_USER
CONFIG_MONGO_PASSWORD
CONFIG_MONGO_DB
```
