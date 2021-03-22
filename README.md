# elastic-stack
Elastic stack docker-compose

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