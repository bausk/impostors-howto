## Linux General

#### Emulate 5x requests load to an endpoint

```
seq 1 5 | xargs -n1 -P5  curl "http://localhost:8888/api/v1/reconciliation_tasks/slow/"
```

## Postgres operations / DevOps

#### Download and restore DB backup

Download

```
pg_dump --format=c --no-owner --no-privileges -h <SERVER> -U read_user -d <DATABASE> -n <SCHEMA> > "$_file"
```

Restore
```
psql -d new_example -U pguser -p 31869 -h localhost < $(ls -Art <FILENAME>_* | tail -n 1)
```