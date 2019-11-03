## Decomposition View

#### Section 1 Primary Presentation

![](https://i.loli.net/2019/11/03/FhGD3rlzqpYj6s1.png)

#### Section 2 Element Catalog

The C4 system is consisted of these important sub-module to decompose:  master node , normal server node , storage node , configuration node , load balance node. To resolve the normal phone request (contains the sync-request and async-request) , the normal server nodes are responsible to tackle the service from customers. Moreover , the storage nodes have to save the current session , cache the query on DB , and other cache to the locks . Load balance node has to detect the load in the whole system , and reschedule the load accordingly. 

#### Section 3 Context diagram

![](https://i.loli.net/2019/11/03/AiSygsqvFdRPCMQ.png)

#### Section 4 Variability Guide

The decomposition view represents the template module in the C4 system , and to exercise the variation points , you are recommended to run the server node. With the sync-request (like fetching user basic information , setting up a new session)  and async-request (conflict between the different sessions) , you could catch the key points in the master.

#### Section 5 Rationale

The PC agents communicate with C4 system across the WAN. 

Master node stands for the whole manager of C4 system . That is ,  the master node uses the loading table to temporarily record the load status of all subordinate slave server clusters, and can timely alarm and resource reallocation. The master node also contains a protocol subnode, which is mainly used to help the master node process user requests. After the agents send the request, they will first be parsed by the master node to inform the agents of the required service node location, and then the agents will send the request to the target server.

The keeper is responsible for asynchronous event processing and session saving between server nodes. Use Lock Manager to allocate locks and save contexts through context storage.

The server node is mainly responsible for periodically reporting heart beat status to the master node, and can perform hot backup between nodes. Please note that one server node here is also a cluster. In our design, one cluster is responsible for a certain range. The service (similar to the microservices architecture), and the leader is elected by the vote inside the cluster to perform resource scheduling and service allocation to the subordinate slave. In addition, the server node also supports the processing of the protocol and async service. Responsible for the interaction of the NOSS and DS systems. The cache storage is responsible for the caching of the external DB system. Since each business request will design a query of more than a dozen data tables, considering the time loss of the cascaded query, we use the cache mechanism to The query result is cached inside the server.