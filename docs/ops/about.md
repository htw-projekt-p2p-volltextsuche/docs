# Description short
An repository containing all the configuration files and scripts used to deploy peers for the htw-projekt-p2p-volltextsuche.

# Description long
The peer to peer network consists of nodes with equal functionality. The nodes all have an user-interface delivered via http, a service for searching for tearms and p2p-distribution system for managing the inverted-index of the search-service. From here on this node is called "peer".

There exists on special node, which not only deploys the services mentioned above, but also hosts a central db holding all the speaches in json format as well as a crawler filling it with speaches. From here on this node is called "data".