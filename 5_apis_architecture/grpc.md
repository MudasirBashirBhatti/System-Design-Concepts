# gRPC (Google Remote Procedure Call)

gRPC is a modern, open-source, high-performance Remote Procedure Call framework that can run in any environment.

## Core Concepts

- **Protocol Buffers**: Uses Google's open-source mechanism for serializing structured data into a compact binary format.
- **HTTP/2**: Operates exclusively over HTTP/2, enabling multiplexing, header compression, and bidirectional streaming.
- **Code Generation**: Automatically generates client and server code stubs across multiple programming languages from a single `.proto` file.
- **RPC Model**: Feels like calling a local function inside your code, hiding the network layer entirely.

## Pros & Cons

### Pros

- **Blazing Fast**: Binary serialization is much smaller and faster to process than text-based JSON.
- **Streaming**: Supports point-to-point streaming (Client streaming, Server streaming, and Bidirectional streaming).
- **Type Safety**: The strict contract guarantees both sides match exactly, eliminating api drift.

### Cons

- **Not Browser Friendly**: Web browsers cannot natively interact with gRPC services without a proxy (gRPC-Web).
- **Human-Unreadable**: Binary payloads cannot be easily read or debugged using basic network tools without decoding.
- **Steep Learning Curve**: Requires understanding Protocol Buffers and managing strict build pipelines.

## Best Use Cases

- Internal microservice-to-microservice communication.
- Low-latency, high-throughput systems.
- Real-time data streaming applications or IoT networks.
