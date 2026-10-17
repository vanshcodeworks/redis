# Redis in Go

A Redis-compatible server written from scratch in Go: the RESP wire protocol,
a TCP listener, and command dispatch.

This was a "build a Redis clone" exercise. The interesting parts are the
protocol handling and the concurrency model, not the command coverage.

## Layout

```
main.go            entrypoint and flag parsing (host, port)
config/main.go     server configuration
core/resp.go       RESP protocol encode/decode
core/resp_test.go  protocol round-trip tests
core/eval.go       command evaluation
core/cmd.go        command table and dispatch
server/sync_tcp.go TCP accept loop and connection handling
```

## Running

```bash
go run main.go -host 127.0.0.1 -port 6379
```

Then connect with any Redis client:

```bash
redis-cli -p 6379
```

## Tests

```bash
go test ./...
```

`core/resp_test.go` covers the protocol layer - encoding and decoding of the
RESP types (simple strings, errors, integers, bulk strings, arrays).

## Notes

- The Go module is named `DB`, which is not a great module path. Imports read as
  `DB/server` and `DB/core`. It works locally but is worth renaming before this
  is shared more widely.
- The server uses a synchronous (mutex-guarded) model rather than the
  single-threaded event loop real Redis uses, so concurrency is correct but not
  as fast.