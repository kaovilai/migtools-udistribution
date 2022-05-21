# udistribution [![Go](https://github.com/kaovilai/udistribution/actions/workflows/go.yml/badge.svg)](https://github.com/kaovilai/udistribution/actions/workflows/go.yml)[![codecov](https://codecov.io/gh/kaovilai/udistribution/branch/main/graph/badge.svg?token=tmGT4hOtQb)](https://codecov.io/gh/kaovilai/udistribution)[![Total alerts](https://img.shields.io/lgtm/alerts/g/kaovilai/udistribution.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/kaovilai/udistribution/alerts/)[![Go Report Card](https://goreportcard.com/badge/github.com/kaovilai/udistribution)](https://goreportcard.com/report/github.com/kaovilai/udistribution)[![License](https://img.shields.io/:license-apache-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
Go library providing a client to interface with storage drivers of [distribution/distribution](https://github.com/distribution/distribution) without a listening HTTP server.

## Goal:
- Given a config and/or environment variables conforming to [available configurations](https://docs.docker.com/registry/configuration/)
  - a client interface can be initialized to access functions enabling access to methods in [distribution api spec](https://github.com/opencontainers/distribution-spec/blob/main/spec.md#api) without a listening registry HTTP Server by exposing ServeHTTP method.

Making it easier for Go programs to consume APIs on a needed basis without a listening server. This approach maybe more secure in an environment where it is not practical to obtain TLS certificates from a trusted certificate authorities, such as an unpredictable hostname/ip address.

Current functionality:
- [x] Initialize client with config string and/or environment variables
- [x] ServeHTTP method can be accessed after initialization

TODO:
- [ ] implement [copy.Image()](https://github.com/containers/image/blob/3c83b65b71650f25c11d9b8585f304bd8299dd00/copy/copy.go#L186) function that copies an image from one running registry to use ServeHTTP from client in this library either here or in a separate library.
- [ ] [oci_src](https://github.com/containers/image/blob/7152f888b90d2f3cd7a633246ceba30f5cd49cc3/oci/layout/oci_src.go), [oci_dest](https://github.com/containers/image/blob/7152f888b90d2f3cd7a633246ceba30f5cd49cc3/oci/layout/oci_dest.go) also need to be implemented.

## Getting Started
Usage example as [seen in test](https://github.com/kaovilai/udistribution/blob/aa22efb91d74e7412c437eb618cc02f4ad46f28a/pkg/client/client_test.go#L73-L86)
```go
  gotClient, err := NewClient(tt.args.configString, tt.args.envs)
  if (err != nil) != tt.wantErr {
    t.Errorf("NewClient() error = %v, wantErr %v", err, tt.wantErr)
    return
  }
  rr := httptest.NewRecorder()
  rq, err := http.NewRequest("GET", "/v2/", strings.NewReader(""))
  if err != nil {
    t.Fatal(err)
  }
  gotClient.GetApp().ServeHTTP(rr, rq)
  if rr.Result().StatusCode != http.StatusOK && !tt.wantErr {
    t.Errorf("NewClient() = %v, want %v", rr.Result().StatusCode, http.StatusOK)
  }
```
First you call `NewClient` with a config string and environment variables.
Then you call the client's `ServeHTTP` method with a desired HTTP request.

You can use `httptest.NewRecorder` to record the response.
## Known issues:
Prometheus metrics config must be disabled.

## NOTICE:
- This library contains some parts from [distribution/distribution](https://github.com/distribution/distribution) which is licensed under the Apache License 2.0.
  - Some parts has been modified to accommodate usage in this library.
  - A copy of the original distribution/distribution license is included in the repository at [LICENSE](LICENSE)