# Agora - Domain Description & Preliminary Design

## 1. Domain Overview

Agora is an academic collaboration platform designed exclusively for verified university students within their campus community. The platform provides a structured, university-only environment where students can engage in course-scoped academic discussions, ask and answer questions, share educational resources, and connect with peers for academic support.

Unlike general-purpose social media platforms designed primarily for entertainment, Agora focuses on fostering meaningful academic interaction through features such as course enrollment, question-and-answer forums, reputation systems, resource sharing, and content tagging. The platform enforces university email authentication to ensure all participants are verified members of the institution, creating a trusted academic space.

The domain encompasses the complete lifecycle of academic collaboration: user registration and authentication, course enrollment, content creation and engagement (posts, problems, answers, comments), social interaction (following, upvoting), resource sharing (textbooks, notes), and content organization through tagging systems.

---

## 2. Problem Statement

College students lack a dedicated university platform designed specifically for academic collaboration within their own campus community. While students often rely on general social media platforms, group chats, or external forums, these tools are not built for structured academic interaction and are primarily designed for entertainment rather than learning.

As a result, meaningful academic discussion is fragmented across multiple platforms, making it difficult for students to ask course-specific questions, share resources, and connect with peers in a focused and reliable environment. Additionally, many students hesitate to participate in classroom discussions or public forums due to fear of judgment, lack of anonymity, or the absence of a trusted academic space.

Agora addresses this problem by providing a centralized, university-only platform where verified students can connect, collaborate, and engage in course-scoped academic discussions. The system enables students to ask and answer questions, share notes and educational resources, and interact with peers through structured features such as courses, tagging, upvotes, and accepted answers. Optional anonymity further encourages open participation while maintaining a secure, institution-specific environment.

---

## 3. Preliminary Entities

### 3.1 Core Entities

**User**
Represents verified university students who register using their institutional email addresses. Each user has a profile containing their name, university affiliation, and a reputation score earned through quality contributions.

Key Attributes: user_id (PK), email (unique), name, university, reputation_score, created_at

**Course**
Represents university courses such as CSCI 411 or MATH 221. Courses provide academic scoping for all content, enabling students to filter posts, problems, and resources by specific classes.

Key Attributes: course_id (PK), code, name, description, created_at

**Enrollment**
Junction entity representing the many-to-many relationship between users and courses. Tracks which students are enrolled in which courses, enforcing that a user can enroll in a given course only once.

Key Attributes: enrollment_id (PK), user_id (FK), course_id (FK), enrolled_at

Business Rule: UNIQUE constraint on (user_id, course_id)

### 3.2 Content Entities

**Post**
Represents general educational posts created by students within a course context. Posts can contain text, images, and file attachments. Users can choose to post anonymously while the system retains internal ownership for moderation purposes.

Key Attributes: post_id (PK), user_id (FK), course_id (FK), content, is_anonymous, created_at

**Problem**
Represents academic questions posted in the Q&A forum. Each problem is scoped to a specific course and can receive multiple answers from other students.

Key Attributes: problem_id (PK), user_id (FK), course_id (FK), title, description, created_at

**Answer**
Represents student responses to problems. Each user may submit only one answer per problem, and the problem owner can mark exactly one answer as accepted, similar to Stack Overflow.

Key Attributes: answer_id (PK), problem_id (FK), user_id (FK), content, is_accepted, created_at

Business Rules:
- UNIQUE constraint on (problem_id, user_id) - one answer per user per problem
- Only one answer per problem can have is_accepted = TRUE

### 3.3 Comment Entities

Comments enable threaded discussion on posts, problems, and answers. To maintain clarity in the relational model, comments are separated into three distinct tables:

**CommentOnPost**
Key Attributes: comment_id (PK), post_id (FK), user_id (FK), content, created_at

**CommentOnProblem**
Key Attributes: comment_id (PK), problem_id (FK), user_id (FK), content, created_at

**CommentOnAnswer**
Key Attributes: comment_id (PK), answer_id (FK), user_id (FK), content, created_at

### 3.4 Upvote Entities

Upvotes track user engagement and contribute to content ranking and reputation scores. Similar to comments, upvotes are modeled as separate tables to ensure referential integrity:

**UpvoteOnPost**
Key Attributes: upvote_id (PK), post_id (FK), user_id (FK), created_at
Business Rule: UNIQUE constraint on (post_id, user_id)

**UpvoteOnProblem**
Key Attributes: upvote_id (PK), problem_id (FK), user_id (FK), created_at
Business Rule: UNIQUE constraint on (problem_id, user_id)

**UpvoteOnAnswer**
Key Attributes: upvote_id (PK), answer_id (FK), user_id (FK), created_at
Business Rule: UNIQUE constraint on (answer_id, user_id)

### 3.5 Interaction Entities

**Follow**
Represents a self-referential relationship on the User entity, allowing students to follow peers for personalized feed generation.

Key Attributes: follow_id (PK), follower_id (FK → User), followed_id (FK → User), created_at

Business Rule: UNIQUE constraint on (follower_id, followed_id)

**Tag**
Reusable labels such as #databases, #midterm, or #homework that can be applied to posts and problems for organization, search, and filtering.

Key Attributes: tag_id (PK), name (unique)

### 3.6 Resource Sharing Entities

**Resource**
Represents shared academic materials such as textbooks, lecture notes, and study guides. Resources are scoped to courses and can be offered for borrowing or lending. The system does not allow sharing of syllabi or assignments.

Key Attributes: resource_id (PK), user_id (FK), course_id (FK), title, type, file_path, created_at

**BorrowRequest**
Tracks borrow/lend workflows between students. Each request includes status tracking (pending, approved, returned) to manage the lifecycle of resource sharing.

Key Attributes: request_id (PK), resource_id (FK), requester_id (FK → User), owner_id (FK → User), status, requested_at, updated_at

### 3.7 Junction Tables

**PostTag**
Many-to-many relationship between Post and Tag.
Key Attributes: post_id (FK), tag_id (FK)
Primary Key: (post_id, tag_id)

**ProblemTag**
Many-to-many relationship between Problem and Tag.
Key Attributes: problem_id (FK), tag_id (FK)
Primary Key: (problem_id, tag_id)

---

## 4. Entity Relationships

### 4.1 User-Course Relationship

Users and courses have a many-to-many relationship implemented through the Enrollment junction table. A student can enroll in multiple courses, and each course contains multiple students. The enrollment relationship enables course-specific feeds, access control (only enrolled students can post in a course), and academic analytics such as identifying the most active courses or top contributors within a course.

### 4.2 Content Ownership

All content entities (Post, Problem, Answer, Resource) have a direct one-to-many relationship with User, establishing clear ownership. Each piece of content is created by exactly one user, while a user can create many posts, problems, answers, and resources. Additionally, all content is scoped to a specific course through a one-to-many relationship with the Course entity, enabling filtering and aggregation by academic context.

### 4.3 Question-Answer Flow

Problems and answers form a one-to-many relationship where each problem can receive multiple answers from different students. Business rules enforce that each user can submit only one answer per problem, and only one answer per problem can be marked as accepted by the problem owner. This structure mirrors real academic Q&A platforms and enables queries such as "problems without accepted answers" or "users with the most accepted answers."

### 4.4 Social Interactions

The Follow entity creates a self-referential many-to-many relationship on User, where users can follow other users. This relationship supports personalized following feeds and academic networking within the university community.

Upvotes create one-to-many relationships between users and content (posts, problems, answers). Each user can upvote a content item only once, and upvotes contribute directly to content ranking and the author's reputation score.

### 4.5 Commenting System

Comments are modeled through three separate one-to-many relationships: Post → CommentOnPost, Problem → CommentOnProblem, and Answer → CommentOnAnswer. Each comment is authored by a user (one-to-many from User) and belongs to exactly one content item, enabling threaded discussions across all content types.

### 4.6 Tagging System

Tags have many-to-many relationships with both posts and problems through the PostTag and ProblemTag junction tables. A single tag can be applied to many posts and problems, while each post or problem can have multiple tags. This structure supports efficient filtering, search functionality, and aggregate analytics such as identifying trending topics or most-used tags.

### 4.7 Resource Sharing Workflow

Resources have a one-to-many relationship with BorrowRequest, where each resource can receive multiple borrow requests over time. Each borrow request connects two users (requester and owner) and tracks the status of the lending transaction, enabling workflows for resource discovery, request management, and return tracking.

---

## 5. Expected Functionality

### 5.1 User Management

- University email-based registration and authentication
- User profiles with optional anonymity for posts and questions
- Role support limited to students (future expansion may include instructors or moderators)

### 5.2 Feed System

- Following Feed: Displays posts from users that the current user follows
- University Feed: Displays campus-wide posts from all enrolled courses
- Posts ordered chronologically with timestamps for recency

### 5.3 Course-Scoped Content

- Students enroll in university courses (e.g., CSCI 411, MATH 221)
- All posts, questions, and resources are linked to specific courses
- Enables filtering and aggregation by course for focused academic discussion

### 5.4 Posts & Engagement

- Educational posts supporting text, images, and file attachments (PDFs, notes)
- Threaded comments for discussions
- Upvoting mechanism for posts and comments to surface quality content
- Bookmarking/saving functionality for future reference

### 5.5 Questions & Answers (Problem Forum)

- Students post academic problems or questions scoped to courses
- Other students submit answers with rich text and formatting support
- Question owners can mark one answer as accepted to indicate the best solution
- Upvotes on answers promote quality responses and contribute to reputation

### 5.6 Tagging & Search

- Posts and questions can be tagged with keywords (e.g., #databases, #midterm, #homework)
- Tags support search, filtering, and trend analysis
- Enables discovery of relevant content across courses and time periods

### 5.7 Reputation System

- Users earn reputation points for receiving upvotes on their content
- Additional points awarded for accepted answers to questions
- Reputation score displayed on user profiles to recognize quality contributors
- Encourages high-quality participation and peer recognition

### 5.8 Resource Sharing & Lending

- Students can share notes, textbooks, and study guides scoped to courses
- Borrow/lend request system with status tracking (pending, approved, returned)
- Explicitly prohibits sharing of syllabi or assignments to maintain academic integrity
- Facilitates peer-to-peer resource exchange within the university community

### 5.9 Moderation & Reporting

- Users can report inappropriate or off-topic content
- Content status tracking (pending review, reviewed, resolved)
- Maintains a safe and academically focused environment

---

## 6. Technology Stack

**Frontend**: Flask (server-rendered views, UI routing, template rendering)

**Backend API**: FastAPI (RESTful services, business logic, authentication)

**Relational Database**: MySQL (core transactional data, normalized schema)

**NoSQL Database**: MongoDB (activity logs, user events, analytics)

**Authentication**: JWT-based token authentication with university email verification

**Repository Structure**:
- frontend - Flask UI and templates
- backend - FastAPI services and REST APIs
- database - SQL schemas, sample data, queries, indexing scripts
- docs - Design documents, ER diagrams, reports

---

## 7. Database Design Approach

### 7.1 Normalization

All relational tables are normalized to Third Normal Form (3NF) or Boyce-Codd Normal Form (BCNF) to ensure:
- No partial dependencies (all non-key attributes depend on the entire primary key)
- No transitive dependencies (non-key attributes depend only on the primary key)
- Minimal data redundancy
- High data integrity and consistency

### 7.2 Business Rule Enforcement

The database enforces key business rules through:
- Primary and foreign key constraints for referential integrity
- UNIQUE constraints to prevent duplicate enrollments, answers, and upvotes
- CHECK constraints for valid data ranges and status values
- Transaction logic for complex operations (posting with reputation updates, borrow workflows)

Critical business rules:
- One enrollment per user per course
- One answer per user per problem
- One accepted answer per problem
- One upvote per user per content item
- Only enrolled users may post within a course
- Anonymous posts retain internal user ownership for moderation

### 7.3 Query & Transaction Support

The database design supports:
- Complex JOIN queries for personalized feeds and course filtering
- Aggregate queries for reputation calculation, popular tags, and active courses
- Subqueries for identifying problems without accepted answers and user statistics
- Indexing on frequently queried attributes (user_id, course_id, timestamps, foreign keys)
- Transaction management for atomic operations (content posting with upvotes, borrow request approval with status updates)

### 7.4 NoSQL Component

MongoDB is used for flexible activity logging and analytics:
- User activity logs (page views, interactions, navigation patterns)
- Feed interaction events for future recommendation systems
- Search activity tracking for trending topics analysis
- Event-driven data that doesn't require strict relational constraints

The NoSQL component complements the relational database without duplicating core transactional data, demonstrating hybrid database architecture while maintaining clear separation of concerns.

---

## 8. Appendix: ER Diagram

A complete Entity-Relationship diagram visualizing all entities, attributes, relationships, and constraints is available in this repository:

**File**: `er_diagram.png`

The diagram includes:
- 18 relational tables (User, Course, Enrollment, Post, Problem, Answer, CommentOnPost, CommentOnProblem, CommentOnAnswer, UpvoteOnPost, UpvoteOnProblem, UpvoteOnAnswer, Follow, Tag, PostTag, ProblemTag, Resource, BorrowRequest)
- Primary keys and foreign keys for all tables
- Cardinality notation for relationships (1:N, M:N)
- Self-referential relationship on User (Follow)
- Junction tables for many-to-many relationships
- Unique constraints and indexes

The DBML source code for the diagram is available in `er_diagram.dbml` and can be imported into dbdiagram.io for interactive exploration and modification.

---

**Document Version**: 1.0
**Last Updated**: 2026-02-10
**Project**: Agora - Academic Collaboration Platform
**Organization**: Agora-Connect (https://github.com/Agora-Connect)
