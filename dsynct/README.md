# Dsynct

## Quick Start

```
# install and start temporal
brew install temporal
temporal server start-dev

docker run -p 8080:8080 markadiom/dsynct worker /dev/random /dev/null run app --host-port 0.0.0.0:8080 temporal --host-port host.docker.internal:7233
```

Run with built in dsync-transform passing in `config.yml` as the transform config:
```
docker run -v "./config.yml:/config.yml" -p 8080:8080 markadiom/dsynct worker --transform /dev/random /dev/null dsync-transform://config.yml run app --host-port 0.0.0.0:8080 temporal --host-port host.docker.internal:7233
```