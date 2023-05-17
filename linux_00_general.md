## Linux General

#### Emulate 5x requests load to an endpoint

```
seq 1 5 | xargs -n1 -P5  curl "http://localhost:8888/api/v1/reconciliation_tasks/slow/"
```
