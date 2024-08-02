+++
categories = ["http", "Web"]
date = 2024-06-26T13:58:43Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1580163661417-3606299aba72?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDJ8fGh0dHAlMjBzdGF0dXN8ZW58MHx8fHwxNzE5NDEwNTc4fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "http-status-codes-explained-from-200-ok-to-429-too-many-requests"
tags = ["http", "Web"]
title = "HTTP Status Codes Explained: From 200 OK to 429 Too Many Requests"

+++


HTTP (Hypertext Transfer Protocol) status codes are three-digit numbers returned by web servers (such as Apache, IIS, Nginx, Kestrel, among others) to indicate the outcome of a client’s request.

These codes serve as communication tools between web clients (usually browsers and mobile applications) and servers about the outcome of the HTTP request.

Let’s discuss HTTP status codes in a developer’s context ️🔨.

#### When to Use Them

* Whenever a client makes a request (e.g. fetching a webpage or submitting a form), the server responds with an appropriate status code
* Use them to indicate whether the request succeeded, encountered an error, or needs further action

#### Why They Matter

* Status codes help us troubleshoot issues. When something goes wrong (e.g. a broken link or server error), these codes provide clues
* They guide our decision-making. For instance, a 404 (Not Found) tells us a resource isn’t available, so we can handle it gracefully

#### How to Use Them

* As developers, we encounter status codes when building APIs, handling requests, or designing error pages.
* Understanding their meanings helps us create robust, user-friendly applications.

## Table of Contents

* [Status Ranges](#status-ranges)
* [Most used Status Codes](#most-used-status-codes)
* [Common Status Codes](#common-status-codes)
* [All HTTP Status Code List](#all-http-status-code-list)

## Status Ranges

Status codes are grouped into five ranges, each with a specific meaning:

* **1xx (Informational)**: These codes indicate that the request has been received and the server is continuing to process it.
* **2xx (Successful)**: These codes signify that the request was successful or that the server accepted it.
* **3xx (Redirection)**: These codes indicate that the client must take additional actions to complete the request (e.g., follow a redirection).
* **4xx (Client Errors)**: These codes indicate that the client (browser) made an error or requested a resource that doesn’t exist.
* **5xx (Server Errors)**: These codes indicate that the server encountered an issue while processing the request.

Understanding these ranges helps diagnose issues during web development.

## Most used Status Codes

Let’s explore some frequently encountered status codes.

* **200 OK:** The server successfully processed the request and returned the requested resource
* **404 Not Found**: The requested resource does not exist on the server
* **500 Internal Server Error**: An unexpected condition occurred on the server

### Common Status Codes

* **201 Created**: This status code indicates that the server has successfully created a new resource as a result of the client’s request
* **302 Found (or 301 Moved Permanently)**: Used for redirection (temporary or permanent)
* **400 Bad Request**: When the client sends a malformed or invalid request (e.g., missing required parameters or incorrect data format), the server responds with a 400 status code
* **401 Unauthorized**: As mentioned earlier, this code indicates that the client lacks proper authentication to access a resource
* **403 Forbidden**: Unlike 401, which implies authentication failure, a 403 status code means that the client is authenticated but doesn’t have permission to access the requested resource
* **429 Too Many Requests**: When a client exceeds the rate limit (e.g., making too many requests in a short time), the server responds with a 429 status code

## All HTTP Status Code List

### Informational Responses (100–199)

1. **100 Continue**: The client should continue the request or ignore the response if the request is already finished
2. **101 Switching Protocols**: Sent in response to an Upgrade request header from the client, indicating the protocol the server is switching to
3. **102 Processing (WebDAV)**: Indicates that the server has received and is processing the request, but no response is available yet
4. **103 Early Hints**: Primarily used with the Link header, allowing preloading resources while the server prepares a response or pre-connects to an origin

### Successful Responses (200–299)

1. **200 OK**: The request succeeded. The meaning of “success” depends on the HTTP method:W**- GET**: The resource has been fetched and transmitted in the message body**- HEAD**: Representation headers are included in the response without a message body**- PUT or POST**: The resource describing the result of the action is transmitted in the message body**- TRACE**: The message body contains the request message as received by the server
2. **201 Created**: A new resource was successfully created as a result of the request (typically after POST or some PUT requests)
3. **202 Accepted**: The request has been received but not yet acted upon. It’s noncommittal, often used for batch processing or when another process handles the request
4. **203 Non-Authoritative Information**: Returned metadata differs from the origin server but is collected from a local or third-party copy
5. **204 No Content**: No content to send, but headers may be useful for caching
6. **205 Reset Content**: Instructs the user agent to reset the document that sent the request
7. **206 Partial Content**: Used when the Range header requests only part of a resource
8. **207 Multi-Status (WebDAV)**: Conveys information about multiple resources

### Redirection Messages (300–399)

1. **300 Multiple Choices**: Indicates multiple options for the requested resource
2. **301 Moved Permanently**: The requested resource has moved permanently to a new location
3. **302 Found (or 303 See Other)**: Temporary redirection
4. **304 Not Modified**: The client’s cached version of the resource is still valid
5. **307 Temporary Redirect (or 308 Permanent Redirect)**: Similar to 302 but preserves the original request method

### Client Error Responses (400–499)

1. **400 Bad Request**: The client sent a malformed or invalid request
2. **401 Unauthorized**: Authentication is required, and the client lacks proper credentials
3. **403 Forbidden**: Authenticated client lacks permission to access the resource
4. **404 Not Found**: The requested resource does not exist
5. **429 Too Many Requests**: Client exceeded the rate limit

### Server Error Responses (500–599)

1. **500 Internal Server Error**: An unexpected condition occurred on the server
2. **502 Bad Gateway**: The server, while acting as a gateway or proxy, received an invalid response from an upstream server
3. **503 Service Unavailable**: The server cannot handle the request due to temporary overloading or maintenance
4. **504 Gateway Timeout**: The server, acting as a gateway or proxy, did not receive a timely response from an upstream server
5. **599 Network Connect Timeout Error**: The network connection has timed out

Remember, these codes play a crucial role in web communication, and understanding them empowers developers to build robust applications. If you need further details or have more questions, feel free to ask!

Remember to consult the official HTTP specification for a complete list of status codes and their meanings 😊🚀

