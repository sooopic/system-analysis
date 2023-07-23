---
title: "Task 2. Integrations"
description: What does your user need to know to try your project?
weight: 2
---

## Task 2.1

{{% pageinfo %}}
**Reverse engineering**

Scenario:

You need to create accompanying documentation for an already implemented endpoint.
Imagine that developers don't have access to this endpoint and have to trust your
documentation blindly

Endpoint: https://www.okx.com/priapi/v5/market/mult-cup-tickers?t=1687526827042&ccys=ETH

{{% /pageinfo %}}

### Method description

* Protocol: HTTPS
* Method: GET
* Host: https://www.okx.com/
* URL Endpoint: /priapi/v5/market/mult-cup-tickers

Method recieves the currency name and time value and returns the exchange rate (for USD) for choosen timestamp.

### Input 

| Attribute | Belongs | Require | Type   | Mask              | Comments                                                         |
| :-------- | :-----: | :-----: | :----: | :---------------: | :--------------------------------------------------------------- |
| t         | Query   | ❌      | string | Unix Timestamp    | Timestamp. Default value - current timestamp.                    |
| ccys      | Query   | ✅      | string | enum[ETH,BTC,...] | Currency name. Can be several values per requests. Can't be null |

### Output 

| Attribute       | Belongs | Require | Type      | Mask              | Comments                                                               |
| :-------------- | :-----: | :-----: | :-------: | :---------------: | :--------------------------------------------------------------------- |
| code            | Body    | ✅      | string    | number            | Code answer. **0** - for correct answers and other for errors          |
| msg             | Body    | ✅      | string    | ---               | Message for error. Example: _Parameter ccys error_                     |
| **data**        | Body    | ✅      | **array** | ---               | Data object. Array. Every element of an array is data for one currency |
| data[n].ts      | Body    | ❌      | string    | Unix Timestamp    | Time                                                                   |
| data[n].ccy     | Body    | ❌      | string    | enum[ETH,BTC,...] | Currency name                                                          |
| data[n].last    | Body    | ❌      | string    | float             | Last exchange rate (USD)                                               |
| data[n].high24h | Body    | ❌      | string    | float             | Maximim exchange rate for 24h (USD)                                    |
| data[n].sodUtc0 | Body    | ❌      | string    | float             | Start of day (UTC0) exchange rate (USD)                                |
| data[n].open24h | Body    | ❌      | string    | float             | Open exchange rate (USD)                                               |
| data[n].low24h  | Body    | ❌      | string    | float             | Minimum exchange rate (USD)                                            |
| data[n].sodUtc8 | Body    | ❌      | string    | float             | Start of day (UTC8) exchange rate (USD)                                |

> Data will be empty if request contains wrong parameters!

#### Input example

Correct request:
```bash 
curl --location --request GET 'https://www.okx.com/priapi/v5/market/mult-cup-tickers?t=1687526827042&ccys=ETH,BTC'
```

Error request:
```bash 
curl --location --request GET 'https://www.okx.com/priapi/v5/market/mult-cup-tickers?ccys='
```

#### Output example

Correct answer:

```json
{
    "code":"0",
    "msg":"",
    "data":[
        {
            "last":"29903.5",
            "high24h":"29985.3",
            "sodUtc0":"29792.8",
            "ccy":"BTC",
            "open24h":"29906.2",
            "low24h":"29644.8",
            "sodUtc8":"29911.3",
            "ts":"1690109322702"
        },
        {
            "last":"1875.32",
            "high24h":"1891.25",
            "sodUtc0":"1866.56",
            "ccy":"ETH",
            "open24h":"1889",
            "low24h":"1853.83",
            "sodUtc8":"1889.62",
            "ts":"1690110128072"
        }
    ]
}
```

Error: 

```json
{
    "code":"51000",
    "msg":"Parameter ccys error",
    "data":[]
}
```

## Task 2.2

{{% pageinfo %}}
How is POST different from GET?
{{% /pageinfo %}}

**GET** method is often used for retrieving information from server. This method doesn't contains the Body-part in request.

Example of simple GET request:
```bash
# Sending only header with auth parameters
curl --location --request GET 'http://{host}/auth/realms/{realm}/protocol/openid-connect/userinfo' \
--header 'Authorization: Bearer .eyJleHAiOjE2NjUzMzczNTUsIml********'
```

**POST** contains the body part in the request and mostly using for sending information to the server (such as creating new resource). It also could be used for retrieving infromation, when there are not enough _Query Parameters_ in GET-method (but it is exceptions).

Example of simple POST request:
```bash
# Sending header (with auth parameters) and data with some information for creating new resource
curl --location --request POST 'https://{hostname}/{index}/_doc/{id}' \
--header 'Authorization: Basic ********' \
--header 'Content-Type: application/json' \
--data-raw '{
    "object": {
        "person": {
            "surname": "Panasyuk",
            "name": "Sergey",
            "middlename": "Alekseevich",
        }
    }
}'
```

## Task 2.3

{{% pageinfo %}}
There are two systems. Name all the ways to integrate these systems.
{{% /pageinfo %}}

The main types are:

* **Request-responce (or point-to-point)**
* **Async interaction**
* **External storage**
* **System level integration**

These are not the only ways to integrate two systems, but these ones are widespread.

### Request-responce (or point-to-point)

> System_1 send a request for System_2 and waits for a response.

```mermaid
sequenceDiagram
    System_1 ->>+ System_2: request via REST / SOAP / gRPC
    System_2 -->>- System_1: response
```

There could be different protocols (most popular): 

* SOAP (XML over HTTP)
* REST API (JSON over HTTP)
* GraphQL
* gRPC (one-directional)
* etc

### Async interaction

There are different ways to integrate two systems in async way:

* Using message brokers (Kafka, RabbitMQ)
    * Publisher/Subscriber
    * Command messages
* Webhooks
* Websockets
* gRPC (bidirectional)
* etc

#### Publisher/Subscriber

```mermaid
sequenceDiagram
    participant System_1
    participant MessageBroker
    participant System_2
    System_1 ->>+ MessageBroker: publish message
    MessageBroker -->>- System_2: read message
    System_2 ->>+ System_2: make actions
```

#### Command messages

```mermaid
sequenceDiagram
    participant System_1
    participant RequestTopic
    participant AnswersTopic
    participant System_2
    System_1 -->>+ RequestTopic: send message
    activate System_1
    RequestTopic -->>+ System_2: read message
    System_2 ->> System_2: make actions
    System_2 -->>- AnswersTopic: send message
    AnswersTopic -->> System_1: read message
    System_1 ->> System_1: make actions
    deactivate System_1
```

#### Webhooks

```mermaid
sequenceDiagram
    actor Client
    participant System_1
    participant System_2
    Client ->> System_1: send request
    System_1 -->> Client: return data
    System_1 ->> System_1: event and activates webhook
    System_1 -->>+ System_2: send information (HTTP POST)
    System_2 ->> System_2: make actions
    System_2 -->>- System_1: return answer
```

#### WebSockets 

This protocol is mostly used for integration of frontend and backend.

```mermaid
sequenceDiagram
    actor Client
    participant FE_System_1
    participant BE_System_1
    FE_System_1 ->> BE_System_1: Establish connection
    BE_System_1 ->> FE_System_1: send events
    BE_System_1 ->> FE_System_1: send events
    FE_System_1 -->> Client: show notification for client
```

#### gRPC (bidirectional) 

This protocol is mostly used for integration of frontend and backend.

```mermaid
sequenceDiagram
    participant System_1
    participant System_2
    System_1 ->> System_2: Establish connection
    System_1 ->> System_2: send data_1 part 1
    System_1 ->> System_2: send data_1 part 2
    System_2 -->> System_1: send data_2 part 1
    System_2 -->> System_1: send data_2 part 2
```

### External storage

We could use an external storage (like an database, S3-storage or FTP-storage) to share data between two systems.

```mermaid
sequenceDiagram
    participant System_1
    participant DataStorage
    participant System_2
    System_1 ->>+ DataStorage: write data
    DataStorage -->>- System_1: return ok
    System_2 ->>+ DataStorage: read data
    DataStorage -->>- System_2: return data
```

### System level integration

Sometimes system has no public APIs or we could have no access to it.

In that case there are a possu for integration, like:
* System 1 could connect System 2 via ssh (telnet, etc.) for performing scripts.
* System 1 could connect System 2 via system protocol (like SNMP) for gathering information (mostly for hardware or system information).
* etc.
 
Each of these methods are unique for current situation and mostly can't be used as an production implementation.

> Sometimes system has its own private API. So we could also use SDK for integration.
