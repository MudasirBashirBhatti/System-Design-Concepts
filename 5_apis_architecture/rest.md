# REST (Representational State Transfer)

REST is an architectural style for designing networked applications. It relies on a stateless, client-server communication protocol, almost always HTTP.

## Core Concepts

- **Resources**: Everything is a resource (e.g., a User, a Product) identified by a unique URI.
- **HTTP Methods**: Standard operations are mapped to HTTP verbs:
  - `GET` (Read)
  - `POST` (Create)
  - `PUT`/`PATCH` (Update)
  - `DELETE` (Delete)
- **Statelessness**: Every request contains all the information needed to understand and process it.

## Pros & Cons

### Pros

- **Simple**: Easy to understand, build, and scale using standard web infrastructure.
- **Cacheable**: Built-in HTTP caching mechanisms work out of the box.
- **Decoupled**: Client and server can evolve independently.

### Cons

- **Over-fetching**: Returns entire data models even if you only need a single field.
- **Under-fetching**: Requires multiple API hits to get related data (e.g., `/users/1` then `/users/1/posts`).
- **No Strict Contract**: Keeping documentation (like Swagger) synced with actual code can be error-prone.

## Best Use Cases

- Public APIs meant for third-party consumption.
- Standard CRUD-based web applications.
