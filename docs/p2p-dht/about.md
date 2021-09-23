# p2p-dht

This peer-to-peer component is responsible for the distribution of the
fulltext-searches posting lists among the peers inside the network.
The component is implemented as a DHT based on the [libp2p Framework](https://libp2p.io)
and implemented in NodeJS.

In the layer modell it sits between the fulltext-search which is exclusively
served by it and above the filesystem which is used for storage.

![p2p layers](https://github.com/htw-projekt-p2p-volltextsuche/vortrag/raw/master/images/Schichten-p2p.png)

The services provided are:

- Insertion and retrieval of objects (in our case posting lists)
- Distributed storage of those objects on multiple nodes in the network
