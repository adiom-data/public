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

## General Usage

Dsync Transform runs mostly off configuration file where the mappings are specified. Duplicate mappings are allowed- they will fan out. Use the filter feature to avoid fanout if necessary. Note that mapping ids should also require mapping the id keys as well. ID mappings only have access to the original id so that they can be applied to deletes which do not have access to the full data.

The general way this works is each source document is converted into an internal format which is then subject to the mappings. Then it is converted back to the output document type. Transformations typically mean specifying a `cel` (Common Expression Language) expression. Since `cel` has access to limited types you may need to use `map` to coerce the type properly. For example, `cel` does not support 32 bit integers, so if we need a 32 bit integer we need to specify that explicitly via a `map`.

## Configuration

* `wild` - string (default: *) - When specifying a path, this matches anything.
* `delimiter` - string (default: .) - When specifying a path, this is the delimiter.
* `env` - map[string, any] - Variables available under the `env` variable in CEL expression mappings.
* `unwrapbson` - boolean (default: false) - If true, will automatically convert various bson types to a more native type like ObjectIds to strings.
* `mappings` - list[mapping]
  * `namespace` - string - Namespace this applies to
  * `mapnamespace` - string - New namespace name
  * `mapid` - CEL string - Expression to map the `id` for updates. Only has the original `id` field available and `env`.
  * `filter` - CEL string - Expression that returns a boolean where if true, this document will be skipped. Only has the original `id` field available and `env`.
  * `idkeys` - list[string] - Describes the original names of each part of the id.
  * `finalidkeys` - list[string] - Describes the names of each part of the id after the mapping.
  * `add` - list[string] - Describes the paths that will be added in the mapping if the parent exists.
  * `delete` - list[string] - Describes the paths that will be deleted if it exists.
  * `cel` - map[string, CEL string] - For each defined path specify a cel expression to perform a mapping. Has `env`, `id`, `doc`, `parent`, and `self` available as variables.
  * `map` - map[string, string] - For each defined path specify a special mapping function to apply. Applies after `cel`.
  * `self` - CEL string - An expression that serves as a mapping for the whole document.
* `defaultmapping` - string - Name (namespace) of the mapping from `mappings` to use as a fallback.
* `namespacemapper` - CEL string - Default expression to automatically map all namespaces. Has `env`, `self` (namespace).

## Available Mappings

For use with the `map` configuration or inside a `cel` configuration expression. Note that in certain cases, it is advisable to use it in the `map` to force a certain type that `cel` cannot.

* `int32` - Converts to an int32. Should use in `map` only.
* `float` - Converts to a float32. Should use in `map` only.
* `json_number` - Converts to a JSON Number.
* `json_decode` - Decodes a JSON string or bytes into an object.
* `json_encode` - Encodes an object as a JSON string.
* `bson_decimal128` - Converts a string to a BSON Decimal128.
* `bson_object_id` - Converts a string to a BSON ObjectID. Should use in `map` only.
* `bson_uuid` - Converts a UUID string to a BSON UUID.
* `bson_object_id_string` - Converts BSON ObjectID to a string.
* `bson_uuid_string` - Converts BSON UUID to a string.
