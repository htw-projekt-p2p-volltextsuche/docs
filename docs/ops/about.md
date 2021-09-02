# Description short
[The OPS repository](https://github.com/htw-projekt-p2p-volltextsuche/ops) contains all configuration files and scripts used to deploy peers for the htw-projekt-p2p-volltextsuche.

# Description long
The peer to peer network consists of nodes with equal functionality. The nodes all have an user-interface delivered via http, a service for searching for terms and p2p-distribution system for managing the inverted index of the search service. From here on such a node is called a "peer".

There is a unique node which additionally to the functionality mentioned above, also hosts a central db holding all the speeches in json format as well as a crawler filling it with speeches. From here on this node is called the "data".