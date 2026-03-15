# Agora — Project Documentation

Design documents, domain model, and architecture specification for the Agora academic collaboration platform built for St. Cloud State University students.

## About Agora

Agora is a campus-exclusive social and academic platform restricted to verified SCSU students (`@stcloudstate.edu` emails). It provides a unified space for course Q&A, resource sharing, study groups, and campus event discovery.

**Production app:** [github.com/Agora-Connect/Agora](https://github.com/Agora-Connect/Agora)
**Live deployment:** Railway (Flask + PostgreSQL)

---

## Final Architecture

The production system is a single unified Flask application:

```
Flask App (Agora repo)
├── Jinja2 templates          — server-side rendering, no separate frontend
├── SQLAlchemy ORM            — all data models in app/models.py
├── Blueprint routing         — auth, posts, forum, groups, events, etc.
├── PostgreSQL (Railway)      — production database
└── Huskies Connect API       — live campus events, news, organizations
```

**Key decisions:**
- No separate frontend or backend services — one unified Flask app
- Tailwind CSS via CDN (no build step required)
- No JavaScript — HTML forms + Jinja2 for all interactions
- Auth gated to `@stcloudstate.edu` email addresses only

---

## Domain Model

### Core Entities

| Entity | Description |
|--------|-------------|
| `User` | SCSU student account — profile, avatar, reputation score |
| `Course` | University course (e.g. CSCI 411, MATH 221) |
| `Post` | Social feed post with optional anonymity |
| `Problem` | Forum question linked to a course |
| `Answer` | Response to a Problem; one can be marked accepted |
| `Resource` | Shared academic material (notes, textbooks, past exams) |
| `Group` | Study group — public (open join) or private (invite/request) |

### Social Graph

| Entity | Description |
|--------|-------------|
| `Follow` | Directional follow between users |
| `Upvote` | Upvote on posts, problems, or answers |
| `Bookmark` | Saved posts |
| `Repost` | Repost of another user's post |
| `Comment` | Comment on a post |
| `Notification` | Unified notification feed (likes, follows, invites, answers) |
| `Message` | Direct messages between users |

### Group Membership

| Entity | Description |
|--------|-------------|
| `GroupMembership` | M:N junction — User ↔ Group |
| `GroupInvitation` | Admin invites a user to a private group |
| `GroupJoinRequest` | User requests to join a private group |

---

## Features

1. **Q&A Forum** — Course-scoped questions with accepted answers and reputation scoring
2. **Resource Hub** — Upload and borrow academic materials
3. **Study Groups** — Public/private with invite and join-request workflows
4. **Class Pages** — Course-scoped discussion boards
5. **Social Feed** — Posts, reposts, follows, bookmarks
6. **Campus Events** — Live from Huskies Connect (SCSU's event platform)
7. **Direct Messages** — Private messaging between students
8. **Notifications** — Unified feed for all activity

---

## Repository Index

| Repository | Purpose |
|-----------|---------|
| [Agora](https://github.com/Agora-Connect/Agora) | Production Flask application — the entire system |
| [docs](https://github.com/Agora-Connect/docs) | **This repo** — design documents and domain specification |
| [backend](https://github.com/Agora-Connect/backend) | Early Flask prototype (reference only, not production) |
| [database](https://github.com/Agora-Connect/database) | Early SQLite prototype (reference only, not production) |

---

## License

MIT License
