# go-web-server

Simple Web Server in Go.


---

### TASKS

#### 01 - HTTP response

In this stage, your server will respond to an HTTP request with a 200 response.
HTTP response

An HTTP response is made up of three parts, each separated by a CRLF (\r\n):

1. Status line.
2. Zero or more headers, each ending with a CRLF.
3. Optional response body.

In this stage, your server's response will only contain a status line. Here's the response your server must send:

```
HTTP/1.1 200 OK\r\n\r\n
```

Here's a breakdown of the response:

```
// Status line
HTTP/1.1  // HTTP version
200       // Status code
OK        // Optional reason phrase
\r\n      // CRLF that marks the end of the status line

// Headers (empty)
\r\n      // CRLF that marks the end of the headers

// Response body (empty)
```

The tester will then send an HTTP GET request to your server:

```
$ curl -v http://localhost:4221
```

Your server must respond to the request with the following response:

```
HTTP/1.1 200 OK\r\n\r\n
```


---

#### 02 - Extract URL path

In this stage, your server will extract the URL path from an HTTP request, and respond with either a 200 or 404, depending on the path.
HTTP request

An HTTP request is made up of three parts, each separated by a CRLF (\r\n):

1. Request line.
2. Zero or more headers, each ending with a CRLF.
3. Optional request body.

Here's an example of an HTTP request:

```
GET /index.html HTTP/1.1\r\nHost: localhost:4221\r\nUser-Agent: curl/7.64.1\r\nAccept: */*\r\n\r\n
```

Here's a breakdown of the request:

```
// Request line
GET                          // HTTP method
/index.html                  // Request target
HTTP/1.1                     // HTTP version
\r\n                         // CRLF that marks the end of the request line

// Headers
Host: localhost:4221\r\n     // Header that specifies the server's host and port
User-Agent: curl/7.64.1\r\n  // Header that describes the client's user agent
Accept: */*\r\n              // Header that specifies which media types the client can accept
\r\n                         // CRLF that marks the end of the headers

// Request body (empty)
```

The "request target" specifies the URL path for this request. In this example, the URL path is /index.html.

Note that each header ends in a CRLF, and the entire header section also ends in a CRLF.
Tests

The tester will then send two HTTP requests to your server.

First, the tester will send a GET request, with a random string as the path:
```

$ curl -v http://localhost:4221/abcdefg
```

Your server must respond to this request with a 404 response:

```
HTTP/1.1 404 Not Found\r\n\r\n
```

Then, the tester will send a GET request, with the path `/`:

```
$ curl -v http://localhost:4221
```

Your server must respond to this request with a 200 response:

```
HTTP/1.1 200 OK\r\n\r\n
```


---

#### 03 - Respond with body

In this stage, you'll implement the `/echo/{str}` endpoint, which accepts a string and returns it in the response body.

A response body is used to return content to the client. This content may be an entire web page, a file, a string, or anything else that can be represented with bytes.

Your `/echo/{str}` endpoint must return a 200 response, with the response body set to given string, and with a Content-Type and Content-Length header.

Here's an example of an `/echo/{str}` request:

```
GET /echo/abc HTTP/1.1\r\nHost: localhost:4221\r\nUser-Agent: curl/7.64.1\r\nAccept: */*\r\n\r\n
```

And here's the expected response:

```
HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\nContent-Length: 3\r\n\r\nabc
```

Here's a breakdown of the response:

```
// Status line
HTTP/1.1 200 OK
\r\n                          // CRLF that marks the end of the status line

// Headers
Content-Type: text/plain\r\n  // Header that specifies the format of the response body
Content-Length: 3\r\n         // Header that specifies the size of the response body, in bytes
\r\n                          // CRLF that marks the end of the headers

// Response body
abc                           // The string from the request
```

The two headers are required for the client to be able to parse the response body. Note that each header ends in a CRLF, and the entire header section also ends in a CRLF.
Tests

The tester will then send a GET request to the `/echo/{str}` endpoint on your server, with some random string.

```
$ curl -v http://localhost:4221/echo/abc
```

Your server must respond with a 200 response that contains the following parts:

1. Content-Type header set to text/plain.
2. Content-Length header set to the length of the given string.
3. Response body set to the given string.

```
HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\nContent-Length: 3\r\n\r\nabc
```


---

#### 04 - Read header

In this stage, you'll implement the `/user-agent` endpoint, which reads the User-Agent request header and returns it in the response body.


The User-Agent header describes the client's user agent.

Your `/user-agent` endpoint must read the User-Agent header, and return it in your response body. Here's an example of a `/user-agent` request:

```
// Request line
GET
/user-agent
HTTP/1.1
\r\n

// Headers
Host: localhost:4221\r\n
User-Agent: foobar/1.2.3\r\n // Read this value
Accept: _/_\r\n
\r\n

// Request body (empty)
```

Here is the expected response:

```
// Status line
HTTP/1.1 200 OK // Status code must be 200
\r\n

// Headers
Content-Type: text/plain\r\n
Content-Length: 12\r\n
\r\n

// Response body
foobar/1.2.3 // The value of `User-Agent`
```

The tester will then send a GET request to the `/user-agent` endpoint on your server. The request will have a User-Agent header.

```
$ curl -v --header "User-Agent: foobar/1.2.3" http://localhost:4221/user-agent
```

Your server must respond with a 200 response that contains the following parts:

1. Content-Type header set to text/plain.
2. Content-Length header set to the length of the User-Agent value.
3. Message body set to the User-Agent value.

```
HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\nContent-Length: 12\r\n\r\nfoobar/1.2.3
```


---

#### 05 - Concurrent connections

The tester will create multiple concurrent TCP connections to your server. (The exact number of connections is determined at random.) After that, the tester will send a single GET request through each of the connections.

```
$ (sleep 3 && printf "GET / HTTP/1.1\r\n\r\n") | nc localhost 4221 &
$ (sleep 3 && printf "GET / HTTP/1.1\r\n\r\n") | nc localhost 4221 &
$ (sleep 3 && printf "GET / HTTP/1.1\r\n\r\n") | nc localhost 4221 &
```

Your server must respond to each request with the following response:

```
HTTP/1.1 200 OK\r\n\r\n
```


---

#### 06 - Return a file

In this stage, you'll implement the `/files/{filename}` endpoint, which returns a requested file to the client.

The tester will execute your program with a --directory flag. The --directory flag specifies the directory where the files are stored, as an absolute path.

```
$ ./your_program.sh --directory /tmp/
```

The tester will then send two GET requests to the `/files/{filename}` endpoint on your server.
First request

The first request will ask for a file that exists in the files directory:

```
$ echo -n 'Hello, World!' > /tmp/foo
$ curl -i http://localhost:4221/files/foo
```

Your server must respond with a 200 response that contains the following parts:

1. Content-Type header set to application/octet-stream.
2. Content-Length header set to the size of the file, in bytes.
3. Response body set to the file contents.

```
HTTP/1.1 200 OK\r\nContent-Type: application/octet-stream\r\nContent-Length: 14\r\n\r\nHello, World!
```

Second request

The second request will ask for a file that doesn't exist in the files directory:

```
$ curl -i http://localhost:4221/files/non_existant_file
```

Your server must respond with a 404 response:

```
HTTP/1.1 404 Not Found\r\n\r\n
```


---

#### 07 - Read request body

In this stage, you'll add support for the POST method of the `/files/{filename}` endpoint, which accepts text from the client and creates a new file with that text.

A request body is used to send data from the client to the server.

Here's an example of a POST `/files/{filename}` request:

```
// Request line
POST /files/number HTTP/1.1
\r\n

// Headers
Host: localhost:4221\r\n
User-Agent: curl/7.64.1\r\n
Accept: _/_\r\n
Content-Type: application/octet-stream // Header that specifies the format of the request body
Content-Length: 5\r\n // Header that specifies the size of the request body, in bytes
\r\n

// Request Body
12345
```

The tester will then send a POST request to the `/files/{filename}` endpoint on your server, with the following parts:

1. Content-Type header set to application/octet-stream.
2. Content-Length header set to the size of the request body, in bytes.
3. Request body set to some random text.

```
$ curl -v --data "12345" -H "Content-Type: application/octet-stream" http://localhost:4221/files/file_123
```

Your server must return a 201 response:

```
HTTP/1.1 201 Created\r\n\r\n
```

Your server must also create a new file in the files directory, with the following requirements:

1. The filename must equal the filename parameter in the endpoint.
2. The file must contain the contents of the request body.



---

#### 08 -  Compression headers

In this stage, you'll add support for the Accept-Encoding and Content-Encoding headers.

An HTTP client uses the Accept-Encoding header to specify the compression schemes it supports. In the following example, the client specifies that it supports the gzip compression scheme:

```
> GET /echo/foo HTTP/1.1
> Host: localhost:4221
> User-Agent: curl/7.81.0
> Accept: _/_
> Accept-Encoding: gzip // Client specifies it supports the gzip compression scheme.
```

The server then chooses one of the compression schemes listed in Accept-Encoding and compresses the response body with it.

Then, the server sends a response with the compressed body and a Content-Encoding header. Content-Encoding specifies the compression scheme that was used.

In the following example, the response body is compressed with gzip:

```
< HTTP/1.1 200 OK
< Content-Encoding: gzip // Server specifies that the response body is compressed with gzip.
< Content-Type: text/plain // Original media type of the body.
< Content-Length: 23 // Size of the compressed body.
< ... // Compressed body.
```

If the server doesn't support any of the compression schemes specified by the client, then it will not compress the response body. Instead, it will send a standard response and omit the Content-Encoding header.

For this extension, assume that your server only supports the gzip compression scheme.

For this stage, you don't need to compress the body. You'll implement compression in a later stage.

The tester will then send two GET requests to the `/echo/{str}` endpoint on your server.

First, the tester will send a request with this header: Accept-Encoding: gzip.

```
$ curl -v -H "Accept-Encoding: gzip" http://localhost:4221/echo/abc
```

Your server's response must contain this header: Content-Encoding: gzip.

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Encoding: gzip

... // Body omitted.
```

Next, the tester will send a request with this header: Accept-Encoding: invalid-encoding.

```
$ curl -v -H "Accept-Encoding: invalid-encoding" http://localhost:4221/echo/abc
```

Your server's response must not contain a Content-Encoding header:

```
HTTP/1.1 200 OK
Content-Type: text/plain

... // Body omitted.
```

---

#### 09 - Gzip compression

You'll add support for gzip compression to your HTTP server.

Then, the tester will send a GET request to the `/echo/{str}` endpoint on your server. The request will contain an Accept-Encoding header that includes gzip.

```
$ curl -v -H "Accept-Encoding: gzip" http://localhost:4221/echo/abc | hexdump -C
```

Your server's response must contain the following:

1. 200 response code.
2. Content-Type header set to text/plain.
3. Content-Encoding header set to gzip.
4. Content-Length header set to the size of the compressed body.
5. Response body set to the gzip-compressed str parameter.

```
HTTP/1.1 200 OK
Content-Encoding: gzip
Content-Type: text/plain
Content-Length: 23

1F 8B 08 00 00 00 00 00 // Hexadecimal representation of the response body
00 03 4B 4C 4A 06 00 C2
41 24 35 03 00 00 00
```

1. To check that your compressed body is correct, you can run echo -n <uncompressed-str> | gzip | hexdump -C.
2. It's normal for a very short string like abc to increase in size when compressed.

---
