# 时序 4 种类型

Prometheus 时序数据分为 [Counter](https://prometheus.io/docs/concepts/metric_types/#counter), [Gauge](https://prometheus.io/docs/concepts/metric_types/#gauge), [Histogram](https://prometheus.io/docs/concepts/metric_types/#histogram), [Summary](https://prometheus.io/docs/concepts/metric_types/#summary) 四种类型。


## Counter

Counter 表示收集的数据是按照时间推移不断累加变化的，我们往往用它记录服务请求总量、错误总数等。

例如 Prometheus server 中 `http_requests_total`,  表示 Prometheus 处理的 http 请求总数，我们可以使用 `delta`, 很容易得到任意区间数据的增量，这个会在 PromQL 一节中细讲。

## Gauge

Gauge 表示搜集的数据是一个瞬时值，与时间没有关系，可以任意变高变低，往往可以用来记录内存使用率、磁盘使用率等。

例如 Prometheus server 中 `go_goroutines`,  表示 Prometheus 当前 goroutines 的数量。

## Histogram

Histogram 由 `<basename>_bucket{le="<upper inclusive bound>"}`，`<basename>_bucket{le="+Inf"}`, `<basename>_sum`，`<basename>_count` 组成，主要对采样的数据（例如请求耗时或响应大小）按照预设的 bucket（统计区间）进行累计，然后针对某个时间范围统计区间的变化情况，可以很方便得出指标的分位数，例如我们可以用它来计算最近5分钟请求耗时的95峰值。

例如 Prometheus server 中 `prometheus_local_storage_series_chunks_persisted`,  表示 Prometheus 中每个时序需要存储的 chunks 数量，我们可以用它计算待持久化的数据的分位数。

## Summary

Summary 和 Histogram 类似，由 `<basename>{quantile="<φ>"}`，`<basename>_sum`，`<basename>_count` 组成，主要用于表示一段时间内数据采样结果（通常是请求持续时间或响应大小），它直接存储了 quantile 数据，而不是根据统计区间计算出来的。

例如 Prometheus server 中 `prometheus_target_interval_length_seconds`。

## Histogram vs Summary

- 都包含 `<basename>_sum`，`<basename>_count`。
- Histogram 需要通过 `<basename>_bucket` 计算 quantile, 而 Summary 直接存储了 quantile 的值。
- 真实应用场景中，大家使用 Histogram 更多一些。
