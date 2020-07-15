# gvm11-docker
### forked from falkowich/gvm10-docker

- [gvm11-docker](#gvm11-docker)
  - [Tags and versions](#tags-and-versions)
  - [Some examples and info](#some-examples-and-info)
  - [Sqlite3 DB backend](#sqlite3-db-backend)
    - [Use with "docker run"](#use-with-%22docker-run%22)
      - [Start with non-persistant storage](#start-with-non-persistant-storage)
      - [Start with mounted volume](#start-with-mounted-volume)
  - [PostgrSQL DB backend](#postgrsql-db-backend)
      - [Start with mounted volume](#start-with-mounted-volume-1)
  - [Use with docker-compose](#use-with-docker-compose)
    - [Start in frontend](#start-in-frontend)
    - [Check logs](#check-logs)
    - [With docker](#with-docker)
  - [Disclamer](#disclamer)
  - [ToDo / Thoughts / Goals](#todo--thoughts--goals)
  - [Done [sorta]](#done-sorta)

Suggestions and bugreports are always welcome, just post an issue over at [falkowich/gvm10-docker](https://github.com/falkowich/gvm10-docker) 

## Tags and versions

This table shows what versions makes up what tags on dockerhub.  
_See https://github.com/falkowich/gvm10-docker/issues/17_

* stable » When the latest image has been stable for some time, it merges to this branch. This is the proposed prod branch  
* latest » Latest update to the upstream release 10.0 releasetree.
* dev » Upstream master, to test new functions and prepare for next major release.  

| image type | upstream gvm-libs | docker Tag | local branch | autobuild on dockerhub| description | 
|---|---|---|---|---|---|
| sqlite | 11.0 | sqlite | stable | no | sqlite and gsa |
| psql | 11.0 | psql | stable | no | psql and gsa |




## Sqlite3 DB backend

```docker pull sohnemann/gvm11:sqlite```  

And if you want to try out "bleeding edge", master branch

```docker pull sohnemann/gvm11:edge```  

### Use with "docker run"

#### Start with non-persistant storage

```docker run -p 443:443 sohnemann/gvm10:sqlite```

```docker pull sohnemann/gvm11:edge```  

#### Start with mounted volume

This will mount /usr/local/var/lib/gvm/ in /var/lib/docker/volumes/gvm/_data/ as docker volume gvm.  
**WARNING** - This volume will be lost if/when container is pruned

```bash
docker run \
       -p 443:443 \
       -v gvm:/usr/local/var/lib/gvm/ \
       --name gvm11 \
       sohnemann/gvm11:sqlite
```

To check out info about the volume

```bash
docker volume inspect gvm
[
    {
        "CreatedAt": "2019-04-13T19:22:15+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/gvm/_data",
        "Name": "gvm",
        "Options": null,
        "Scope": "local"
    }
]
```

```bash
docker run \
       -p 443:443 \
       -v gvm:/usr/local/var/lib/gvm/ \
       --name gvm11 \
       sohnemann/gvm11:edge
```


## PostgrSQL DB backend

```docker pull sohnemann/gvm11:psql```  

### Use with "docker run"

#### Start with non-persistant storage

```docker run -p 443:443 falkowich/gvm10:psql```

#### Start with mounted volume

**WARNING** - These volumes will be lost if/when container is pruned

```bash
docker run \
       -p 443:443 \
       -v gvm:/usr/local/var/lib/gvm \
       -v psql:/var/lib/postgresql/ \
       --name gvm11 \
       sohnemann/gvm11:psql
```

To check out info about the volume

```bash
docker volume inspect gvm
[
    {
        "CreatedAt": "2019-04-13T19:22:15+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/gvm/_data",
        "Name": "gvm",
        "Options": null,
        "Scope": "local"
    }
]
```

## Use with docker-compose

### Start in frontend

```docker-compose up```

### Start in backend

```docker-compose up -d```

### Check logs

```docker-compose logs -f```

## Maintanance

### With docker-compose

Sync SCAP data  
```docker-compose exec gvm11 /usr/local/sbin/greenbone-scapdata-sync```

Sync CERT data  
```docker-compose exec gvm11 /usr/local/sbin/greenbone-certdata-sync```

Sync NVT data  
```docker-compose exec gvm11 /usr/local/sbin/greenbone-nvt-sync```

DB maintanance (vacuum, analyze, cleanup-config-prefs, cleanup-port-names, cleanup-result-severities, cleanup-schedule-times, rebuild-report-cache or update-report-cache)  
```docker-compose exec gvm11 /usr/local/sbin/gvmd -v --optimize=vacuum```

Change admin password  
```docker-compose exec gvm11 /usr/local/sbin/gvmd -v --user=admin --new-password=super-secret-password```

Update to "latest" image
```bash
docker-compose stop
docker-compose pull
docker-compose up -d
```

### With docker

Sync SCAP data  
```docker exec -i gvm11 sh -c "/usr/local/sbin/greenbone-scapdata-sync"```

Sync CERT data  
```docker exec -i gvm11 sh -c "/usr/local/sbin/greenbone-certdata-sync"```

Sync NVT data  
```docker exec -i gvm11 sh -c "/usr/local/sbin/greenbone-nvt-sync"```

DB maintanance (vacuum, analyze, cleanup-config-prefs, cleanup-port-names, cleanup-result-severities, cleanup-schedule-times, rebuild-report-cache or update-report-cache)  
```docker exec -i gvm11 sh -c "/usr/local/sbin/gvmd -v --optimize=vacuum"```

Change admin password  
```docker exec -i gvm11 sh -c "/usr/local/sbin/gvmd -v --user=admin --new-password=super-secret-password"```

## GSA

user/pass - admin/admin

## Infos

This repo was forked from [falkowich/gvm10-docker](https://github.com/mikesplain/openvas-docker). Thanks for the great work!

## ToDo / Thoughts / Goals
- Make GVM11 work in Docker
