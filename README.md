# Agora Documentation

Official documentation repository for Agora - A university-focused academic collaboration platform

---

## What is Agora?

Agora is an academic social platform designed exclusively for verified university students to connect, collaborate, and share knowledge within their campus community. The platform provides course-scoped discussions, Q&A forums, resource sharing, and structured peer-to-peer academic support in a trusted, university-only environment.

Unlike general-purpose social media platforms designed for entertainment, Agora focuses on meaningful academic interaction through features such as course enrollment, question-and-answer systems, reputation scoring, and content tagging.

---

## Documentation Structure

This repository contains all design documents, diagrams, and specifications for the Agora project.

### Core Documents

**[design_doc.md](./design_doc.md)** - Comprehensive design specification covering:
- Project overview and problem statement
- Complete feature set and functionality descriptions
- System architecture and technology stack decisions
- Detailed database design with entities, relationships, and business rules
- Query and transaction support requirements
- NoSQL component for activity logging

**[DOMAIN_DESCRIPTION.md](./DOMAIN_DESCRIPTION.md)** - Concise domain description for project proposal submission:
- Domain overview and problem statement
- Preliminary entities with attributes and constraints
- Entity relationships and cardinality
- Expected functionality summary
- Technology stack and database design approach

### Diagrams

**[er_diagram.png](./er_diagram.png)** - Visual Entity-Relationship diagram showing all database tables, attributes, primary keys, foreign keys, and relationships

**[er_diagram.dbml](./er_diagram.dbml)** - Source DBML schema definition in dbdiagram.io format for interactive exploration and modification

---

## Project Structure

Agora is organized into four GitHub repositories under the Agora-Connect organization:

- **[docs](https://github.com/Agora-Connect/docs)** - This repository (design documents and diagrams)
- **[frontend](https://github.com/Agora-Connect/frontend)** - Flask UI, templates, and client-side logic
- **[backend](https://github.com/Agora-Connect/backend)** - FastAPI services, REST APIs, and business logic
- **[database](https://github.com/Agora-Connect/database)** - SQL schemas, sample data, queries, and indexing scripts

---

## Technology Stack

**Frontend**: Flask (server-rendered views, template rendering, UI routing)

**Backend**: FastAPI (RESTful APIs, business logic, authentication services)

**Database**:
- MySQL (relational data, normalized schema for core transactional data)
- MongoDB (activity logs, user events, analytics)

**Authentication**: JWT-based token authentication with university email verification

---

## Project Status

**Current Phase**: Foundation & Documentation (Pre-Implementation)

We are currently finalizing design artifacts before beginning development.

**Completed:**
- Comprehensive design document with full feature specifications
- Entity-relationship modeling with 18 normalized tables
- Visual ER diagram export from dbdiagram.io
- Domain description document for project proposal
- Business rules and constraint definitions
- Technology stack selection and justification

**In Progress:**
- SQL schema generation from ER diagram
- API endpoint specification and documentation

**Planned:**
- Backend API implementation (FastAPI)
- Frontend UI implementation (Flask)
- Database setup with sample data
- Authentication and authorization system
- Testing and deployment configuration

---

## Academic Context

This project is being developed as an academic database systems project demonstrating:

**Key Deliverables:**
- Project proposal with domain description and ER diagram
- Normalized relational database schema (3NF/BCNF)
- Full-stack implementation with RESTful APIs
- Complex queries involving JOINs, aggregates, and subqueries
- Transaction management for atomic operations
- Indexing strategy for query optimization
- Hybrid database architecture (SQL + NoSQL)

**Database Concepts Demonstrated:**
- Entity-Relationship modeling
- Normalization (3NF/BCNF)
- Referential integrity through foreign keys
- Business rule enforcement via constraints
- Junction tables for many-to-many relationships
- Self-referential relationships (Follow)
- Transaction support for complex workflows
- Query optimization through indexing

---

## Key Features

**User Management**: University email authentication, profile management, optional anonymity

**Feed System**: Following feed (personalized) and university feed (campus-wide)

**Course-Scoped Content**: Enrollment-based access control, course filtering, academic context

**Posts & Engagement**: Rich text posts with images and attachments, comments, upvotes, bookmarking

**Q&A Forum**: Academic problem posting, multiple answers per problem, accepted answer selection

**Tagging System**: Keyword tags for search, filtering, and trend analysis

**Reputation System**: Points for upvotes and accepted answers, quality contributor recognition

**Resource Sharing**: Textbook and note lending with borrow request workflows

**Moderation**: User reporting and content status tracking

---

## Database Design Highlights

The Agora database consists of 18 relational tables organized into logical groups:

**Core Structure**: User, Course, Enrollment (M:N junction)

**Content**: Post, Problem, Answer

**Engagement**: CommentOnPost, CommentOnProblem, CommentOnAnswer, UpvoteOnPost, UpvoteOnProblem, UpvoteOnAnswer

**Social**: Follow (self-referential on User)

**Tagging**: Tag, PostTag (M:N junction), ProblemTag (M:N junction)

**Resources**: Resource, BorrowRequest

**Business Rules Enforced:**
- One enrollment per user per course
- One answer per user per problem
- One accepted answer per problem
- One upvote per user per content item
- Only enrolled users can post in a course

For complete database documentation, see [design_doc.md](./design_doc.md) Section 5 and [DOMAIN_DESCRIPTION.md](./DOMAIN_DESCRIPTION.md) Section 3.

---

## Repository Navigation

**For project proposal reviewers**: Start with [DOMAIN_DESCRIPTION.md](./DOMAIN_DESCRIPTION.md) and [er_diagram.png](./er_diagram.png)

**For detailed technical specifications**: See [design_doc.md](./design_doc.md)

**For database schema modification**: Import [er_diagram.dbml](./er_diagram.dbml) into [dbdiagram.io](https://dbdiagram.io/d)

**For implementation**: Refer to backend, frontend, and database repositories

---

## License

MIT License - See [LICENSE](./LICENSE) for details

---

## Contributing

This is an academic project developed for educational purposes. For questions or collaboration inquiries, please refer to the individual repository documentation.

---

**Organization**: [Agora-Connect](https://github.com/Agora-Connect)

**Documentation Version**: 1.0

**Last Updated**: 2026-02-10
