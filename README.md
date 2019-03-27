# Sockpuppet: record and replay HTTP traffic
Sockpuppet is a mostly-transparent HTTP proxy that records sessions into files
that can be parameterized to form things like unit and performance tests. The
idea is that if you do something with a browser, you can automate it with
sockpuppet.


## Usage
```sh
# Proxy port 3001 to localhost:3000, recording the resulting script to output:
$ ./sockpuppet 3001 localhost:3000 > output
```

You can now issue web requests to `http://localhost:3001`, each of which will be
bidirectionally logged into `output`.

Sockpuppet isn't a completely transparent proxy. In particular, it applies a few
HTTP-level transformations that make it easier to model the resulting traffic:

1. HTTP/2 requests are rewritten as HTTP/1.1.
2. The `Host:` header is fixed up to represent the destination as sockpuppet
   sees it, not as the browser connecting to sockpuppet. Note that we don't fix
   other headers, like `Origin` or `Referer`.
3. `Sec-WebSocket-Extensions:` is removed from outbound requests. This disables
   per-message deflate for now until I implement it.


## Output format
TSV containing unidirectional events, each occupying a single row. Requests and
responses are matched up using connection IDs, each of which is an integer. All
headings (e.g. HTTP headers) and data are preserved verbatim and encoded as hex
strings. For example, here's what a conversation with a server might look like
(Sockpuppet does generate a `notes` column, and the ellipses wouldn't appear in
real outputs):

| Starttime        | Endtime          | ConnID | Up/down | State  | Notes           | Headings     | Data      |
| ---------------- | ---------------- | ------ | ------- | ------ | --------------- | ------------ | --------- |
| 1553541453.1279  | 1553541453.1279  | 1      | up      | `http` | GET / HTTP/1.1  | `4745542`... |           |
| 1553541567.10688 | 1553541567.10688 | 2      | up      | `http` | GET /favico...  | `4745542`... |           |
| 1553541568.51422 | 1553541568.51422 | 2      | down    | `http` | HTTP/1.1 404... | `4854545`... |           |
| 1553541569.4118  | 1553541569.5256  | 1      | down    | `http` | HTTP/1.1 200 OK | `4854545`... | `3c21`... |

Sockpuppet applies three transformations that deviate from the literal data
traveling over the socket:

1. Chunked HTTP transfers are un-chunkified in the `data` column (both ways).
2. Websocket packets are unmasked in `data` even though the `headings` column
   may contain a frame with masking. Any continuation frame headers are dropped.
3. Deflate/gzip content is decoded.

It's also worth noting that HTTP request headers are logged in their rewritten
form to make the request/response conversation more coherent/replayable.


## Authors
- [Spencer Tipping](https://github.com/spencertipping)


## License
Sockpuppet is released under the MIT license. See [LICENSE.md](LICENSE.md) for
details.
