# OpenTelemetry-Elasticsearch-APM-for-.NET
Setup of OpenTelemetry-Elasticsearch-APM on docker for .NET8 service logging, traces and metrics

The Elasticsearch, Kibana and APM version used in the docker-compose file is version 8.18.0
The OpenTelemetry Collector version used in the docker-compose file is 0.140.1


Indices created are now created under Data Streams.

Deleting an index using ILM also deletes the Data Stream that the index is created in.


PUT _index_template/opentel-logs-template
{
  "index_patterns": ["opentel-logs-*"],
  "data_stream": {},
  "priority": 500,
  "template": {
    "settings": {
      "index.lifecycle.name": "deleteOldIndices"
    }
  }
}


PUT /_template/opentel-logs-template_classic?pretty
{
"index_patterns": ["opentel-logs-*"], "settings": { "index.lifecycle.name": "deleteOldIndices" }
}
