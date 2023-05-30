# goaxios - inspired by the popular JavaScript axios

A lightweight package that makes it easier to send Rest and GraphQL requests in Golang.
For every request you make, Goaxios returns the http response object, the raw response body in bytes, the parsed response body in a struct or interface, and an error object.

## Features

- [x] Create and run REST HTTP requests
- [x] Basic configuration for REST HTTP requests
- [x] Validate Goaxios Struct before running request
- [x] Interceptors for before Request and after Response
- [ ] Create and run GraphQL HTTP requests
- [ ] Basic configuration for GraphQL HTTP requests
- [ ] Download file to a destination
- [ ] Upload file from a source
- [ ] Upload and download progress
- [ ] JavaScript `Promise.all()` style to run multiple requests
- [ ] Auto build path from path parameters like `/users/{id}`

## Installation

```bash
go get github.com/opensaucerer/goaxios
```

## Usage for REST HTTP requests

### Run a simple GET request

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)
a := goaxios.GoAxios{
    Url:    "https://anapioficeandfire.com/api/houses/1",
    Method: "GET",
}
r, b, d, err := a.RunRest()
if err != nil {
    fmt.Printf("err: %v", err)
}
fmt.Printf("Response Object: ", r)
fmt.Printf("Raw Body in Bytes: ", b)
fmt.Printf("Parsed Body: ", d)
```

### POST request with query parameters

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

a := goaxios.GoAxios{
    Url:    "https://anapioficeandfire.com/api/houses",
    Method: "POST",
    Query: map[string]string{
        "name": "House Stark",
        "region": "The North",
    },
}
_, _, _, err := a.RunRest()
if err != nil {
    fmt.Printf("err: %v", err)
}
```

### POST request with body

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

a := goaxios.GoAxios{
    Url:    "https://anapioficeandfire.com/api/houses",
    Method: "POST",
    Body: map[string]string{
        "name": "House Stark",
        "region": "The North",
    },
}
_, _, _, err := a.RunRest()
if err != nil {
    fmt.Printf("err: %v", err)
}
```

### GET request with custom response struct

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

type House struct {
    Name    string `json:"name"`
    Region  string `json:"region"`
    Words   string `json:"words"`
    Seats   []string `json:"seats"`
}
a := goaxios.GoAxios{
    Url:    "https://anapioficeandfire.com/api/houses/1",
    Method: "GET",
    ResponseStruct: &House{},
}
_, _, _, err := a.RunRest()
if err != nil {
    fmt.Printf("err: %v", err)
}
```

### Request With bearer token

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

type ResponseStruct struct {
  Login            string `json:"login"`
  Id               int    `json:"id"`
  NodeID           string `json:"node_id"`
  URL              string `json:"url"`
  ReposURL         string `json:"repos_url"`
  EventsURL        string `json:"events_url"`
  HooksURL         string `json:"hooks_url"`
  IssuesURL        string `json:"issues_url"`
  MembersURL       string `json:"members_url"`
  PublicMembersURL string `json:"public_members_url"`
  AvatarURL        string `json:"avatar_url"`
  Description      string `json:"description"`
}
token := ""
a := goaxios.GoAxios{
  Url:            "https://api.github.com/user/orgs",
  Method:         "GET",
  ResponseStruct: []ResponseStruct{},
  BearerToken:    token,
}
_, _, response, err := a.RunRest()
if err != nil {
  fmt.Printf("err: %v", err)
}
fmt.Println(response)
```

### URL encoded POST request

```go
package main

import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

r := goaxios.GoAxios{
    Url:     "https://api.twitter.com/2/oauth2/token",
    Method:  "POST",
    Headers: map[string]string{
        // needs to be empty to prevent goaxios from setting content-type to application/json
    },
    Query: map[string]interface{}{
        "grant_type":    "refresh_token",
        "refresh_token": refreshToken,
        "client_id":     config.Env.TwitterClientID,
    },
}
_, _, response, err := r.RunRest()
if err != nil {
  fmt.Printf("err: %v", err)
}
fmt.Println(response)
```

## Interceptors

### Request

```go
package main
import (
    "fmt"
    "github.com/opensaucerer/goaxios"
)

a := goaxios.GoAxios{
  Url:    "https://api.twitter.com/2/oauth2/token",
  Method: "POST",
  Interceptor: goaxios.Interceptor{
    Request: func(req *goaxios.GoAxios) *goaxios.GoAxios {
      // modify the request as needed
      req.BearerToken = "token"
      req.Headers = map[string]string{
        "Content-Type": "application/json",
      }
      req.Body = map[string]string{
        "key": "value",
      }
      return req
    },
    Response: func(resp *http.Response) *http.Response {
      // do something with the response - logging/error handling or something
      if resp.StatusCode != 200 {
        panic("not OK")
      }
      return resp
    },
  },
}

_, _, response, err := a.RunRest()
if err != nil {
  t.Errorf("err: %v", err)
}
fmt.Println(response)
```

## Usage for GraphQL HTTP requests

## Contributing

Contributions are welcome. Please open a [pull request](https://github.com/opensaucerer/goaxios/pulls) or open an [issue](https://github.com/opensaucerer/goaxios/issues) to discuss the change you wish to make.
