---
title: "Task 2. Integrations"
description: What does your user need to know to try your project?
weight: 2
---

## Task 2.1

{{% pageinfo %}}
### Reverse engineering

Scenario:

You need to create accompanying documentation for an already implemented endpoint.
Imagine that developers don't have access to this endpoint and have to trust your
documentation blindly

Endpoint: https://www.okx.com/priapi/v5/market/mult-cup-tickers?t=1687526827042&ccys=ETH

{{% /pageinfo %}}

## Task 2.2

{{% pageinfo %}}
How is POST different from GET?
{{% /pageinfo %}}

**GET** method is often used for retrieving information from server. Mostly, this mehod doesn't contains the body part in request (some of web-servers will not except )

**POST** contains the body part in the request and mostly using for sending information to the server. It also could be used for retrieving infromation, when there are not enough _Query Parameters_ in GET-method

## Task 2.3

{{% pageinfo %}}
There are two systems. Name all the ways to integrate these systems.
{{% /pageinfo %}}

There are ways:
* Request-responce
* Queues
* RPC
* Async
* External storage

### Request-responce
