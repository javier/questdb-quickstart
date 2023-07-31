# questdb-quickstart

QuestDB is an Apache 2.0 licensed time-series database for high throughput ingestion and fast SQL queries with operational simplicity. To learn more about QuestDB visit https://questdb.io

 This is a quickstart covering:

* Getting started with QuestDB on docker
* Loading data using a CSV
* Using the web console for interactive queries
* Ingesting real-time data using the official clients in Go, Java, or Python
* Real-time dashboards with Grafana and QuestDB using the PostgreSQL connector

# Deploying the demo

You have three ways of running this demo

* Fully managed cloud-based installation (requires creating a free account on gitpod) using the Gitpod link in the next section. Recommended for quick low-friction demo
* Local installation using docker-compose. Recommended for quick low-friction demo, as long as you have docker/docker-compose installed locally and are comfortable using them
* Local installation using docker but doing step-by-step. Recommended to learn more about the details and how everything fits together

## Fully managed deployment using Gitpod

If you already have a gitpod account, you will need to log in when launching the deployment. If you don't have a gitpod
account, you can create one for free when launching the deployment.

When you click the button below, gitpod will provision an environment with questdb, a python script generating demo data,
and a grafana dashboard for visualisation. On finishing (typically about one minute), gitpod will try to open two new
tabs, one with the grafana dashboard, one with the QuestDB web interface. If your browser is blocking pop-ups, you will
need to click on the alert icon on the navigation bar to open the two links. When opening the grafana dashboard,
user is "demo" and password is "quest".

Click the button below to start a new development environment:

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/javier/questdb-quickstart)

## Docker-compose local deployment

Docker compose will provision an environment with questdb, a python script generating demo data,
and a grafana dashboard for visualisation. The whole process will take about 2-3 minutes, depending on yout internet speed
downloading the container images, and how quick your machine can build a python-based docker image.

```
git clone https://github.com/javier/questdb-quickstart.git
cd questdb-quickstart
docker-compose up
```

The grafana web interface will be available at http://localhost:13000/d/qdb-ilp-demo/device-data-questdb-demo?orgId=1&refresh=5s.
User is "demo" and password is "quest".

The QuestDB console is available at http://localhost:19000

Stop the demo via:

```
docker-compose down
```

## Local docker based deployment

There are [many ways to install QuestDB](https://questdb.io/docs/get-started/docker/), but I am choosing docker for portability. Note I won't be using a docker volume and the data will be ephemeral. Check the QuestDB docks to see how to start with a persistent directory.

```docker run --add-host=host.docker.internal:host-gateway -p 9000:9000 -p 9009:9009 -p 8812:8812 -p 9003:9003 questdb/questdb:latest```

Port 9000 serves the web console, port 9009 is for streaming ingestion, port 8812 is for PostgreSQL-protocol reads or writes, port 9003 is a monitoring/metrics endpoint

## Loading data using a CSV

There are different ways of loading CSV data into QuestDB. I am showing here the simplest one, recommended only for small/medium files with rows sorted by timestamp.

Go to the url of the web console, which runs on port 9000. If you are running this on your machine, it should be running at http://localhost:9000

You will see some icons at the left bar. Choose the one with an arrow pointing up. When you hover it will read "import". Click to browse or drag the provided demo file `trips.csv`. After a few seconds, your file should be loaded.

Go back to the web console main screen by clicking the `</>` icon on the left menu bar.


## Using the web console for interactive queries

If the name `trips.csv` is not showing at the `tables` section, click the reload icon (a circle formed by two arrows) at the top left.

You can now click on the table name to see the auto-discovered schema.

Run your first query by writting `select * from 'trips.csv'` at the editor and click run.

The data we loaded represents real taxi rides in the city of New York in January 2018. It is a very small dataset with only 999 rows for demo purposes.

Since the name of the table is not great, let's rename it by running this SQL statement

`rename table 'trips.csv' to  trips_2018`

And now we can run queries like

`select count() from trips_2018`

You can find the complete SQL reference for QuestDB (including time-series extensions) at [the docs](https://questdb.io/docs/concept/sql-execution-order/)

If you want to run some interesting queries on top of larger demo datasets, you can head to [QuestDB live demo](https://demo.questdb.io/) and just click on the top where it says 'Example Queries'. The `trips` dataset at that live demo has over 1.6 billion rows. All the datasets at the demo site are static, except for the `trades` table, which pulls crypto prices from Coinbase's API every second or so.

I have compiled some of the queries you can run on the demo dataset in [this markdown file](./demo_queries.md)

## Loading CSV data using the API

We can also load CSV files using the API. In this case, we can add schema details (for every column or just specific ones),
and table details, such as the name, or the partitioning strategy.

In this repository, I am providing a dataset with energy consumption and forecast data in 15-minutes intervals for a few
European countries. This file is a subset of [the original](https://data.open-power-system-data.org/time_series/2020-10-06)
and contains data only for 2018 (205,189 rows).

Import using curl:

```
curl -F schema='[{"name":"timestamp", "type": "TIMESTAMP", "pattern": "yyyy-MM-dd HH:mm:ss"}]' -F data=@energy_2018.csv 'http://localhost:9000/imp?overwrite=false&name=energy_2018&timestamp=timestamp&partitionBy=MONTH'
```

Navigate to the [QuestDB Web Console](http://localhost:9000) and explore the table we just created.

## Ingesting real-time data using the official clients in Go, Java, or Python

Follow the instructions at
* https://github.com/javier/questdb-quickstart/tree/main/ingestion/go
* https://github.com/javier/questdb-quickstart/tree/main/ingestion/java
* https://github.com/javier/questdb-quickstart/tree/main/ingestion/python

## Real-time dashboards with Grafana and QuestDB using the PostgreSQL connector

Follow the instructions at https://github.com/javier/questdb-quickstart/tree/main/dashboard/grafana

