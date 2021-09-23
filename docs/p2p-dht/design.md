# Design

## Framework Choice and Data Distribution Design

> How the inverted index should be distributed with the DHT

### Original Plan

**Indexing**

Initally the plan we developed was to have the crawler send the extracted and
structured documents to it's local full-text-search, which would break it up and
assemble groups of keywords. Those groups of keywords are to be split up accross
the p2p network, turned into an inverted index and persisted with the dht.

**Retrieval**

The posting lists of the inverted index are available to the full-text-search
component through an http server, which answers the UI's queries for searches.

### Choosing a p2p library

I had decided that [libp2p](https://libp2p.io) would be the p2p framework to be
used, as it fullfilled all the other demands of the project:

- net and routing (different configurable ways to run/setup the network)
- content routing (put/get using key-value pairs)
- static connection addresses
- well maintained and documented
- efficient transports
- well tested with checks and corrections throughout to maintain consistent
  state and functionality

It was preferable to:

1. using a smaller (and/or outdated) library that didn't cover the demands above
2. building the system from the ground up, for which the timeframe would be
   tight and it probably would not reach libp2p's level of consistency

#### libp2p's Constraints

One constraint that comes along with using libp2p is that the
[put](https://github.com/libp2p/js-libp2p-kad-dht/blob/2137e2a89f9d1a32f870b3ccce40d5cbb3d1af16/src/content-fetching/index.js#L103)
separates local and remote insertion. Looking at the implementation of put it
always inserts locally and then remotely into the network.
This is not to our specification as we did not want to make a distinction
between local and remote but just wanted to insert on the peer(s) closest to the
hashed key of the value to be inserted.

##### Possible changes to the design to address this

###### 1. local & remote

Keep current design and use the given implementation to insert once locally and
n-times remotely.

**Pro:**
- scalable: the processing of keywords -> posting lists happens distributed
  accross the network

**Contra:**
- suboptimal inverted index distribution: the algorithm used in the
  full-text-search component - to divvy up the keywords to different peers for
  computation - would be responsible for distribution the local part of the data
  accross the network
- no separation of concerns: again the algorithm just mentioned handles
  functionality that should be part of the p2p net

###### 2. remote only

Designate a peer to become the central crawler and inserter instance and only
insert remotely.

**Pro:**
- optimal inverted index distribution: by inserted in the closest peer, to the
  specification of kademlia this would be the most optimal distribution of the
  data accross the network
- simple implementation: least effort to implement (no balancing algorithm in
  full-text-search, trivial adjustment in the framework)

**Contra:**
- not scalable: to have all the computation of creating the index done on a
  single peer has a natural limit and is not an efficient use of the networks
  resources
- resource imbalance: increased data load on the rest of the peers, storage
  resources unused (hd and cpu (see previous point))
- single point of failure: not critial though, as the indexing only happens
  upfront and very occasionally throughout service

###### 3. local/remote

Fork the framework and adjust it to do local and remote put/get through the same
routines. This would be the optimal solution and satifies the original design.

**Pro:**
- optimal inverted index distribution: same as with 2.
- optimal resource usage: storage and cpu load are distributed equally
- scalable: same as with 1.

**Contra:**
- stability/introduction of inconsistencies: requires heavy modification of the
  framework, which will possibly circumvent checks and corrections within it and
  produce a less stable framework as a result
- a lot of work: most effort by far, I tried implementing this and hit one
  hurdle after another, the underlying system (kademlia and k-buckets below
  libp2p) definitely allow for this change, but you are just fighting
  against libp2p at every point

##### Our choice

We decided upon moving forward with "remote only" (2.) as the downsides are
very much tolerable along with having the best of both words on the pro side.

## API Specification

[As defined here](https://github.com/htw-projekt-p2p-volltextsuche/p2p-dht/blob/main/docs/openapi.yaml).

```yaml
openapi: 3.1.0
info:
  title: P2P framework
  version: 0.2
paths:
  /:key:
    get:
      summary: Get an entry from hash table
      description: data is an array
      parameters:
      - name: key
        in: path
      responses:
        "200":
          description: OK
          content:
            schema:
              format:
                error: "boolean"
                key: "string"
                value: "any[]"
              example:
                error: false
                key: "Grundgesetz"
                value: [ "postingList1", "postingList2" ]
        "400":
          description: User error
          content:
            schema:
              format:
                error: "boolean"
                errorMsg: "string"
              example:
                error: true
                errorMsg: "No key given"
  /append/:key:
    put:
      summary: Append to an entry in the hash table
      description: Entities are an array, to which you can add a single value through this route
      parameters:
      - name: key
        in: path
      - name: data
        in: body
        schema:
          format:
            data: "any"
          example:
            data: { id: 123 }
      responses:
        "200":
          description: OK
          content:
            schema:
              format:
                error: "boolean"
                key: "string"
              example:
                error: false
                key: "Grundgesetz"
        "400":
          description: User error
          content:
            schema:
              format:
                error: "boolean"
                errorMsg: "string"
              example:
                error: true
                errorMsg: "missing data"
  /merge/:key:
    put:
      summary: Merge with the array in the hash table
      description: Entities are an array, to which you can add a mutiple values through this route by wrapping them in an array
      parameters:
      - name: key
        in: path
      - name: data
        in: body
        schema:
          format:
            data: "any[]"
          example:
            data: [ { id: 123 }, { id: 124 } ]
      responses:
        "200":
          description: OK
          content:
            schema:
              format:
                error: "boolean"
                key: "string"
              example:
                error: false
                key: "Grundgesetz"
        "400":
          description: User error
          content:
            schema:
              format:
                error: "boolean"
                errorMsg: "string"
              example:
                error: true
                errorMsg: "missing data"
  /batch-get:
    post:
      summary: Get multiple entries of the hash table
      description:
        parameters:
          - name: keys
            in: body
            schema:
              format:
                data: "string[]"
        responses:
          "200":
            description: OK
            content:
              schema:
                format:
                  error: "boolean"
                  keys: "string[]"
                  values: "object"
                example:
                  error: false
                  keys: [ "Grundgesetz", "Merkel" ]
                  values: {
                    "Grungesetz": {
                      "error": false,
                      "value": [ "postingList1", "postingList2" ]
                    },
                    "Merkel": {
                      "error": true,
                      "errorMsg": "Not found"
                    }
          "400":
            description: User error
            content:
              schema:
                format:
                  error: "boolean"
                  errorMsg: "string"
                example:
                  error: true
                  errorMsg: "missing keys"
```

Also there is a key mangaged by the system named `_keyset_size`, which tracks
the number of keysets in the dht.

## API Examples

### Setup to run the examples

```sh
./spawnnode 1 &
#=> Server running on http://localhost:8091

./spawnnode 2 &
#=> Server running on http://localhost:8092

./spawnnode 3 &
#=> Server running on http://localhost:8093
```

### PUT `/append/:key`

```sh
curl -Ss -X PUT "http://localhost:8093/append/linux" -d '{"data":"arch"}' -H "Content-Type: application/json"
curl -Ss -X PUT "http://localhost:8093/append/linux" -d '{"data":"debian"}' -H "Content-Type: application/json"
```

```
{
  "error": false,
  "key": "linux"
}
```

### PUT `/merge/:key`

```sh
curl -Ss -X PUT "http://localhost:8093/merge/linux" -d '{"data":["ubuntu","manjaro"]}' -H "Content-Type: application/json"
```

```
{
  "error": false,
  "key": "linux"
}
```

### GET `/:key`

```sh
curl -Ss "http://localhost:8091/linux"
```

```
{
  "error": false,
  "key": "linux",
  "value": [
    "arch",
    "debian",
    "ubuntu",
    "manjaro"
  ]
}
```

### POST `/batch-get`

```sh
curl -Ss -X POST "http://localhost:8092/batch-get" -d '{"keys":["linux","windows"]}' -H "Content-Type: application/json"
```

```
{
  "error": false,
  "keys": [
    "linux",
    "windows"
  ],
  "values": {
    "linux": {
      "error": false,
      "value": [
        "arch",
        "debian",
        "ubuntu",
        "manjaro"
      ]
    },
    "windows": {
      "error": true,
      "errorMsg": "Not found"
    }
  }
}
```
