# GraphQL

GraphQL is an open-source data query and manipulation language for APIs, and a runtime for fulfilling those queries with your existing data.

## Core Concepts

- **Single Endpoint**: All requests go to a single URL (usually `/graphql`) via `POST`.
- **Schema**: A strongly-typed definition of what data is available.
- **Queries & Mutations**:
  - `Query`: Used to fetch data.
  - `Mutation`: Used to modify data (create, update, delete).
- **Client-Driven**: The frontend dictates the exact shape of the response.

## Pros & Cons

### Pros

- **No Over/Under-fetching**: Clients request exactly what they need and nothing more.
- **Single Request**: Consolidates nested or related data resources into one round-trip.
- **Self-Documenting**: The typed schema serves as living, accurate documentation.

### Cons

- **Complexity**: Server-side setup, query optimization, and security (preventing deeply nested malicious queries) are complex.
- **Hard Caching**: Standard HTTP caching doesn't work well because requests use `POST` and a single endpoint.
- **Overhead**: Can be overkill for simple applications with uniform data needs.

## Best Use Cases

- Complex frontends (Web/Mobile) drawing data from multiple backend sources.
- Dashboards requiring deeply nested or rapidly changing data shapes.
