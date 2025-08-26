# Dsynct

## Quick Start

```
# install and start temporal
brew install temporal
temporal server start-dev

docker run -p 8080:8080 markadiom/dsynct worker --flow-name myflow /dev/random /dev/null run temporal --host-port host.docker.internal:7233
```