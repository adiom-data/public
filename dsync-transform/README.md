# Dsync-Transform

## Quick Start

```
touch config.yml
docker run -v "./config.yml:/config.yml" -p 8085:8085 markadiom/dsync-transform
```

## Example Config File

```
mappings:
  - namespace: srcnamespace
    mapnamespace: dstnamespace
    cel:
      name: self + "!"
      newfield: '"abcd"'
    add: ["newfield"]
    delete: ["existingfield"]
```
