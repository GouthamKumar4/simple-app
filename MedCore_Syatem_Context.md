# MedCore -- System Context Document

## Secure Patient Management System

**Document Version:** 1.0\
**Purpose:** Define the system boundary, external actors, external
dependencies, and trust boundaries.

------------------------------------------------------------------------

# 1. What Is System Context?

System Context defines:

-   What is inside the system under design
-   What is outside the system
-   Who interacts with the system
-   What external systems it depends on
-   Where trust boundaries exist

It is the highest-level architectural view.

------------------------------------------------------------------------

# 2. System Under Design

The system under design is:

**MedCore Patient Management Application**

This includes all components built and controlled by our engineering
team.

------------------------------------------------------------------------

# 3. Internal Components (Inside System Boundary)

The following components are part of our system:

-   Web Application (Frontend)
-   Backend API
-   Business Logic Layer
-   Database
-   Audit Logging Module

These are fully controlled, deployed, and managed by our organization.

------------------------------------------------------------------------

# 4. External Actors (Users)

External actors interact with the system but are not part of it.

-   Doctor
-   Receptionist
-   Admin

They access the system via browser over the internet.

------------------------------------------------------------------------

# 5. External Systems (Dependencies)

External systems are services we depend on but do not fully control.

-   Identity Provider (Authentication + MFA)
-   Email/SMS Service (if notifications exist)
-   Monitoring Platform (if external)

These services operate independently from our application.

------------------------------------------------------------------------

# 6. High-Level Context Diagram

``` mermaid
flowchart TD
    subgraph External Actors
        Doctor
        Receptionist
        Admin
    end

    subgraph External Systems
        IdentityProvider
    end

    subgraph MedCoreSystem
        WebApp
        API
        Database
        AuditLog
    end

    Doctor --> WebApp
    Receptionist --> WebApp
    Admin --> WebApp
    WebApp --> API
    API --> Database
    API --> AuditLog
    WebApp --> IdentityProvider
```

------------------------------------------------------------------------

# 7. Trust Boundaries

Trust boundary = point where data moves from less trusted zone to more
trusted zone.

------------------------------------------------------------------------

## 7.1 Primary Trust Boundary

Between Internet Users and Application.

``` mermaid
flowchart LR
    InternetUsers -->|Untrusted Traffic| TrustBoundary
    TrustBoundary -->|Validated| WebApp
```

Why:

-   Internet traffic is untrusted
-   All inputs must be validated
-   Authentication required
-   TLS required

------------------------------------------------------------------------

## 7.2 Secondary Trust Boundary

Between Application and Identity Provider.

Reason:

-   Identity Provider is external
-   Token signatures must be validated
-   External service availability affects login

------------------------------------------------------------------------

# 8. Database Position in Context

Database is:

-   Internal component
-   Not publicly accessible
-   Accessible only through Backend API

Database must never be directly reachable from the internet.

------------------------------------------------------------------------

# 9. Availability Dependency Awareness

If Identity Provider is unavailable:

-   Users cannot log in
-   System becomes partially unavailable

This creates an external SLA dependency.

------------------------------------------------------------------------

# 10. Why System Context Is Critical

System context helps:

-   Identify security exposure points
-   Define network boundaries
-   Understand external dependencies
-   Plan availability strategies
-   Prevent architectural mistakes

Without system context, infrastructure design becomes incorrect.

------------------------------------------------------------------------

# 11. Summary

System Context defines:

-   What is inside the system
-   What is outside the system
-   Who interacts with it
-   Where trust boundaries exist

It is the first step in System Design and must be completed before
moving to component architecture or infrastructure mapping.

# 12. system vs componetns
System-Level = Black Box View
Component-Level = White Box View

Black Box:
We see only inputs and outputs.

White Box:
We see internal modules.

🔹 Why This Abstraction Is Important

In large systems:

Executives care about system-level.

Developers care about component-level.

Platform engineers must understand both.