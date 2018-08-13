# Sidecar Backup Volumes

Example deploy on  ```deploy_sidecar_example/docker-compose.yml```

For kubernetes job see `kubernetes/jobs.yml`

Copy `env.sample` as `.env`

Run ONE TIME ONLY. Restores tar.gz given in .env on existent folder replacing contents

ENVIROMENT VARIABLE   | DESCRIPTION | Values
----------   | ---------- | --------------  
FOLDERS | folders comma separated | example `/var/www,/var/etc`
SCHEDULE | see below | 

## Minio/S3 config

ENVIROMENT VARIABLE   | DESCRIPTION | Values
----------   | ---------- | --------------  
TAR_TO_RESTORE | File path on bucket | string
S3_BUCKET | Bucket name | string
S3_HOST | host:port | `host:port`
S3_PROTOCOL | protocol type | `http` or `https`
S3_KEY | key | string
S3_SECRET | secret | string

# Usage

Create `.env` file:

```bash
TAR_TO_RESTORE="dumpdata/XXXXX.tar.gz"
### S3 or minio host
S3_HOST=minio:9000
### Protocol
S3_PROTOCOL=http
### Your bucket name
S3_BUCKET=cicciopollo
### minio or s3 credentials
S3_KEY=85A8U57ZITLSLFBYKNCG
S3_SECRET=14MAuAetrv7y3E6zAuUOimXy5KYRqrZKw3cWuEe/

```

Create `docker-compose.yml` file:

```yml
version: '2'
services:
  sidecar-restore-volumes:
      image: nutellinoit/sidecar-restore-volumes:latest
      restart: "no"
      environment:
        - TAR_TO_RESTORE=${TAR_TO_RESTORE}
        - SCHEDULE=${SCHEDULE}
        - S3_BUCKET=${S3_BUCKET}
        - S3_KEY=${S3_KEY}
        - S3_SECRET=${S3_SECRET}
        - S3_HOST=${S3_HOST}
        - S3_PROTOCOL=${S3_PROTOCOL}
      volumes_from:
        - wordpress:rw  # example mounted volumes for backups
###################
##### example wordpress install
  wordpress:
      image: wordpress
      restart: always
      ports:
        - "80:80"
      volumes:
        - ./wp-app:/var/www/html

```

Launch with

```bash
docker-compose up -d
```