# Configure

Configured via environmental variables in `variables.env`.
See: [`variables.env.example`](variables.env.example).

### `HTTP_PORT`

Port for the http server to run on.

Default: `8090`

### `HTTP_LIMIT`

The maximum request body size.

Default: `10mb`

See: [body-parser](https://www.npmjs.com/package/body-parser#limit-1)

### `PEER_PORT`

Port for the p2p node to run on.

Default: `8070`

### `PEER_IP`

IP for the p2p node to run on.

Default: `127.0.0.1` (localhost)

### `PEER_LIST`

List of peers, required for joining the network. Leave blank on first peer, that
is "creating" the network.

Default: `` (empty)

Format:
  - [multiaddr](https://multiformats.github.io/js-multiaddr/#what-is-multiaddr)
    incl. ipfs address <!--Todo: describe how to get the ipfs address-->
  - seperated by commas

Example:

```sh
PEER_LIST=/ip4/127.0.0.1/tcp/8071/ipfs/QmdW3RF4Yq4acYc4bgUmxeuJQLb2mQpQmMuDTGir5gQcYM, /ip4/127.0.0.1/tcp/8072/ipfs/QmPP5pdu6Dh93DL7LnQkKU2x8m4BoSrQswjQR5q26PMneg
```

### `PEER_STORAGE`

Storage location on disk for the data of the DHT.

Default: `/tmp/datastore`

### `PEER_REDUNDANCY`

Specifies on how many different remote nodes a value should be stored.

Default: 2

### `PEER_MPLEX_SIZE`

Specify the max message size of the p2p net's
[multiplexer](https://github.com/libp2p/js-libp2p-mplex).

Default: `10mb`

### `DEBUG`

Enable logging in the p2p network.

Set it to:

```
libp2p:dht:Q*,libp2p:dht:rpc:get-value:*
```