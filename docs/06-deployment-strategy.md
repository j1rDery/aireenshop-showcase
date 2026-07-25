# ☁️ Deployment Strategy

> Building a production-ready system with practical infrastructure decisions.

---

# 🌱 Introduction

AireenShop's deployment strategy follows a simple principle:

> Good engineering decisions reduce unnecessary infrastructure requirements.

The goal was not to build the biggest infrastructure possible.

The goal was to create a reliable production system that:

* Runs efficiently
* Remains maintainable
* Can operate under realistic constraints
* Has a clear path for future growth

Infrastructure supports the application.

The application should not depend on excessive infrastructure to function correctly.

---

# ☁️ Decision: Validate Efficiency Under Constraints

## The Decision

AireenShop was initially deployed and tested on a small cloud environment:

```text
Google Cloud e2-micro

2 vCPU
1GB RAM
```

---

## The Reason

The purpose was not to permanently limit the system to small infrastructure.

The purpose was to validate:

* Resource efficiency
* Service boundaries
* Operational stability
* Real-world deployment practicality

---

A common assumption is:

> "Good systems require expensive infrastructure."

AireenShop follows a different principle:

> "Good engineering decisions reduce unnecessary infrastructure requirements."

---

# 🧪 The e2-micro Environment As A Validation Platform

Running under constrained resources helped identify:

* Memory usage
* Service efficiency
* Unnecessary processes
* Performance bottlenecks

This encouraged better engineering decisions:

* Lightweight services
* Controlled background processes
* Appropriate technology choices
* Efficient deployment workflows

---

The constrained environment also encouraged careful evaluation of every component introduced into the stack.

Every service needed to answer:

> "What customer value does this provide?"

---

The goal was not:

> "Make the system stay small forever."

The goal was:

> "Build a system that can grow from a strong foundation."

---

# 🤖 AI Capability Validation

AireenShop was not only tested as a website.

The AI commerce experience was also validated under limited resources.

---

The important question was not:

> "Can we connect an LLM?"

The important question was:

> "Can AI provide meaningful customer assistance while remaining efficient and controlled?"

---

The architecture separates responsibilities:

```text
Customer Request

        |

        v

FastAPI AI Orchestrator

        |

        v

LLM Processing

        |

        v

Commerce/Search Systems
```

---

The AI does not handle everything.

Instead:

* Intent recognition determines the customer's need
* Product truth remains in the commerce backend
* Search handles product discovery
* AI provides assistance and guidance

---

This prevents unnecessary AI usage and keeps the system predictable.

---

# 📈 Scaling Beyond e2-micro

## The Decision

AireenShop is not designed to remain on e2-micro permanently.

The architecture is designed so infrastructure can scale as customer demand grows.

---

The scaling path:

```text
Validation Environment

e2-micro

        |

        v

Larger Compute Instance

        |

        v

Dedicated Workloads

        |

        v

Distributed Scaling
```

---

Possible future improvements:

## Increasing Resources

Examples:

* More CPU capacity
* More memory
* Larger database resources
* Higher network capacity

---

## Separating Workloads

Future workloads may become independent:

* AI processing workers
* Background task workers
* Search infrastructure
* Delivery services
* Analytics systems

---

The principle:

> Scale when the business requires it, not before.

---

# 🏗️ Production Architecture Overview

AireenShop uses a modular service architecture:

```text
                         Internet

                            |

                            v

                          Nginx

                            |

        +-------------------+-------------------+

        |                   |                   |

        v                   v                   v


     Next.js             Django              FastAPI

   Storefront            Commerce           Tindera AI


                            |

                            v


                       Data Services


             +--------------+--------------+

             |                             |

             v                             v


        PostgreSQL                   MeiliSearch

        Business Data               Product Discovery
```

---

Each service has a clear responsibility.

---

# ⚛️ Next.js Storefront

## Responsibility

Customer-facing experience.

Handles:

* Product browsing
* Customer interaction
* Basket experience
* Checkout flow
* Tindera interface

---

The storefront focuses on:

> Making the customer's journey simple.

---

# 🏪 Django Ninja Backend

## Responsibility

Commerce authority.

Handles:

* Products
* Customers
* Orders
* Authentication
* Business rules

---

The backend remains the source of truth.

---

# 🤖 FastAPI AI Orchestrator

## Responsibility

AI interaction layer.

Handles:

* LLM communication
* Intent recognition
* AI workflow coordination
* Tindera behavior

---

The AI assists.

The backend decides.

---

# 🔎 MeiliSearch Product Discovery

## Responsibility

Fast product discovery.

Handles:

* Product searching
* Customer-friendly discovery
* Recommendation support

---

The separation:

```text
AI

Understands customer intent


        ↓


MeiliSearch

Finds possible products


        ↓


Django

Confirms business reality
```

---

# 🔄 Decision: Local-First Deployment Workflow

## The Decision

AireenShop follows a local-first deployment workflow.

---

The process:

```text
Local Development

        |

        v

Testing

        |

        v

Git Commit

        |

        v

GitHub

        |

        v

Production Sync

        |

        v

Service Restart
```

---

## The Reason

Direct production editing creates unnecessary risk.

The workflow provides:

* Clear source of truth
* Safer changes
* Easier rollback
* Better development discipline

---

The production server runs deployed versions.

Development happens locally.

---

# 📦 Decision: Next.js Build Artifact Strategy

## The Decision

The Next.js build output is created locally and deployed as an artifact.

The `.next` directory is not committed into Git.

---

## The Reason

Building Next.js directly on limited production hardware is unnecessary overhead.

The workflow:

```text
Developer Machine

        |

        v

Next.js Build

        |

        v

Upload Build Artifact

        |

        v

Production Server
```

---

Benefits:

* Faster deployments
* Less production resource usage
* Cleaner repositories
* More predictable builds

---

# 🌐 Decision: Nginx Reverse Proxy

## The Decision

Nginx acts as the public entry point.

---

Routing:

```text
/

↓

Next.js Storefront


/api/

↓

Django Ninja API


/admin/

↓

Django Administration


/cms/

↓

CMS
```

---

## The Reason

Nginx provides:

* Clean routing
* Service separation
* Production-ready request handling

---

# ⚙️ Decision: Systemd Service Management

## The Decision

Production services are managed through systemd.

---

Services:

```text
nextjs.service

django.service

fastapi.service
```

---

## The Reason

The current scale does not require unnecessary orchestration complexity.

Systemd provides:

* Process management
* Automatic restart capability
* Simple operational control
* Lower maintenance overhead

---

The principle:

> Use infrastructure that matches the current operational need.

---

# ⚖️ Decision: Avoid Premature Background Processing Infrastructure

## The Decision

AireenShop V1 does not include Celery and Redis for background task processing.

---

## The Reason

The decision was based on current workload requirements.

Additional infrastructure introduces:

* More services to maintain
* More deployment complexity
* More monitoring requirements
* More operational overhead

---

The question was not:

> "Can we add Celery and Redis?"

The question was:

> "Does the current customer experience require them?"

---

# 🧩 V1 Processing Approach

AireenShop V1 focuses on:

* Predictable request flows
* Clear service boundaries
* Simple operations
* Reliable customer experience

---

Current approach:

```text
Customer Request

        |

        v

Application Logic

        |

        v

Response
```

---

Future evolution when required:

```text
Application

        |

        v

Message Queue

        |

        v

Background Workers
```

---

Celery + Redis can be introduced when actual workload requires:

* Large background jobs
* High-volume processing
* Asynchronous workflows
* Independent worker scaling

---

The principle:

> Add complexity when it creates measurable value, not simply because it is available.

---

# 📊 Performance Validation

## The Decision

Performance was measured instead of assumed.

---

Initial Lighthouse results:

```text
Performance: 90

Accessibility: 90

Best Practices: 78

SEO: 100
```

---

Additional metrics:

```text
FCP: 1.2s

LCP: 1.2s

TBT: 150ms

CLS: 0.001

Speed Index: 1.7s
```

---

## The Reason

Performance comes from engineering decisions:

* Efficient architecture
* Controlled resources
* Proper separation of responsibilities
* Avoiding unnecessary complexity

---

# 🔐 Operational Philosophy

AireenShop deployment follows:

```text
Reliable Application

        +

Appropriate Infrastructure

        +

Controlled Complexity

        =

Sustainable System
```

---

The goal is not:

> "Use the most expensive infrastructure."

The goal is:

> "Deliver a reliable customer experience with a system that can grow."

---

# 🔮 Future Deployment Evolution

As AireenShop grows, possible improvements include:

## Infrastructure Scaling

* Larger compute instances
* Dedicated database resources
* More powerful AI processing environments

---

## Service Expansion

Possible future services:

```text
AI Workers

Delivery Platform

Notification Service

Analytics Platform
```

---

The architecture allows these components to evolve independently when needed.

---

# 🏁 Final Thought

AireenShop was not built around infrastructure size.

It was built around engineering discipline.

The e2-micro environment proved that the system could operate efficiently under constraints.

The AI layer proved that intelligent customer assistance could be delivered through careful architecture decisions.

Future growth will not require rebuilding the architecture.

It will require scaling the resources that support it.

The principle:

> Start with a strong foundation. Scale when the business demands it.
