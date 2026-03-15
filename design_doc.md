# Agora — Design Document

**Course:** Database Systems (CSCI 411)
**Team:** Agora-Connect
**Date:** March 2026

---

## 1. ER Diagram

The complete ER diagram is available in this repository as `er_diagram.png` (visual) and `er_diagram.dbml` (source).

The diagram includes all 18 tables with primary keys, foreign keys, cardinality notation, and self-referential relationships.

---

## 2. Entities, Relationships, and Cardinalities

### Core Entities

| Entity | Description |
|--------|-------------|
| User | Verified SCSU student. Uniquely identified by university email. |
| Course | A university course (e.g., CSCI 411). All content is scoped to a course. |
| Enrollment | Junction between User and Course. **Weak entity** — no meaning without both. |
| Tag | Reusable content label (e.g., #databases, #midterm). |
| Post | General educational post authored by a student. |
| Problem | Academic question posted in the Q&A forum. |
| Answer | Student response to a Problem. |
| Resource | Shared academic material (textbook, notes, study guide, past exam). |
| BorrowRequest | Borrow/lend lifecycle for a Resource. **Weak entity** — deleted if the Resource is deleted. |

### Interaction Entities

| Entity | Description |
|--------|-------------|
| CommentOnPost | Comment on a Post. |
| CommentOnProblem | Comment on a Problem. |
| CommentOnAnswer | Comment on an Answer. |
| UpvoteOnPost | One upvote per user per Post. |
| UpvoteOnProblem | One upvote per user per Problem. |
| UpvoteOnAnswer | One upvote per user per Answer. |
| Follow | Self-referential social graph on User. |
| PostTag | Many-to-many junction between Post and Tag. |
| ProblemTag | Many-to-many junction between Problem and Tag. |

### Key Relationships

| Relationship | Type | Constraint |
|---|---|---|
| User enrolls in Course | M:N via Enrollment | One enrollment per (user, course) |
| User authors Post | 1:N | Each post has exactly one author |
| User authors Problem | 1:N | Each problem has exactly one author |
| Problem has Answers | 1:N | One user → one answer per problem |
| Problem has one accepted Answer | 1:1 (partial) | Enforced by trigger |
| User follows User | M:N self-referential | Cannot follow yourself |
| Post has Tags | M:N via PostTag | Composite PK |
| Resource has BorrowRequests | 1:N | BorrowRequest deleted with Resource |

### Weak Entities

- **Enrollment** — Identifying relationship with both User and Course. Existence depends on both parent entities. Deleting a user or course cascades to remove enrollments.
- **BorrowRequest** — Existence depends on the Resource it references. Deleting a resource cascades to remove all associated borrow requests.

---

## 3. Normalization

All 18 tables are normalized to **Third Normal Form (3NF)**. The process is demonstrated below for the three most significant tables.

---

### 3.1 User Table

**Attributes:** user_id, email, name, university, reputation_score, created_at

**1NF check:** All attributes are atomic (no multi-valued or composite attributes). ✓

**2NF check:** Primary key is `user_id` (single column). No composite key, so partial dependencies are impossible. ✓

**3NF check:** Check for transitive dependencies:
- `name` depends only on `user_id` ✓
- `email` depends only on `user_id` ✓
- `university` depends only on `user_id` ✓
- `reputation_score` depends only on `user_id` ✓

No non-key attribute determines another non-key attribute. **User is in 3NF.**

---

### 3.2 Enrollment Table (Weak Entity)

**Attributes:** enrollment_id, user_id, course_id, enrolled_at

**1NF check:** All attributes are atomic. ✓

**2NF check:** Primary key is `enrollment_id`. However, the functional dependency `(user_id, course_id) → enrolled_at` holds, and the UNIQUE constraint on `(user_id, course_id)` is effectively a candidate key. `enrolled_at` depends on the full candidate key, not a partial subset. ✓

**3NF check:**
- `enrolled_at` depends only on the enrollment itself, not on any other non-key attribute. ✓

No transitive dependencies. **Enrollment is in 3NF.**

---

### 3.3 Answer Table

**Attributes:** answer_id, problem_id, user_id, content, is_accepted, created_at

**1NF check:** All attributes are atomic. ✓

**2NF check:** Primary key is `answer_id` (single column). No partial dependencies possible. ✓

**3NF check:**
- `content`, `is_accepted`, `created_at` depend only on `answer_id` ✓
- `problem_id` and `user_id` are foreign keys — they do not create transitive dependencies because they are part of the key structure (and form the candidate key `(problem_id, user_id)`) ✓

**Answer is in 3NF.**

---

### 3.4 Why Comments and Upvotes are Separate Tables

An earlier design considered a single polymorphic `Comment` table with a `content_type` column (e.g., `'post'`, `'problem'`, `'answer'`). This was rejected because:

1. A foreign key cannot point to multiple tables — the polymorphic FK would be unenforceable at the database level.
2. Separate tables allow proper `ON DELETE CASCADE` from each parent.
3. Queries on a specific comment type are simpler and index-friendly.

The same reasoning applies to the three Upvote tables.

---

## 4. Final Relational Schema

```
User(user_id PK, email UNIQUE, name, university, reputation_score, created_at)

Course(course_id PK, code UNIQUE, name, description, created_at)

Enrollment(enrollment_id PK, user_id FK→User, course_id FK→Course, enrolled_at)
    UNIQUE(user_id, course_id)

Tag(tag_id PK, name UNIQUE)

Post(post_id PK, user_id FK→User, course_id FK→Course, content, is_anonymous, created_at)

Problem(problem_id PK, user_id FK→User, course_id FK→Course, title, description, created_at)

Answer(answer_id PK, problem_id FK→Problem, user_id FK→User, content, is_accepted, created_at)
    UNIQUE(problem_id, user_id)

CommentOnPost(comment_id PK, post_id FK→Post, user_id FK→User, content, created_at)

CommentOnProblem(comment_id PK, problem_id FK→Problem, user_id FK→User, content, created_at)

CommentOnAnswer(comment_id PK, answer_id FK→Answer, user_id FK→User, content, created_at)

UpvoteOnPost(upvote_id PK, post_id FK→Post, user_id FK→User, created_at)
    UNIQUE(post_id, user_id)

UpvoteOnProblem(upvote_id PK, problem_id FK→Problem, user_id FK→User, created_at)
    UNIQUE(problem_id, user_id)

UpvoteOnAnswer(upvote_id PK, answer_id FK→Answer, user_id FK→User, created_at)
    UNIQUE(answer_id, user_id)

Follow(follow_id PK, follower_id FK→User, followed_id FK→User, created_at)
    UNIQUE(follower_id, followed_id)
    CHECK(follower_id != followed_id)

Resource(resource_id PK, user_id FK→User, course_id FK→Course, title, type, file_path, created_at)
    CHECK(type IN ('textbook','notes','study_guide','past_exam','other'))

BorrowRequest(request_id PK, resource_id FK→Resource, requester_id FK→User,
              owner_id FK→User, status, requested_at, updated_at)
    CHECK(status IN ('pending','approved','returned','declined'))
    CHECK(requester_id != owner_id)

PostTag(post_id FK→Post, tag_id FK→Tag)
    PRIMARY KEY(post_id, tag_id)

ProblemTag(problem_id FK→Problem, tag_id FK→Tag)
    PRIMARY KEY(problem_id, tag_id)
```

---

## 5. Business Rules Summary

| Rule | Enforcement |
|---|---|
| One enrollment per user per course | UNIQUE(user_id, course_id) on Enrollment |
| One answer per user per problem | UNIQUE(problem_id, user_id) on Answer |
| Only one accepted answer per problem | BEFORE UPDATE trigger on Answer |
| One upvote per user per content item | UNIQUE constraint on each Upvote table |
| A user cannot follow themselves | CHECK(follower_id != followed_id) on Follow |
| A user cannot borrow from themselves | CHECK(requester_id != owner_id) on BorrowRequest |
| Resource type must be academic | CHECK constraint on Resource.type |
| Borrow status must be valid | CHECK constraint on BorrowRequest.status |
| Reputation score is non-negative | CHECK(reputation_score >= 0) on User |

---

## 6. Repository Structure

```
database/
    schema.sql       — MySQL CREATE TABLE statements
    seed.sql         — Synthetic data (350+ rows)
    queries.sql      — Required SQL queries and views
    indexes.sql      — Index definitions and EXPLAIN analysis
    transactions.sql — COMMIT, ROLLBACK, SAVEPOINT demos
    mongodb.js       — NoSQL activity log component
    app.py           — Python application with prepared statements

docs/
    DOMAIN_DESCRIPTION.md  — Project proposal (Week 4)
    design_doc.md          — This document (Week 7)
    er_diagram.png         — ER diagram (visual)
    er_diagram.dbml        — ER diagram source (dbdiagram.io)
```
