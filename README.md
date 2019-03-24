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


## Output format
Lines of JSON, each describing a socket-level event that happened. Each output
record contains at least these three fields:

- `connid`: the ID of the TCP connection (an integer)
- `time`: a UNIX timestamp with fractional seconds
- `type`: the type of event that occurred -- more below

### Event types
- `connect`: a TCP `connect()` happened and was accepted; the socket is now open
- `disconnectd`: somebody disconnected; the socket is now closed
- **TODO:** write something really smart here


## MIT license
Copyright 2019 Spencer Tipping

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
