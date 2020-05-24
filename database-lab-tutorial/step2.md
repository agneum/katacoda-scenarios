Let's generate some synthetic database with data directory ("PGDATA") located at `/var/lib/dblab/data`. 

To do so we will use standard PostgreSQL tool called `pgbench`. With scale factor `-s 100`, the database size will be ~1.4 GiB.

To generate PGDATA with `pgbench`, we are going to launch a regular Docker container with Postgres temporarily. 
We are going to use `POSTGRES_HOST_AUTH_METHOD=trust` to allow connection without authentication. 

```bash
docker run \
  --name dblab_pg_initdb \
  --label dblab_sync \
  --env PGDATA=/var/lib/postgresql/pgdata \
  --env POSTGRES_HOST_AUTH_METHOD=trust \
  --volume /var/lib/dblab/data:/var/lib/postgresql/pgdata \
  --detach \
  postgres:12-alpine
```{{execute}}

Create the `test` database:
```bash
docker exec -it dblab_pg_initdb psql -U postgres -c 'create database test'
```{{execute}}

Generate data in the `test` database using `pgbench`:
```bash
docker exec -it dblab_pg_initdb pgbench -U postgres -i -s 100 test
```{{execute}}

PGDATA is ready and now it is time to stop and delete the temporary container with Postgres:
```bash
docker stop dblab_pg_initdb
docker rm dblab_pg_initdb
```{{execute}}