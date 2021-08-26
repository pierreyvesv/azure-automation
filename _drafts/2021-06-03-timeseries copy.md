
# time series storage

## scaling

Thanos use prometheus Side car and aditionnal Querier can be installed to handle more load for reading data




## Object storage

- Victoria Metrics don't support object storage, few documentation mention an installation with object storage. If not supported you need to manage servers and disk may a Fuse storage.

## Recording rules

Thanos allow to use prometheus recording rules to aggregate some metrics.

Why storing all cpus and vcpus metrics from a server when only the average cpu is needed ?


## Metrics downsampling

Downsampling is used to reduce the number of point stored by period of time.

This is usefull in two cases to reduce the amount of data stored:

- the input when some service are too verbose
- reduce the amount of data stored for aged data by having fewer point per period of time. 1 point every 5min instead of every minutes
- or event store 1 point per hour instead of 12 points for each 5minutes time bucket

Thanos support downsampling where cortex do not.
-  [thanos-compaction]

Cortex is has not yet downsampling implemented [cortex-downsampling]

Victoria don't have downsampling, data have to be aggregated at first, from Victorias blog it consume less resources [victoria-downsampling]

## deduplication

Victoria metrics support when received in the bucket

## retention

Victoria: after the retention period the data is deleted , no compaction option or downsampling on aged data

## cardinality 

A cardinality explosion is the sudden rapid creation of new series due to one or more labels on one or metrics being populated with high-cardinality data

Victoria have cardinality limiter to drop data which comes at a too high rate

https://github.com/VictoriaMetrics/VictoriaMetrics#cardinality-limiter


Prometheus Bomb squad project allows to detect and respond to the high cardinality 
https://blog.freshtracks.io/bomb-squad-automatic-detection-and-suppression-of-prometheus-cardinality-explosions-62ca8e02fa32
https://github.com/open-fresh/bomb-squad

## Prometheus compliance

Victoria Metrics is not 100% compatible with Prometheus remote write

<https://prometheus.io/blog/2021/05/04/prometheus-conformance-remote-write-compliance/>

<https://valyala.medium.com/prometheus-vs-victoriametrics-benchmark-on-node-exporter-metrics-4ca29c75590fs>

## backfilling data to Thanos and Prometheus



[thanos-compaction]: https://thanos.io/v0.9/components/compact/

[cortex-downsampling]: https://cortexmetrics.io/docs/roadmap/#downsampling

[victoria-downsampling]: https://github.com/VictoriaMetrics/VictoriaMetrics#downsampling