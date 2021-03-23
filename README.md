# Elastic stack
Elastic stack project setup with docker-compose to fiddle around.

Compose file including:
- Elasticsearch
- Kibana
- APM server

## Running
Run with:

`docker-compose up -d`

To make it start next time you restart your machine run: 

`export RESTART_SERVICES=always && docker-compose up -d`

Then go to Kibana on: http://localhost:25601

## Setup Filebeat

```
docker run --net=host --rm \
  --name=filebeat-setup \
  --user=root \
  --volume="$(pwd)/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="$(pwd)/filebeat/apache.yml:/usr/share/filebeat/modules.d/apache.yml:ro" \
  --volume="$(pwd)/filebeat/tomcat.yml:/usr/share/filebeat/modules.d/tomcat.yml:ro" \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  --volume="$(pwd)/filebeat/:/var/log/:ro" \
  docker.elastic.co/beats/filebeat:7.11.2 \
  setup -strict.perms=false --modules=apache,tomcat \
  -E setup.kibana.host=localhost:25601 \
  -E output.elasticsearch.hosts=["localhost:29200"]
```

## Run Filebeat

enabled modules: apache, tomcat
log input for pipeline apache-responsetimes

`mkdir -p filebeat/{tomcat,apache,apache-responsetimes}`

```
docker run --net=host --rm \
  --name=filebeat \
  --user=root \
  --volume="$(pwd)/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="$(pwd)/filebeat/apache.yml:/usr/share/filebeat/modules.d/apache.yml:ro" \
  --volume="$(pwd)/filebeat/tomcat.yml:/usr/share/filebeat/modules.d/tomcat.yml:ro" \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  --volume="$(pwd)/filebeat/:/var/log/:ro" \
  docker.elastic.co/beats/filebeat:7.11.2 filebeat -e -strict.perms=false \
  -E output.elasticsearch.hosts=["localhost:29200"]
```

copy the logs in filebeat/tomcat or filebeat/apache or filebeat/apache-responsetimes