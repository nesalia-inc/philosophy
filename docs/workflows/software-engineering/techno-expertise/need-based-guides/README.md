# Need-Based Guides

Problem-centric documentation focusing on solving specific challenges.

---

## Purpose

Solve specific **problems** regardless of technology:

- How to cache effectively
- How to manage state
- How to design APIs
- How to handle errors

---

## Difference from Techno Courses

| Aspect | Techno Course | Need-Based Guide |
|--------|---------------|------------------|
| Focus | Single technology | Multiple technologies |
| Question | "What is X?" | "How to solve Y?" |
| Example | "What is Stripe?" | "How to handle payments?" |

---

## Example Structure

```markdown
Caching Guide
├── README.md              # Overview, when to use cache
├── strategies/
│   ├── cache-aside.md    # Strategy 1
│   ├── write-through.md  # Strategy 2
│   └── write-back.md     # Strategy 3
├── technologies/
│   ├── redis.md          # Redis implementation
│   ├── memcached.md      # Memcached implementation
│   └── in-memory.md     # In-memory implementation
├── patterns/
│   ├── invalidation.md   # Cache invalidation
│   ├── ttl.md           # Time-to-live patterns
│   └── distribution.md  # Distributed caching
└── examples/
    ├── redis-example.ts  # Redis code
    └── local-example.ts  # Local cache code
```

---

## Topics to Cover

### Technical

- **Caching** - Cache strategies, invalidation, distribution
- **Database** - SQL vs NoSQL, ORM, migrations
- **State Management** - Client, server, global
- **API Design** - REST, GraphQL, gRPC
- **Authentication** - JWT, OAuth, sessions
- **Error Handling** - Types, recovery, logging

### Architectural

- **Event-Driven** - Pub/sub, message queues
- **Microservices** - Communication, orchestration
- **CQRS/Event Sourcing** - Pattern implementations
- **Scalability** - Horizontal/vertical, sharding

### Operational

- **Monitoring** - Metrics, tracing, logging
- **Deployment** - CI/CD, containers
- **Security** - Encryption, secrets
- **Performance** - Profiling, optimization

---

## Relationship to Techno Courses

Need-based guides reference techno courses:

```
Need-Based Guide (Caching)
    │
    ├── References: Redis Course (for Redis implementation)
    ├── References: Memcached Course (for Memcached)
    └── Provides: General guidance on caching strategies
```

---

## When to Use

| Situation | Use |
|-----------|-----|
| Need to add caching | → Caching Guide |
| Need to choose database | → Database Guide |
| Need to manage state | → State Management Guide |

---

## Creating a Need-Based Guide

**Status**: Process to be defined.

---

*Document version: 1.0*
*Last updated: 2026-03-06*
