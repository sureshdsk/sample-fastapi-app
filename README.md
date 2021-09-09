# FastAPI with SigNoz observability integration
### Prerequisite
SigNoz should be installed in your local machine or any server. To install SigNoz follow the instructions at https://signoz.io/docs/deployment/docker/


### Run instructions for sending data to SigNoz

#### Run with docker
Build docker image
```
docker build -t sample-fastapi-app .
```

Run fast api app
```
# If you have your SigNoz IP Address, replace <IP of SigNoz> with your IP Address. 

docker run -d --name fastapi-container \
-e OTEL_METRICS_EXPORTER='none' \
-e OTEL_RESOURCE_ATTRIBUTES='service.name=fastapiApp' \
-e OTEL_EXPORTER_OTLP_ENDPOINT='http://<IP of SigNoz>:4317' \
-p 5000:5000 sample-fastapi-app


# If you are running signoz through official docker-compose setup, run `docker network ls` and find clickhouse network id. It will be something like this clickhouse-setup_default 
# and pass network id by using --net <network ID>

docker run -d --name fastapi-container \ 
--net clickhouse-setup_default  \ 
--link clickhouse-setup_otel-collector_1 \
-e OTEL_METRICS_EXPORTER='none' \
-e OTEL_RESOURCE_ATTRIBUTES='service.name=fastapiApp' \
-e OTEL_EXPORTER_OTLP_ENDPOINT='http://clickhouse-setup_otel-collector_1:4317' \
-p 5000:5000 sample-fastapi-app

```


### Send Traffic 
```
pip install locust
```

```
locust -f locust.py --headless --users 10 --spawn-rate 1 -H http://localhost:5000
```


### Trobleshooting
If you face any problem in instrumenting with OpenTelemetry, refer to docs at 
https://signoz.io/docs/instrumentation/python
