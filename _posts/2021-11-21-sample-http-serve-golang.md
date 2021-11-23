---
layout: post
title: "How to start the simplest http server in golang"
description: ""
comments: true
keywords: ""
---


```go
package main

import (
	"log"
	"net"
	"net/http"
	"time"
)

func timeHandler(format string) http.Handler {
	fn := func(w http.ResponseWriter, r *http.Request) {
		tm := time.Now().Format(format)
		w.Write([]byte("The time is: " + tm))
	}
	return http.HandlerFunc(fn)
}

func main() {
	// Note that we skip creating the ServeMux...

	var format string = time.RFC1123
	th := timeHandler(format)

	// We use http.Handle instead of mux.Handle...
	http.Handle("/time", th)

	log.Println("Listening...")
	// And pass nil as the handler to ListenAndServe.

	// Alternative:
	// http.ListenAndServe(":3000", nil)

	l, _ := net.Listen("tcp", ":9999")
	_ = http.Serve(l, nil)
}

```


Terminal 1
```go
➜ go run sample_httpServe.go
2021/11/21 19:28:06 Listening...
```


Terminal 2
```go
➜ curl 127.0.0.1:9999/time
The time is: Sun, 21 Nov 2021 19:28:21 CST%
```
