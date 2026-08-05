# Data Transfer Format

## Prometheus exposition format

Services that want to expose Prometheus metrics simply need to expose an HTTP endpoint providing metrics in Prometheus's text-based exposition format. The output of such an endpoint is human-readable and can look like this in its simplest form:

```
http_requests_total{status="200"} 8556
http_requests_total{status="404"} 20
http_requests_total{status="500"} 68
```

Each line in this format represents a single sample (with metric name and labels) and its value, making it easy to expose metrics from systems and services. We will learn more about this format later in the course.

### What it's actually called

The canonical name is the **Prometheus exposition format** (currently version 0.0.4, stable since 2014). Its grammar per line is:

```
metric_name [ "{" label_name "=" quoted_value { "," ... } "}" ] SP value [ SP timestamp ] LF
```

Structurally it's a _sparse coordinate list_ — COO format. Each line is a `(coordinates, value)` tuple where the label set is the coordinate in a high-dimensional space and the sample is the value at that point. a metric is a point in an n-dimensional space whose axes are label names, and the exposition format is just the on-the-wire serialization of nonzero points in that space.
