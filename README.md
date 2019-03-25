# Sockpuppet: record and replay HTTP traffic
Sockpuppet is a mostly-transparent HTTP proxy that records sessions into files
that can be parameterized to form things like unit and performance tests. The
idea is that if you do something with a browser, you can automate it with
sockpuppet.

**NOTE:** still in development; not ready for production use yet.


## Usage
```sh
# Proxy port 3001 to localhost:3000, recording the resulting script to output:
$ ./sockpuppet 3001 localhost:3000 > output
```

You can now issue web requests to `http://localhost:3001`, each of which will be
bidirectionally logged into `output`.


## Output format
TSV containing unidirectional events, each occupying a single row. Requests and
responses are matched up using connection IDs, each of which is an integer. All
headings (e.g. HTTP headers) and data are preserved verbatim and encoded as hex
strings. For example, here's what a conversation with a server might look like
(Sockpuppet does generate a `notes` column, and the ellipses wouldn't appear in
real outputs):

| Time             | ConnID | Up/down | Protocol | Headings     | Data      | Notes           |
| ---------------- | ------ | ------- | -------- | ------------ | --------- | --------------- |
| 1553541453.1279  | 1      | up      | http     | `4745542`... |           | GET / HTTP/1.1  |
| 1553541567.10688 | 2      | up      | http     | `4745542`... |           | GET /favico...  |
| 1553541568.51422 | 2      | down    | http     | `4854545`... |           | HTTP/1.1 404... |
| 1553541569.4118  | 1      | down    | http     | `4854545`... | `3c21`... | HTTP/1.1 200 OK |

Sockpuppet also parses websocket frames, logging all of a websocket's traffic
under a single connection ID.


## Authors
- [Spencer Tipping](https://github.com/spencertipping)


## License
Sockpuppet is released under the MIT license. See [LICENSE.md](LICENSE.md) for
details.
