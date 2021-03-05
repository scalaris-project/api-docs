# XCloud API

The following set of calls are used to communicate and interact with XCloud services remotely over the Scalaris network via XRouter. XCloud consists of services that are powered by XRouter and are therefore interacted with using a subset of commands from the [XRouter API](#xrouter-api). 

See [XCloud Setup](#xcloud-setup) for instructions on setting up your environment for use with XCloud.


Call                                              | Description
--------------------------------------------------|---------------
[xrGetNetworkServices](#xrgetnetworkservices)     | Returns supported services
[xrConnect](#xrconnect)                           | Pre-connect to XRouter nodes
[xrConnectedNodes](#xrconnectednodes)             | Returns connected node services and fees
[xrService](#xrservice)*                          | Use to interact with XCloud services
[xrServiceConsensus](#xrServiceconsensus)*        | Use to interact with XCloud services with consensus
[xrGetReply](#xrgetreply)                         | Returns prior response associated with UUID
[xrShowConfigs](#xrshowconfigs)                   | Returns all node configs received as raw text
[xrUpdateConfigs](#xrupdateconfigs)               | Requests latest configs from nodes
[xrReloadConfigs](#xrreloadconfigs)               | Applies changes made to your configs
[xrStatus](#xrstatus)                             | Returns your XRouter configurations










## xrService

> Sample Data

```shell
{
  "service": "BTCgetbestblockhash"
}
```
This call is used to send a request to an XCloud service. XCloud is a decentralized oracle and microservice cloud network powered by XRouter that allows for interaction with microservices, blockchains, off-chain APIs, and cloud tech that's hosted by Service Nodes. If you'd like to operate your own Service Node, see the [Service Node Setup Guide](https://docs.scalaris.info/service-nodes/setup).


### Request Parameters

> Sample Request

```shell
scalaris-cli xrService SYSgetbestblockhash
```
<code class="api-call">xrService [service] [param1 param2 paramN]</code>

Parameter       | Type    | Description
----------------|---------|-------------
service         | string  | The service name without the namespace (*xcloud_service*, not *xrs::[xcloud_service]*).
paramN          | unknown | (Optional Parameter)<br>Refer to service documentation for parameter requirements. Information about an XCloud service can be viewed in the service configuration. Use [xrConnect](#xrconnect) to find a node with the service, then use [xrConnectedNodes](#xrconnectednodes) to review the service information.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```shell
{
  "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2",
  "error" : null,
  "id" : 1,
  "uuid" : "54b6ec00-8b06-4c2c-9e56-acdff4da69fe"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | unknown | The service's response data.
error           | object  | The native error response if an error occurred, otherwise a successful response will contain a `null` error.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).










## xrServiceConsensus

> Sample Data

```shell
{
  "service": "SYSgetbestblockhash"
}
```
This call is used to send a request to an XCloud service, along with a specified amount of nodes for consensus. XCloud is a decentralized oracle and microservice cloud network powered by XRouter that allows for interaction with microservices, blockchains, off-chain APIs, and cloud tech that's hosted by Service Nodes. If you'd like to operate your own Service Node, see the [Service Node Setup Guide](https://docs.scalaris.info/service-nodes/setup).


### Request Parameters

> Sample Request

```shell
scalaris-cli xrServiceConsensus 1 SYSgetbestblockhash
```
<code class="api-call">xrServiceConsensus [node_count] [service] [param1 param2 paramN]</code>

Parameter       | Type    | Description
----------------|---------|-------------
node_count      | int     | The number of nodes that will be used to route calls. The most common response (i.e. the response with the most consensus) will be returned as `reply`.
service         | string  | The service name without the namespace (*xcloud_service*, not *xrs::[xcloud_service]*).
paramN          | unknown | (Optional Parameter)<br>Refer to service documentation for parameter requirements. Information about an XCloud service can be viewed in the service configuration. Use [xrConnect](#xrconnect) to find a node with the service, then use [xrConnectedNodes](#xrconnectednodes) to review the service information.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```shell
{
  "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2",
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 250,
      "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2"
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 300,
      "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2"
    }
  ],
  "error" : null,
  "id" : 1,
  "uuid" : "54b6ec00-8b06-4c2c-9e56-acdff4da69fe"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | unknown | The service's response data. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `scalaris.infonf`. See node scoring for more details.
reply*          | unknown | The service's response data from the respective node.
error           | object  | The native error response if an error occurred, otherwise a successful response will contain a `null` error.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.









