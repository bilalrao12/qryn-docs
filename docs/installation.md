# 📦 Installation

Let's install **qryn** in a snap. All you need is `NodeJS`, `Bun` or `Docker` to get started.

?> Using a ClickHouse LTS version > 23.3.8 is required. Use latest at your own risk. 

Configuration is performed using [ENV](env.md) parameters passed to the process or container.

<!-- tabs:start -->

#### ** Docker **
<a id=docker name=docker></a>

![image](https://user-images.githubusercontent.com/1423657/184507884-624b9598-62e1-413f-854e-8210ecac4e75.png ':size=300x100')

Get started in seconds using **qryn** with Docker - _batteries included!_

Each _qryn_ release is automatically pushed to [docker hub](https://hub.docker.com/r/qxip/qryn/tags) and [ghcr](ghcr.io/metrico/qryn:latest)

#### NodeJS runtime
```
qxip/qryn:latest
```
```
ghcr.io/metrico/qryn:latest
```

#### Bun runtime
```
qxip/qryn:bun
```
```
ghcr.io/metrico/qryn:bun
```


##### Docker Compose

Start qryn using a bundled ClickHouse instance: 

```
  qryn:
    image: qxip/qryn:latest
    ports:
      - "3100:3100"
    environment:
      - CLICKHOUSE_SERVER=clickhouse-server
      - CLICKHOUSE_AUTH=qryn:supersecretpassword
      - CLICKHOUSE_DB=qryn
  clickhouse-server:
    image: clickhouse/clickhouse-server:latest
    container_name: clickhouse-server
    environment:
      - CLICKHOUSE_USER=qryn
      - CLICKHOUSE_PASSWORD=supersecretpassword
    ports:
      - 8123:8123
    healthcheck:
      test: ['CMD', 'wget', '--spider', '-q', '127.0.0.1:8123/ping']
      interval: 1s
      timeout: 1s
      retries: 30
```

If you prefer using a _remote_ ClickHouse instance rather than a local one:

```
qryn:
    image: qxip/qryn:latest
    ports:
      - "3100:3100"
    environment:
      - CLICKHOUSE_SERVER="my.clickhouse.service"
      - CLICKHOUSE_PORT=9443
      - CLICKHOUSE_PROTO="https"
      - CLICKHOUSE_AUTH="admin:supersecretpassword"
      - CLICKHOUSE_DB=qryn
```

To use `qryn` with a [Clickhouse Cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment), include a `CLUSTER_NAME` ENV variable<br>
with the name of the [ClickHouse cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment) you want to use.

```
      - CLUSTER_NAME="mycluster"
```

<br>

Once all settings are ready, deploy your **qryn** instance:

```
docker compose up -d
```

<br>

!> Refer to the [configuration](env.md) for a list of supported parameters

#### Polyglot Demo

Want to see everything in action? 

Get an instant [qryn polyglot demo](https://github.com/metrico/qryn-oss-demo) w/ sample logs, traces and metrics - _batteries included!_

![image](https://user-images.githubusercontent.com/1423657/183257423-59ac2648-0627-4edc-99b0-eea42abc3ca1.png)


?> That's it - demo logs included! Just access your stack using [qryn-view or Grafana](getting-started)


#### ** K8s **
<a id=k8s name=k8s></a>

![image](https://github.com/user-attachments/assets/9d041e13-9b2a-4f03-89bb-f9e208525e7e ':size=400')

Get started in seconds using **qryn** with Docker - _batteries included!_

Each _qryn_ release is automatically pushed to [docker hub](https://hub.docker.com/r/qxip/qryn/tags) and [ghcr](ghcr.io/metrico/qryn:latest)

### Requirements
- Kubernetes 1.19+
- Helm 3.7+

### Install Chart Repository

```bash
helm repo add qryn-helm https://metrico.github.io/qryn-helm/
helm repo update
```

See [helm repository](https://helm.sh/docs/helm/helm_repo/) for command documentation.

### Install Chart
To deploy [qryn](https://github.com/metrico/qryn) using this Helm chart, use the following command:

```bash
helm repo add qryn-helm https://metrico.github.io/qryn-helm/
helm install [RELEASE_NAME] qryn-helm/qryn-helm --version 0.1.1
```

#### Documentation

See [helm install](https://helm.sh/docs/helm/helm_install/) for further documentation.

For customization, you can provide a `values.yaml` file or use `--set` flags to override specific configurations during installation.

Feel free to modify the configurations based on your requirements and environment.

### Global parameters

| Parameter                | Value         |
|--------------------------|---------------|
| kubernetesClusterDomain  | cluster.local |

## global parameters

| Parameter                              | Value         |
|----------------------------------------|---------------|
| nameOverride                           | ""            |
| fullnameOverride                       | ""            |
| imageCredentials                        | {}            |
| replicas                               | 1             |
| service.type                           | ClusterIP     |
| service.port                           | 3100          |
| podAnnotations                         | {}            |
| podLabels                              | qryn          |
| nodeSelector                           | {}            |
| tolerations                            | []            |
| affinity                               | {}            |
| resources.limits.cpu                   | 200m          |
| resources.limits.memory                | 256Mi         |
| resources.requests.cpu                 | 100m          |
| resources.requests.memory              | 128Mi         |
| autoscaling.enabled                    | true          |
| autoscaling.minReplicas                | 1             |
| autoscaling.maxReplicas                | 5             |
| autoscaling.targetCPUUtilizationPercentage | 80        |
| autoscaling.targetMemoryUtilizationPercentage | 80      |
| securityContext                        | {}            |
| ingress.enabled                        | false         |
| ingress.className                      | ""            |
| ingress.annotations                    | {}            |
| ingress.hosts                          | []            |
| ingress.tls                            | []            |

### qryn container parameters

| Environment Variable             | Default        | Usage                                     |
|---------------------------------|----------------|-------------------------------------------|
| CLICKHOUSE_SERVER               | localhost      | Clickhouse Server address                |
| CLICKHOUSE_PORT                 | 8123           | Clickhouse Server port                   |
| CLICKHOUSE_DB                   | qryn           | Clickhouse Database Name                 |
| CLICKHOUSE_AUTH                 | default:       | Clickhouse Authentication (user:password)|
| CLICKHOUSE_PROTO                | http           | Clickhouse Protocol (http, https)        |
| CLICKHOUSE_TIMEFIELD            | record_datetime| Clickhouse DateTime column for native queries|
| CLUSTER_NAME                    | undefined      | Clickhouse Cluster name                  |
| BULK_MAXAGE                     | 2000           | Max Age for Bulk Inserts                 |
| BULK_MAXSIZE                    | 5000           | Max Size for Bulk Inserts                |
| BULK_MAXCACHE                   | 50000          | Max Labels in Memory Cache               |
| LABELS_DAYS                     | 7              | Max Days before Label rotation           |
| SAMPLES_DAYS                    | 7              | Max Days before Timeseries rotation      |
| HOST                            | 0.0.0.0        | HTTP API IP                               |
| PORT                            | 3100           | HTTP API PORT                             |
| QRYN_LOGIN                      | undefined      | Basic HTTP Username                       |
| QRYN_PASSWORD                   | undefined      | Basic HTTP Password                       |
| READONLY                        | false          | Readonly Mode, no DB Init                 |
| OMIT_CREATE_TABLES              | false          | Omit database provisioning on startup. Dangerous.|
| FASTIFY_BODYLIMIT               | 5242880        | API Maximum payload size in bytes        |
| FASTIFY_REQUESTTIMEOUT          | 0              | API Maximum Request Timeout in ms        |
| FASTIFY_MAXREQUESTS             | 0              | API Maximum Requests per socket           |
| FASTIFY_METRICS                 | false          | API /metrics exporter                     |
| ADVANCED_PROMETHEUS_MAX_SAMPLES | 5000000        | Max samples per a promql request         |
| CORS_ALLOW_ORIGIN               | *              | CORS Allow Origin, default to any         |
| TEMPO_SPAN                      | 24             | Default span for Tempo queries in hours  |
| TEMPO_TAGTRACE                  | false          | Optional tagging of TraceID (expensive)  |
| DEBUG                           | false          | Debug Mode (for backwards compatibility) |
| LOG_LEVEL                       | info           | Log Level                                 |
| HASH                            | xxhash64       | Hash function using for fingerprints. Currently supported short-hash and xxhash64 (xxhash64 function)|
| ALERTMAN_URL                    | false          | Alertmanager API URL, i.e., http://my_alertmanager_url:1234|
| ADVANCED_SAMPLES_ORDERING       | timestamp_ns   | Specify the 'ORDER BY' your samples table should use (for multiple use comma-separated list fingerprint,timestamp_ns)|

## ENV Settings
For more information about qryn environment variables, visit [qryn Environments](https://qryn.metrico.in/#/env).


?> That's it - you're ready to run qryn in Kubernetes! Access your stack using qryn-view or Grafana



#### ** NPM **
<a id=npm name=npm></a>

![image](https://user-images.githubusercontent.com/1423657/184507827-9087fc2d-457b-42cd-ae2e-1c894160745b.png ':size=100')

Install `qryn` as global package on your system using `npm` and `nodejs` _(16.x-18.x)_
```bash
sudo npm install -g qryn
```

##### Single Instance

Start `qryn` using [ENV](env.md) variables for its settings:

```bash
CLICKHOUSE_SERVER="my.clickhouse.server" \
CLICKHOUSE_AUTH="default:password" \
CLICKHOUSE_DB="qryn" \
qryn
```

Connect securely to _any_ ClickHouse hosted service:
```
CLICKHOUSE_SERVER="my.cloud.service" \
CLICKHOUSE_PORT=9443 \
CLICKHOUSE_PROTO="https" \
CLICKHOUSE_AUTH="admin:supersecretpassword" \
CLICKHOUSE_DB="qryn" \
qryn
```

#### ClickHouse Cluster
To use `qryn` with a [Clickhouse Cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment), include a `CLUSTER_NAME` ENV variable with the name of the [ClickHouse cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment) you want to use.

```
CLICKHOUSE_SERVER="my.cloud.service" \
CLICKHOUSE_PORT=9443 \
CLICKHOUSE_PROTO="https" \
CLICKHOUSE_AUTH="admin:supersecretpassword" \
CLICKHOUSE_DB="qryn" \
CLUSTER_NAME="mycluster" \
qryn
```

If the variable is provided, the system automatically starts in the clustered clickhouse mode.
Make sure that `internal_replication=1` option is set in your ClickHouse Server config.


##### Cluster
You can cluster multiple instance of `qryn` using pm2 clustering or other techniques:

```
//ecosystem.config.js
module.exports = {
  apps: [{
    name: 'qryn',
    script: 'qryn',
    interpreter: '',
    instances: 4,
    exec_mode: "cluster",
    env: {
      CLICKHOUSE_SERVER: 'my.clickhouse.server',
      CLICKHOUSE_PORT: 8123,
      CLICKHOUSE_AUTH: 'default:password',
      CLICKHOUSE_DB: 'qryn',
      LABELS_DAYS: 7,
      SAMPLES_DAYS: 7,
      DEBUG: false
    }
  }]
}
```


?> That's it! You are ready to access the stack using qryn-view or Grafana

?> Intensive load? Run multiple process instances for ingestion and querying



#### ** PM2 **
<a id=pm2 name=pm2></a>

![image](https://user-images.githubusercontent.com/1423657/184507827-9087fc2d-457b-42cd-ae2e-1c894160745b.png ':size=100')

Install `qryn` and `pm2` as global packages on your system using `npm`

```bash
sudo npm install -g qryn pm2
```

Start `qryn` using [ENV](env.md) variables for its settings:
```bash
CLICKHOUSE_SERVER="my.clickhouse.server" \
CLICKHOUSE_AUTH="default:password" \
CLICKHOUSE_DB="qryn" \
pm2 start qryn
```

#### ClickHouse Cluster
To use `qryn` with a [Clickhouse Cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment), include a `CLUSTER_NAME` ENV variable with the name of the [ClickHouse cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment) you want to use.

```
CLICKHOUSE_SERVER="my.cloud.service" \
CLICKHOUSE_PORT=9443 \
CLICKHOUSE_PROTO="https" \
CLICKHOUSE_AUTH="admin:supersecretpassword" \
CLICKHOUSE_DB="qryn" \
CLUSTER_NAME="mycluster" \
pm2 start qryn
```


Save your settings and install as a system service
```
pm2 save
pm2 startup
```

?> That's it! You are ready to access the stack using qryn-view or Grafana

?> Intensive load? Run pm2 in `cluster` mode to leverage multiple cpu threads


#### ** Bun **
<a id=bun name=bun></a>

![image](https://user-images.githubusercontent.com/1423657/185789758-d7366d2f-7b59-4cda-8bf4-198214581dd6.png ':size=100')

Use **bun** to install and execute **qryn 3.x**

?> Install [bun](https://bun.sh/) before proceeding!

Install `qryn` from git and execute using `bun`
```bash
git clone https://github.com/metrico/qryn && cd qryn
bun install
```

Start `qryn` using [ENV](env.md) variables for its settings:

```bash
CLICKHOUSE_SERVER="my.clickhouse.server" \
CLICKHOUSE_AUTH="default:password" \
CLICKHOUSE_DB="qryn" \
bun qryn.mjs
```

### PM2

To start **qryn** with PM2 and Bun as the interpreter, run the following command:

```bash
CLICKHOUSE_SERVER="my.clickhouse.server" \
CLICKHOUSE_AUTH="default:password" \
CLICKHOUSE_DB="qryn" \
pm2 start --interpreter ~/.bun/bin/bun qryn
```

?> That's it! You are ready to use **qryn** with bun runtime



#### ** GIT **
<a id=git name=git></a>

![image](https://user-images.githubusercontent.com/1423657/184507866-9e43ae95-6974-4f0b-bdf3-7a8d05f43d8d.png ':size=100')

Clone the qryn repository, install with `npm`and run using `nodejs` 16.x *(or higher)*
```bash
git clone https://github.com/metrico/qryn && cd qryn
npm install
```

Start `qryn` using [ENV](env.md) variables for its settings:

```bash
CLICKHOUSE_SERVER="my.clickhouse.server" \
CLICKHOUSE_AUTH="default:password" \
CLICKHOUSE_DB="qryn" \
node qryn.js
```

#### ClickHouse Cluster
To use `qryn` with a [Clickhouse Cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment), include a `CLUSTER_NAME` ENV variable with the name of the [ClickHouse cluster](https://clickhouse.com/docs/en/architecture/cluster-deployment) you want to use.

```
CLICKHOUSE_SERVER="my.cloud.service" \
CLICKHOUSE_PORT=9443 \
CLICKHOUSE_PROTO="https" \
CLICKHOUSE_AUTH="admin:supersecretpassword" \
CLICKHOUSE_DB="qryn" \
CLUSTER_NAME="mycluster" \
qryn
```

?> That's it! You are ready to access the stack using qryn-view or Grafana

#### ** ECS **
<a id=aws name=aws></a>

![image](https://user-images.githubusercontent.com/1423657/196041303-7ec4b3f8-a948-46f2-90ec-1058ec50f91c.png ':size=200')

Use `AWS Fargate` to deploy and scale `qryn` using a remote ClickHouse instance

#### Create ECS Cluster
```
aws ecs create-cluster \
--cluster-name <YOUR_ECS_CLUSTER>
```

#### Create Log group
```
aws logs create-log-group \
--log-group-name /ecs/<YOUR_APP_NAME>/<YOUR_CONTAINER_NAME>
```

#### Create ECS Task Definiton
Every time a task is modified its version increases. For new tasks the version will be 1.
```
aws ecs register-task-definition \
--cli-input-json "file://./<YOUR_TASK_DEF_NAME>.json"
```

> This json file defines the container specification. You can define secrets such as NC_DB and environment variables here.

Here's the sample Task Definition

```json
{
  "family": "qryn-sample-task-def",
  "networkMode": "awsvpc",
  "containerDefinitions": [{
    "name": "<YOUR_CONTAINER_NAME>",
    "image": "qxip/qryn:latest",
    "essential": true,
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": {
        "awslogs-group": "/ecs/<YOUR_APP_NAME>/<YOUR_CONTAINER_NAME>",
        "awslogs-region": "<YOUR_AWS_REGION>",
        "awslogs-stream-prefix": "ecs"
      }
    },
    "secrets": [{
      "name": "<YOUR_SECRETS_NAME>",
      "valueFrom": "<YOUR_SECRET_ARN>"
    }],
    "environment": [{
      "name": "CLICKHOUSE_SERVER",
      "value": "<YOUR_CLICKHOUSE_SERVER_URL>"
    },{
      "name": "CLICKHOUSE_AUTH",
      "value": "<YOUR_CLICKHOUSE_SERVER_AUTH>"
    },{
      "name": "CLICKHOUSE_DB",
      "value": "qryn"
    }],
    "portMappings": [{
      "containerPort": 3100,
      "hostPort": 3100,
      "protocol": "tcp"
    }]
  }],
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "cpu": "256",
  "memory": "2048",
  "executionRoleArn": "<YOUR_ECS_EXECUTION_ROLE_ARN>",
  "taskRoleArn": "<YOUR_ECS_TASK_ROLE_ARN>"
}
```

#### Create ECS Service
```bash
aws ecs create-service \
--cluster <YOUR_ECS_CLUSTER> \
--service-name  <YOUR_SERVICE_NAME> \
--task-definition <YOUR_TASK_DEF>:<YOUR_TASK_DEF_VERSION> \
--desired-count <DESIRED_COUNT> \
--launch-type "FARGATE" \
--platform-version <VERSION> \
--health-check-grace-period-seconds <GRACE_PERIOD_IN_SECOND> \
--network-configuration "awsvpcConfiguration={subnets=["<YOUR_SUBSETS>"], securityGroups=["<YOUR_SECURITY_GROUPS>"], assignPublicIp=ENABLED}" \
--load-balancer targetGroupArn=<TARGET_GROUP_ARN>,containerName=<CONTAINER_NAME>,containerPort=<YOUR_CONTAINER_PORT>
```

> If your service fails to start, you may check the logs in ECS console or in Cloudwatch. Make sure the security groups have the correct inbound and outbound rules.

?> That's it! You are ready to use **qryn** on Fargate!


#### ** GCP **
<a id=gcp name=gcp></a>

![image](https://user-images.githubusercontent.com/1423657/196041367-1ea95555-4e6d-45bf-b98e-7a8e5d4ddfaa.png ':size=200')

Use `Google Compute Cloud` to deploy and scale `qryn` using a remote ClickHouse instance


#### Pull qryn Image on Cloud Shell
Since Cloud Run only supports images from Google Container Registry (GCR) or Artifact Registry, we need to pull qryn image, tag it and push it in GCP using Cloud Shell. Here are some sample commands which you can execute in Cloud Shell.
```
# pull latest qryn image
docker pull qxip/qryn:latest
```
```
# tag the image
docker tag qryn:latest gcr.io/<MY_PROJECT_ID>/qryn/qryn:latest
```
```
# push the image to GCR
docker push gcr.io/<MY_PROJECT_ID>/qryn/qryn:latest
Deploy qryn on Cloud Run
gcloud run deploy --image=gcr.io/<MY_PROJECT_ID>/qryn/qryn:latest \
                  --set-env-vars "CLICKHOUSE_SERVER=y<YOUR_CLICKHOUSE_SERVER_URL>" \
                  --set-env-vars "CLICKHOUSE_AUTH=<YOUR_CLICKHOUSE_SERVER_AUTH>" \
                  --region=us-central1 \
                  --allow-unauthenticated \
                  --platform=managed 
```

?> That's it! You are ready to use **qryn** on GCP

#### ** DigitalOcean **
<a id=digital name=digital></a>

![image](https://user-images.githubusercontent.com/1423657/196041474-98130d07-26fc-493c-bd4c-28b55c347aed.png ':size=200')


Use `Digital Ocean` to deploy and scale `qryn` using a remote ClickHouse instance

## Create App

On Home page, Click on `Create` and select `Apps (Deploy your code)`

![image](https://user-images.githubusercontent.com/1423657/196040670-6a9dfba8-c246-4836-9ba0-35f0fda1bb5d.png ':size=200')

![image](https://user-images.githubusercontent.com/1423657/196040591-13bae4b3-4bf8-406d-b48b-5bebf5617d9d.png ':size=200')

### Choose Source: Docker Hub

Configure Docker Hub as the container source

![image](https://user-images.githubusercontent.com/1423657/196040696-428cf633-61e3-4356-9dea-93a9fded1bf9.png ':size=200')

#### Choose Repository
- `qryn`

#### Choose Tag
- `latest` _(optional)_

#### Choose Port
Configure the HTTP port to `3100`

#### Enviroment Variables
Configure the ENV variables for connectivity, authentication, etc

- `CLICKHOUSE_SERVER` = `<YOUR_CLICKHOUSE_SERVER_URL>`
- `CLICKHOUSE_AUTH` = `<YOUR_CLICKHOUSE_SERVER_AUTH>`

### Review and Launch

Review all your App settings and launch in your preferred region

?> That's it! You are ready to use **qryn** on DigitalOcean


#### ** RPi4 **
<a id=bun name=raspberry></a>

<img src="https://j6z7x9q7.rocketcdn.me/wp-content/uploads/2019/09/Heatsink-case-goud-1.jpg" width=150 />

Install a full **qryn** stack on **aarch64** platforms

### Requirements
- RPI 4 (aarch64)
- ARMv8 64-Bit OS (DietPI or Raspbian)

#### Install ClickHouse (aarch64)
Install ClickHouse and define the default user `password`
```
curl -O 'https://builds.clickhouse.com/master/aarch64/clickhouse' && chmod a+x ./clickhouse
./clickhouse install
```

Start the ClickHouse service:
```
sudo clickhouse start
```

#### Install NodeJS
```
curl -sSL https://deb.nodesource.com/setup_16.x | sudo bash -
sudo apt install -y nodejs
```

#### Install qryn
Install **qryn** using the chosen ClickHouse `password`
```
npm install -g qryn @pastash/pastash @pastash/output_loki
```
```
CLICKHOUSE_AUTH="default:password" \ 
CLICKHOUSE_SERVER="localhost" \
CLICKHOUSE_DB="qryn" \
pm2 start qryn --name "qryn"
```

#### Install Grafana
```
wget https://dl.grafana.com/oss/release/grafana_9.1.1_arm64.deb
sudo dpkg -i grafana_9.1.1_arm64.deb
```

Add a datasource for qryn via UI or by using this [provisioning example]([https://github.com/metrico/qryn-oss-demo/blob/main/grafana/provisioning/datasources/datasource.yml](https://gist.githubusercontent.com/lmangani/a4be2275731783b37e0fd6f67439e5d5/raw/5a4d193976142c729197ae175e59bb927820a58f/datasource.yml))

![image](https://user-images.githubusercontent.com/1423657/186918652-d37c11de-a7a8-4d2c-a104-4a63c2da51e5.png)


```yaml
        apiVersion: 1
        datasources:
          - name: Loki
            type: loki
            access: proxy
            url: http://localhost:3100
```

**TLDR;** _The above procedure is available as a [shell script](https://gist.githubusercontent.com/lmangani/a4be2275731783b37e0fd6f67439e5d5/raw/5a4d193976142c729197ae175e59bb927820a58f/rpi4_cloki_install.sh)_


?> That's it! You are ready to use **qryn**

<!-- tabs:end -->
