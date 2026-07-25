# 🧠 Backend Engineering Decisions

> Why AireenShop's backend was designed this way.

---

# 🌱 Introduction

AireenShop was not built by choosing technologies first.

The backend decisions came from practical questions:

* How do we keep commerce reliable?
* How do we add AI without losing control?
* How do we support future growth without unnecessary complexity?
* How do we build something maintainable for a real business?

The guiding principle:

> The AI can assist the customer, but the backend must protect the business.

AireenShop is designed around a simple separation:

```text
Customer Experience

        +

AI Intelligence

        +

Business Reliability
```

Each layer has a clear responsibility.

---

# 🏛️ Decision: Django As The Commerce Foundation

## The Decision

Django was selected as the main commerce backend.

---

## The Reason

AireenShop is not only an AI application.

It is still a commerce system.

Commerce requires strong control over:

* Products
* Customers
* Orders
* Authentication
* Payments
* Business rules
* Data consistency

The backend must remain predictable.

AI capabilities can evolve quickly.

Business rules cannot.

---

## Why Not Let AI Handle Everything?

A tempting approach is:

> "Let the AI manage the whole shopping experience."

However, AI should not become the source of business truth.

Example:

Customer:

> "Can I order this cake?"

AI may understand the request.

But only the backend should decide:

* Does the product exist?
* Is it available?
* What is the actual price?
* Can the order proceed?

The responsibility stays clear:

```text
AI understands.

Backend decides.
```

---

# 🧩 Decision: Modular Monolith Instead Of Full Microservices

## The Decision

AireenShop uses a modular monolith as the commerce foundation.

---

## The Reason

Microservices are useful when there is a real need for independent scaling and ownership.

However, splitting every component too early can create problems:

* More infrastructure
* More deployments
* More network communication
* More operational complexity

For a real bakery business, unnecessary complexity creates more risk.

---

## The Trade-Off

Instead of:

```text
Many independent services

=

More complexity
```

AireenShop uses:

```text
One strong commerce core

+

Specialized capabilities where needed
```

---

Benefits:

* Strong data consistency
* Easier maintenance
* Faster development
* Lower operational overhead
* Clear ownership boundaries

---

# ⚡ Decision: Separate AI Processing From Commerce Logic

## The Decision

AI processing runs separately through FastAPI.

---

## The Reason

AI development changes rapidly.

Models improve.
Prompts evolve.
Capabilities expand.

Commerce logic changes differently.

Separating them allows:

* AI experimentation without affecting transactions
* Independent model improvements
* Cleaner system boundaries
* Reduced risk to business operations

---

The principle:

> Intelligence can change faster than business rules.

---

# 🧠 Decision: Intent Recognition Before Product Retrieval

## The Decision

AireenShop does not immediately search products from customer messages.

---

## The Reason

Customers do not think like databases.

They describe situations.

Example:

Customer:

> "My daughter has a birthday tomorrow."

A keyword-based system sees:

```text
birthday
```

Ate Aireen understands:

```text
Occasion:
Birthday

Goal:
Find a suitable celebration product

Need:
Recommendation

Emotion:
Create a memorable moment
```

---

Understanding comes before searching.

The customer does not need to know:

* Product categories
* Database terms
* Exact product names

They can simply explain what they need.

---

# 🔎 Decision: MeiliSearch For Product Discovery

## The Decision

MeiliSearch was added as a dedicated product discovery layer.

---

## The Reason

The database is excellent for storing business data.

However, product discovery requires a different experience.

Customers search using:

* Natural language
* Partial ideas
* Preferences
* Descriptions

Examples:

> "Something for sharing."

> "Something affordable."

> "A cake that is not too sweet."

---

The responsibility separation:

```text
AI

Understands the customer


MeiliSearch

Finds possible products


Django

Confirms business reality
```

---

# 🔐 Decision: Dual Authentication Strategy

## The Decision

AireenShop uses different authentication approaches for different users:

* HTTP-only JWT authentication for storefront customers
* Django authentication for CMS users

---

## The Reason

The storefront and CMS are different applications serving different purposes.

They have different users:

```text
Customers

    |

    v

Storefront

    |

    v

Shopping Experience



--------------------------



Business Staff

    |

    v

CMS

    |

    v

Operations
```

Using the same authentication approach everywhere is not always the best design.

---

# 🛒 Storefront Authentication

## HTTP-only JWT

Customer authentication uses HTTP-only JWT tokens.

---

## Why?

The storefront is a modern customer-facing application.

It requires:

* API communication
* Secure sessions
* Frontend/backend separation
* Smooth customer interaction

JWT allows the frontend and backend to communicate securely while keeping authentication tokens protected from direct JavaScript access.

---

The flow:

```text
Customer Login

        |

        v

Django Authentication API

        |

        v

HTTP-only JWT Cookie

        |

        v

Authenticated Requests
```

---

Benefits:

✅ Secure customer sessions
✅ Suitable for API-driven applications
✅ Works well with modern frontend architecture
✅ Keeps authentication details away from frontend logic

---

# 🏪 CMS Authentication

## Django Authentication

The CMS uses Django's built-in authentication system.

---

## Why?

The CMS is not a public customer application.

It is a business operation tool.

Its priorities are:

* Staff access control
* Administrative workflows
* Permission management
* Business security

Django provides mature authentication and authorization capabilities for internal systems.

---

Benefits:

✅ Built-in security features
✅ Permission handling
✅ Staff management
✅ Administration-focused workflows

---

# ⚖️ Why Not Use One Authentication System?

Using one authentication method everywhere may appear simpler.

However:

> Different users have different responsibilities and different security requirements.

The design follows:

```text
Customer Side

HTTP-only JWT

Goal:
Convenient and secure shopping


--------------------------


Business Side

Django Authentication

Goal:
Controlled operational access
```

---

The principle:

> Authentication should match the role and risk of the user.

---

# 🔐 Decision: Backend-Owned Business Rules

## The Decision

Business rules remain inside the backend.

---

## The Reason

AI systems can misunderstand.

Business systems must remain predictable.

Example:

AI:

> "The customer may like this product."

Backend:

> "This product exists, is available, and follows business rules."

---

This protects:

* Customer trust
* Business accuracy
* Data integrity

---

# 🛡️ Decision: AI Safety Boundaries

## The Decision

Ate Aireen follows strict limitations.

---

The AI can:

✅ Recommend available products
✅ Explain confirmed information
✅ Guide customer decisions
✅ Assist shopping conversations

---

The AI cannot:

❌ Invent products
❌ Create fake prices
❌ Claim unavailable items exist
❌ Override business rules

---

## The Reason

A useful AI is not the one that always answers.

A useful AI is the one customers can trust.

---

# 🤖 Decision: Different AI Models For Different Responsibilities

## The Decision

AireenShop uses different AI models depending on the task.

---

# 🛒 Storefront AI

## GPT-OSS-120B

Used for customer interaction.

Responsibilities:

* Conversation understanding
* Recommendations
* Intent recognition
* Shopping assistance

Priority:

Better customer experience.

---

# 🏪 CMS AI

## GPT-OSS-20B

Used for internal assistance.

Responsibilities:

* CMS assistance
* Content generation
* Administrative support
* Business tools

Priority:

Operational efficiency.

---

## The Reason

One model does not need to solve every problem.

The correct question is:

> "What intelligence level does this task actually require?"

---

# ☁️ Decision: Design For Limited Infrastructure

## The Decision

AireenShop was validated on a small cloud environment.

Example:

```text
Google Cloud e2-micro

2 vCPU
1GB RAM
```

---

## The Reason

Real projects have constraints.

Engineering quality should not depend only on expensive infrastructure.

The challenge:

> How much capability can we deliver with limited resources?

---

The architecture prioritizes:

* Efficient services
* Controlled processes
* Appropriate technology choices
* Avoiding unnecessary overhead

---

# 🔮 Decision: Design For Future Expansion

## The Decision

The backend was designed to support future capabilities.

Possible additions:

---

## More AI Agents

Examples:

* Business assistant
* Analytics assistant
* Operations assistant

---

## Voice Interaction

Future STT support:

```text
Customer Voice

        ↓

Speech Recognition

        ↓

Ate Aireen

        ↓

Commerce Assistance
```

---

## Delivery Ecosystem

Future Rider App support:

* Order workflow
* Delivery operations
* Tracking capabilities

---

# 🌟 Backend Engineering Philosophy

Every backend decision follows:

```text
Reliable Foundation

        +

Controlled Intelligence

        +

Practical Evolution
```

---

# 🏁 Final Thought

The hardest part of AI commerce is not adding AI.

The hardest part is adding intelligence without sacrificing reliability.

AireenShop's backend approach is:

> Let AI make the experience smarter. Let the backend keep the business trustworthy.
