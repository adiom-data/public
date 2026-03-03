# Dsync-Transform

## Quick Start

```
touch config.yml
docker run -v "./config.yml:/config.yml" -e "DSYNCT_MODE=simple" -p 8085:8085 markadiom/dsynct --host-port=0.0.0.0:8085 transformer
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

## General Usage

Dsync Transform runs mostly off configuration file where the mappings are specified. Duplicate mappings are allowed- they will fan out. Use the filter feature to avoid fanout if necessary. Note that mapping ids should also require mapping the id keys as well. ID mappings only have access to the original id so that they can be applied to deletes which do not have access to the full data.

The general way this works is each source document is converted into an internal format which is then subject to the mappings. Then it is converted back to the output document type. Transformations typically mean specifying a `cel` (Common Expression Language) expression. Since `cel` has access to limited types you may need to use `map` to coerce the type properly. For example, `cel` does not support 32 bit integers, so if we need a 32 bit integer we need to specify that explicitly via a `map`.

## Configuration

* `wild` - string (default: *) - When specifying a path, this matches anything.
* `delimiter` - string (default: .) - When specifying a path, this is the delimiter.
* `env` - map[string, any] - Variables available under the `env` variable in CEL expression mappings.
* `unwrapbson` - boolean (default: false) - If true, will automatically convert various bson types to a more native type like ObjectIds to strings.
* `filtererrors` - boolean (default: false) - If true, will not fail on errors during conversion and instead skip it and log a warn message. Errors encountered when retrieving the original ID will still error.
* `mappings` - list[mapping]
  * `namespace` - string - Namespace this applies to
  * `mapnamespace` - string - New namespace name
  * `mapid` - CEL string - Expression to map the `id` for updates. Only has the original `id` field available and `env`.
  * `filter` - CEL string - Expression that returns a boolean where if true, this document will be retained. Only has the original `id` field available and `env`.
  * `idkeys` - list[string] - Describes the original names of each part of the id.
  * `finalidkeys` - list[string] - Describes the names of each part of the id after the mapping.
  * `add` - list[string] - Describes the paths that will be added in the mapping if the parent exists.
  * `delete` - list[string] - Describes the paths that will be deleted if it exists.
  * `cel` - map[string, CEL string] - For each defined path specify a cel expression to perform a mapping. Has `env`, `id`, `doc`, `parent`, and `self` available as variables.
  * `map` - map[string, string] - For each defined path specify a special mapping function to apply. Applies after `cel`.
  * `self` - CEL string - An expression that serves as a mapping for the whole document.
* `defaultmapping` - string - Name (namespace) of the mapping from `mappings` to use as a fallback.
* `namespacemapper` - CEL string - Default expression to automatically map all namespaces. Has `env`, `self` (namespace).
* `idlist` - boolean (default: false) - If true, the `id` variable will always be a list. When false, the `id` variable contains the first id value if it is the only id value.

## Available Mappings

For use with the `map` configuration or inside a `cel` configuration expression. Note that in certain cases, it is advisable to use it in the `map` to force a certain type that `cel` cannot.

* `int32` - Converts to an int32. Should use in `map` only.
* `float` - Converts to a float32. Should use in `map` only.
* `json_number` - Converts to a JSON Number.
* `json_decode` - Decodes a JSON string or bytes into an object.
* `json_encode` - Encodes an object as a JSON string.
* `bson_decimal128` - Converts a string to a BSON Decimal128.
* `bson_decimal128_string` - Converts a BSON Decimal128 to a string.
* `bson_object_id` - Converts a string to a BSON ObjectID. Should use in `map` only.
* `bson_uuid` - Converts a UUID string to a BSON UUID.
* `bson_object_id_string` - Converts BSON ObjectID to a string.
* `bson_uuid_string` - Converts BSON UUID to a string.
* `md5` - Applies the MD5 hash to a string or bytes returning bytes.
* `sha1` - Applies the SHA-1 hash to a string or bytes returning bytes.
* `sha256` - Applies the SHA-256 hash to a string or bytes returning bytes.

### Byte mappings

* `be_to_int32` - Converts bytes to an int assuming a big endian format. Use in a `map` to get an int32.
* `be_to_int64` - Converts bytes to an int64 assuming a big endian format.
* `to_be_int32` - Converts data into bytes representing an int32 in big endian format.
* `to_be_int64` - Converts data into bytes representing an int64 in big endian format.
* `reverse_bytes` - Reverses a byte array.

## Available Functions

All the available mappings are usable as unary functions, however here are additional non-unary ones:

* `now_millis()` - Current time in milliseconds
* `now_nanos()` - Current time in nanoseconds (resolution may be limited by your machine)
* `uuid_v4_bytes()` - Generate a random UUID as bytes
* `uuid_v4_string()` - Generate a random UUID as a string
* `uuid_v3_bytes(uuid, name)` - Generate a deterministic UUID based on a UUID namespace and name as bytes (MD5)
* `uuid_v3_string(uuid, name)` - Generate a deterministic UUID based on a UUID namespace and name as a string (MD5)
* `uuid_v5_bytes(uuid, name)` - Generate a deterministic UUID based on a UUID namespace and name as bytes (SHA-1)
* `uuid_v5_string(uuid, name)` - Generate a deterministic UUID based on a UUID namespace and name as a string (SHA-1)
