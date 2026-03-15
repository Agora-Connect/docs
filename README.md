# docs

Design documents, ER diagrams, and database theory documentation for the Agora academic collaboration platform.

---

## Contents

| File | Description |
|------|-------------|
| `DOMAIN_DESCRIPTION.md` | Project proposal — domain overview, entities, relationships, and expected functionality |
| `design_doc.md` | Design document — ER diagram walkthrough, weak entities, 3NF normalization steps, final relational schema, and business rules |
| `er_diagram.png` | Complete ER diagram showing all 18 tables, primary/foreign keys, and cardinalities |
| `er_diagram.dbml` | ER diagram source — importable into [dbdiagram.io](https://dbdiagram.io) for interactive exploration |

---

## About Agora

Agora is a campus-exclusive academic collaboration platform for verified SCSU students (`@stcloudstate.edu`). It provides course-scoped Q&A, resource sharing, study groups, and a social feed — all backed by a MySQL database designed from scratch by the team.

**Live application:** [Agora on Railway](https://web-production-48b9b.up.railway.app)

---

## Architecture

```
Flask App (Agora repo)
├── Jinja2 templates        — server-side rendering
├── SQLAlchemy ORM          — data models in app/models.py
├── Blueprint routing       — auth, posts, forum, groups, events, etc.
├── MySQL (Railway)         — production database (designed in database repo)
└── Huskies Connect API     — live campus events, news, organizations
```

---

## Database Design Summary

The relational schema contains 18 tables normalized to 3NF. Full details are in `design_doc.md`.

**Weak entities:** Enrollment (depends on User + Course), BorrowRequest (depends on Resource)

**Self-referential relationship:** Follow (User follows User)

**Many-to-many junctions:** PostTag, ProblemTag, Enrollment

Key business rules enforced at the database level are documented in `design_doc.md` Section 5.

---

## Organization

| Repository | Purpose |
|-----------|---------|
| [Agora](https://github.com/Agora-Connect/Agora) | Production Flask application |
| [database](https://github.com/Agora-Connect/database) | MySQL schema, queries, indexes, transactions, MongoDB component |
| [docs](https://github.com/Agora-Connect/docs) | **This repo** — design documents and ER diagrams |

---

## License

MIT License
