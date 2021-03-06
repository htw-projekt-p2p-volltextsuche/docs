# Get started

```bash
$ git clone https://github.com/htw-projekt-p2p-volltextsuche/fulltext-search
$ cd fulltext-search

$ sbt run
```

### Configure the App

The application configuration can be specified in *src/main/resources/application.conf*
in [HOCON](https://github.com/lightbend/config/blob/master/HOCON.md#hocon-human-optimized-config-object-notation)
notation. It's also possible to override the *application.conf* by java system properties or environment variables.

Environment variables need to be prefixed by `CONFIG_FORCE_` except if there is an *env-alias* specified for the property.

**They will be evaluated in following order** (starting from the highest priority):

1. Environment variable:               `export CONFIG_FORCE_SERVER_HOST="0.0.0.0"`
    - **This actually doesn't work!** - use env-alias if defined
2. Java system property as argument:   `sbt '; set javaOptions += "-Dserver.host="0.0.0.0""; run'`
3. *application.conf*:                 `server { host = 0.0.0.0 }`

#### Configuration properties

| identifier | description | env-alias | default |
|------------|------------:|----------:|--------:|
|server.port|HTTP port of the service|HTTP_PORT|8421|
|server.host|Host of the service|SERVER_HOST|0.0.0.0|
|server.log-body|Enables server logging of response bodies|SERVER_LOG_BODY|true|
|index.storage|Storage policy for the inverted index|INDEX_STORAGE_POLICY|local|
|index.stop-words-location|File name of the stopwords resource|-|stopwords_de.txt|
|index.sample-speeches-location|File name of the sample speeches resource|-|sample_speeches.json|
|index.insert-sample-speeches|Inserts sample speeches on startup when set|-|false|
|index.distribution-interval|Interval for scanning and distributing the cached index in ms|INDEX_DISTRIBUTION_INTERVAL|120000|
|index.distribution-chunk-size|Size of each concurrently processed chunk of the cached index|INDEX_DISTRIBUTION_CHUNK_SIZE|100|
|index.insertion-ttl|Amount of retries for the insertion of single index entry|INDEX_INSERTION_TTL|5|
|search.cache-size|Size of cache for search results|SEARCH_CACHE_SIZE|5|
|peers.uri|Entrypoint to the P2P network|-|http://localhost:8090/|
|peers.log-body|Enables client logging of response bodies|PEERS_LOG_BODY|true|
|peers.max-wait-queue-limit|Max wait queue limit for requests to peers|PEERS_MAX_WAIT_QUEUE_LIMIT|1024|

### Index Storage Policies

The application can be started with three different storage policies. They work as follows:

* `local`
    * Indexing and retrieval are both done locally in memory on the host machine.
* `distributed`
    * Indexing and retrieval are handled by calling the P2P network directly.
    * ?????? Using this option, the index request is blocked until all entries are distributed to the P2P network. This can
      possibly take a long while. Therefore, an appropriate timeout should be set on the calling machine.
* `lazy-distributed`
    * Indexing is done by first storing the index in a local cache and then distributing it on a background thread.
    * The interval for scanning the cached index can be configured with the option `index.distribution-interval`

### Run tests

```bash
$ sbt test
```