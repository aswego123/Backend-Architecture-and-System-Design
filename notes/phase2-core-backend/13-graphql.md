# GraphQL: when it helps, when it hurts

> Phase 2 · Tags: `api` `graphql`

## 1. The concept
GraphQL = a query language for APIs. The client specifies exactly which fields it wants; the server returns just those. One endpoint, schema-typed, with `query`, `mutation`, `subscription`.

## 2. The rule / the why
- Solves over-/under-fetching in REST when clients have diverse needs (mobile vs web).
- Strongly typed schema, great tooling (introspection, GraphiQL).
- Cost: complexity on the server (resolvers, N+1 risk, auth per field), harder caching (no HTTP cache by URL).

## 3. Java-specific behavior
- Spring for GraphQL (official, on top of `graphql-java`).
- Schema-first: `.graphqls` files define types/queries; you implement resolvers (`@QueryMapping`, `@MutationMapping`).
- Use the **DataLoader** pattern to batch resolver calls and dodge N+1.

```java
@QueryMapping
public User user(@Argument Long id) { return repo.find(id); }

@SchemaMapping(typeName = "User", field = "orders")
public List<Order> orders(User user) { return orderRepo.byUser(user.id()); }
```

## 4. System design angle
- Excellent for BFF (Backend-for-Frontend) when one backend serves many clients.
- Federation (Apollo Federation) composes a single graph from many services — powerful but operationally heavy.
- Subscriptions over WebSocket for live updates.

## 5. Common mistakes / traps
- N+1 in resolvers — solve with DataLoader batching.
- Exposing every entity field — auth must be enforced per field, not just per endpoint.
- Allowing arbitrarily deep queries → DOS risk. Cap depth and complexity.
- Treating mutations like REST PUT/PATCH — design them as use-case actions instead.
- Choosing GraphQL because it's trendy when REST would do.

## 6. Revision checklist
- One problem GraphQL solves: ______
- One it creates: ______
- DataLoader purpose: ______
- One DOS-shaped concern unique to GraphQL: ______
