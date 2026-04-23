# Architecture Principles

## Single Source of Truth
Every piece of state has exactly one owner. Everything else is derived. If the same data exists in two places, they will diverge. Before adding any state, ask: "Who is the single source of truth?" If you cannot answer clearly, your architecture is wrong.

## Imports Flow Down, Never Up
```
Api -> App -> Business -> Storage
```

Dependencies point inward. Always. Outer layers depend on inner layers, never the reverse. Domain models should have zero framework imports. The business layer should compile and run without knowing what database, HTTP framework, or messaging system exists.

### Three Firewalls
1. **Packaging**: One package = one purpose
2. **Layering**: Imports go one direction (down)
3. **Domain**: Business domains do not cross-contaminate

## Make Invalid States Unrepresentable
Use the type system to prevent misuse, not documentation. If you need docs to use an API correctly, the API failed. Boolean parameters are a code smell -- name the behavior instead. Interfaces belong to the consumer, not the implementer.

## Compose, Don't Configure
Small, composable pieces that snap together beat large, configurable monoliths with option flags. This applies to:
- UI components (modifiers over properties)
- Data models (composition over inheritance)
- System architecture (microservices over monolith settings)
- Agent design (specialized agents over one agent with modes)

## State Machines, Not Spaghetti
Every screen, workflow, and process is a set of states and transitions. If you can draw the state machine, you can build the system. If you cannot draw it, your system has undefined states -- that is where bugs live.

Common states for any async operation:
```
idle -> loading -> loaded | error | empty
```

## The Dependency Rule
Dependencies point inward:
- Views depend on ViewModels/Stores
- Stores depend on Domain
- Domain depends on nothing

Domain models are pure. They have no framework imports, no I/O, no side effects. They are the most testable, portable, and long-lived code in the system.

## Offline First
The network is a lie. The local database is the source of truth. The server is a backup that eventually converges. Design for:
1. Apply mutations optimistically to local state
2. Queue changes for sync when network returns
3. Resolve conflicts when server state diverged

## Feature Flags as Architecture
Feature flags are not scattered `if/else` statements. They are an architectural concern -- centralized, observable, and cleanly gating entire features at the boundary layer.
