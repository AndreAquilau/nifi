### Start NIFI

```bash
docker compose up -d
```

### For use HTTP edit file nifi.properties

```
before - nifi.remote.input.secure=true
after - nifi.remote.input.secure=false
```

**step 1**

```bash
docker compose exec --user="root" nifi bash

apt update

apt install vim

exit
```

**step 2**

```bash
docker compose exec nifi bash

# before - nifi.remote.input.secure=true
# after - nifi.remote.input.secure=false

vim /opt/nifi/nifi-current/conf/nifi.properties
```

### Application Web

nifi: **http://localhost:8080**

### Docker Compose

**docker-compose.yml**

```
services:
  nifi:
    hostname: nifi
    container_name: nifi
    image: apache/nifi:1.19.0
    ports:
      - '9443:9443'
      # HTTP
      - '8080:8080'
      # HTTPS
      - '8443:8443'
      # Remote Input Socket
      - '10000:10000'
      # JVM Debugger
      - '8000:8000'
      # Cluster Node Protocol
      #- '11443:11443'
    volumes:
      # mkdir /var/lib/nifi && chown -R 1000:1000 /var/lib/nifi
      - ./var/lib/nifi/flowfile_repository:/opt/nifi/nifi-current/flowfile_repository
      - ./var/lib/nifi/database_repository:/opt/nifi/nifi-current/database_repository
      - ./var/lib/nifi/content_repository:/opt/nifi/nifi-current/content_repository
      - ./var/lib/nifi/provenance_repository:/opt/nifi/nifi-current/provenance_repository
      - ./var/lib/nifi/state:/opt/nifi/nifi-current/state
      - ./var/lib/nifi/logs:/opt/nifi/nifi-current/logs

      # A custom nifi.properties file can be mounted as follows.
      # - ./var/lib/nifi/conf/nifi.properties:/opt/nifi/nifi-current/conf/nifi.properties:ro
    environment:
      ########## Web ##########
      # nifi.web.http.host
      NIFI_WEB_HTTP_HOST: '0.0.0.0'

      # nifi.web.http.port
      #   HTTP Port
      NIFI_WEB_HTTP_PORT: '8080'

      # nifi.web.https.host
      #NIFI_WEB_HTTPS_HOST: '0.0.0.0'

      # nifi.web.https.port
      #   HTTPS Port
      #NIFI_WEB_HTTP_PORT: '8443'

      SINGLE_USER_CREDENTIALS_USERNAME: admin
      SINGLE_USER_CREDENTIALS_PASSWORD: 1234567891011

      # Environment NIFI CORE
      NIFI_HOME: /opt/nifi/nifi-current
      NIFI_LOG_DIR: /opt/nifi/nifi-current/logs
      NIFI_TOOLKIT_HOME: /opt/nifi/nifi-toolkit-current
      NIFI_PID_DIR: /opt/nifi/nifi-current/run
      NIFI_BASE_DIR: /opt/nifi
```
