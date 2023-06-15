---
date: 2017-03-24T09:58:52Z
title: Caching
tags: ["Caching responses", "Caching requests", "Cache"]
description: "How to cache responses in Tyk"
menu:
  main:
    parent: "Reduce Latency"
weight: 0 
---

The Tyk Gateway can cache responses from your upstream services.

API Clients which make subsequent requests to a cached endpoint will receive the cached response directly from the Gateway, which:
 - reduces load on the upstream service
 - provides a quicker response to the API Client (reduces latency)
 - reduces concurrent load on the API Gateway

Caching is best used on endpoints where responses infrequently change and are computationally expensive for the upstream service to generate.

### Caching with Tyk

Tyk uses Redis to store the cached responses and, as you'd expect from Tyk, there is lots of flexibility in how you configure caching so that you can optimise the performance of your system.

There are two approaches to configure caching for an API deployed with Tyk.

 - the [basic]({{< ref "basic-config-and-security/reduce-latency/caching/global-cache">}}) approach is to enable caching for all requests to an API for which it is safe to do so. This is termed "Global" or "[Safe Request]({{< ref "caching#safe-requests">}})" caching and is applied at the API (rather than endpoint) level; please note that the scope is restricted to the API definition, it is not globally applied across the portfolio of APIs deployed on your Tyk Gateway.
 - more [advanced]({{< ref "basic-config-and-security/reduce-latency/caching/advanced-cache">}}) options allow you to apply more selective rules at the individual endpoint level. This gives granular control over which paths are cached, and allows you to vary cache configuration across API versions.

Using the per-endpoint approach, you can selectively cache the responses to all requests, only those from specific paths or only responses with specific status codes returned by the API. You can even cache dynamically based upon instruction from the upstream service received within the response.

Caching is enabled by default at the Gateway level, but no caching will happen until the API Definition is configured to do so.

## Cache Terminology and Features

#### Cache Key
Cache keys are used to differentiate cached responses, such that slight variations in the request can generate different cache keys. Depending how you configure the cache, this can enable API Clients to receive different cached responses when accessing the same API endpoint.

This makes for a very granular cache, which may result in duplication of cached responses. This is preferable to the cache not being granular enough and therefore rendering it unsuitable for use, such that two API Clients receive the same cached response when this is not desired.

The cache key is calculated using many factors:
 - request HTTP method
 - request URL (API path/endpoint)
 - keys and values of any headers specified by `cache_by_headers` property
 - hash of the request body
 - API Id of the requested API
 - value of the authorization header, if present, or if not, the API Client IP address

#### Cache Value
The value stored in the cache is a base64 encoded string of the response body. When a subsequent request matches the cache key (a cache hit), Tyk decodes the cache value and  returns this to the API Client that made the request.

#### Indicating a Cached Response
When a request causes a cache hit, the Gateway will add a special header to indicate that the response being received is from a cache:
 - `X-Tyk-Cached-Response` is added to the response header with the value `1`

The API Client can use this to identify cached responses from non-cached responses.

#### Global Cache (Safe Requests)  
We define a <b>safe request</b> as any category of API request that is considered cacheable without causing any undesired side effects or security concerns. These are requests made using the HTTP methods GET, HEAD or OPTIONS that do not modify data and can be safely cached for performance gains (i.e. they should be idempotent and so good candidates for caching). If these methods are not idempotent for your API, then you should not use safe request caching.

Safe request caching is enabled by setting the `cache_all_safe_requests` option to `true`, or by checking the equivalent checkbox in the Dashboard UI.

This mode of operation is referred to as Global Caching because it is applied globally within the scope of a single API. Picking this approach will override any per-endpoint (per-path) caching configuration, so it’s not suitable if granular control is required.

#### Cache Timeout
The cache timeout (Time-To-Live or TTL) value can be configured per API and is the maximum age for which Tyk will consider a cache entry to be valid. You should use this to optimise the tradeoff between reducing calls to your upstream service and potential for changes to the upstream data.

If the timeout has been exceeded when a request is made to a cached API, that request will be passed to the upstream and the response will (if appropriate) be used to refresh the cache.

The timeout is configured in seconds.

#### Cache Response Codes
You can configure Tyk to cache only responses with certain HTTP status codes (e.g. 200 OK) for example to save caching error responses. You can configure multiple status codes that will be cached for an API, but note that this applies only to APIs that return with an HTTP status code in the response.

#### Dynamic Caching
By default Tyk maintains its response cache with a separate entry for each combination of API key (if authorisation is enabled), request method and request path. Dynamic caching is a more flexible method of caching API responses based on header or body content rather than just the request method and path. This allows for more granular caching control and maintaining separate caches for different users or request properties.

#### Upstream Cache Control
Upstream cache control refers to caching API responses based on instructions provided by the upstream service within the response headers. This allows the upstream service to have more control over which responses are cached and for how long.