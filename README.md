# OpenTelemetry-Elasticsearch-APM-for-.NET
Setup of OpenTelemetry-Elasticsearch-APM on docker for .NET8 service logging, traces and metrics

The Elasticsearch, Kibana and APM version used in the docker-compose file is version 8.18.0
The OpenTelemetry Collector version used in the docker-compose file is 0.140.1


Indices created are now created under Data Streams. The indices would be hidden because of a fullstop in front of the index.
If the name of an index is opentel-logs-* (based on the OpenTelemetry Collector config file):

The Daily Data Stream name would look like this: opentel-logs-2026.07.02

Daily index created would look like this: .ds-opentel-logs-2026.07.02-2026.07.02-000001

Deleting an index using ILM also deletes the Data Stream that the index is created in.

Newer Elasticsearch versions already have default Index Templates that automatically filters out Data Streams and Indices that start with "logs", "otel" and manages them, we won't create our indices starting with these words. Applying our own policy on the Data Streams and Indices won't work because they are managed by another policy.
Hence we used the word "opentel-logs" so that the Dat Streams and indices created won't be automatically managed by any default Index Templates.

#### Create a policy (where min_age is the highest age for a log/index):
```
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
```

#### Assign new policy to existing opentel-logs-* indices:
```
PUT opentel-logs-*/_settings
{
  "index.lifecycle.name": "deleteOldIndices"
}
```

#### Create template for opentel-logs-* indices:
```
PUT /_template/opentel-logs-template?pretty
{
"index_patterns": ["opentel-logs-*"], "settings": { "index.lifecycle.name": "deleteOldIndices" }
}
```


##### NOTE: OpenTelemetry Collector's Elasticsearch exporter has issues persisting the data it collects on disk. It currently does not work. There is an open [issue](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/45747#) on this failure.
