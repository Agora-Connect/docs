# Agora

## 1. Project Overview

**Agora** is an academic collaboration platform designed to help college students connect, ask questions, and share educational content within their campus community. Inspired by the concept of the *Agora* as a central hub of knowledge exchange, the platform provides a focused and trusted academic environment different from general purpose social media.

Students authenticate using their official university email addresses, ensuring that all users are verified members of the institution. The platform emphasizes peer to peer support, course based interaction and structured knowledge sharing.

---

## 2. Problem Statement

College students lack a dedicated university platform designed specifically for academic collaboration within their own campus community. While students often rely on general social media platforms, group chats, or external forums, these tools are not built for structured academic interaction and are primarily designed for entertainment rather than learning.

As a result, meaningful academic discussion is fragmented across multiple platforms, making it difficult for students to ask course specific questions, share resources, and connect with peers in a focused and reliable environment. Additionally, many students hesitate to participate in classroom discussions or public forums due to fear of judgment, lack of anonymity, or the absence of a trusted academic space.

Agora addresses this problem by providing a centralized, university only platform where verified students can connect, collaborate, and engage in course scoped academic discussions. The system enables students to ask and answer questions, share notes and educational resources, and interact with peers through structured features such as courses, tagging, upvotes, and accepted answers. Optional anonymity further encourages open participation while maintaining a secure, institution specific environment.

---

## 3. Core Features & Functionality

### 3.1 User Management

* University email based registration and authentication
* User profiles with optional anonymity for posts and questions
* Role support (students)

### 3.2 Feed System

* **Following Feed**: Displays posts from followed users
* **University Feed**: Displays campus wide posts
* Posts are timestamped and ordered chronologically

### 3.3 Course Scoped Content

* Students enroll in courses (e.g., CSCI 411, MATH 221)
* Posts, questions, and resources are linked to specific courses
* Enables filtering and aggregation by course

### 3.4 Posts & Engagement

* Educational posts with text, images, and file attachments (PDFs, notes)
* Comments and threaded discussions
* Upvotes for posts and comments
* Bookmarking/saving content

### 3.5 Questions & Answers (Problem Forum)

* Students post academic problems/questions
* Other students submit answers
* Question owners can mark one answer as *accepted*
* Upvotes on answers to promote quality responses

### 3.6 Tagging System

* Posts and questions can be tagged (e.g., #databases, #midterm)
* Supports search, filtering, and trend analysis

### 3.7 Reputation System

* Users earn points for:

  * Receiving upvotes
  * Having accepted answers
* Encourages high-quality participation

### 3.8 Resource Sharing & Lending

* Students can share notes or textbooks
* Borrow/lend requests with status tracking
* No syllabus or assignment sharing allowed

### 3.9 Moderation & Reporting

* Users can report inappropriate content
* Content status tracking (pending, reviewed, resolved)

---

## 4. System Architecture

### 4.1 Technology Stack

* **Frontend**: Flask (server-rendered views, UI routing)
* **Backend API**: FastAPI (RESTful services, business logic)
* **Relational Database**: MySQL (core transactional data)
* **NoSQL Database**: MongoDB (logs, activity feeds, or events)
* **Authentication**: JWT-based authentication

### 4.2 Repository Structure (GitHub Organization)

* `frontend` – Flask UI and templates
* `backend` – FastAPI services and APIs
* `database` – SQL schemas, sample data, queries, indexing scripts
* `docs` – Design documents, ER diagrams, reports

Repositories communicate via REST APIs, enabling modular development while functioning as a single system.

---

## 5. Database Design Overview
### 5.1 Design Goals

The database for Agora is designed to support academic collaboration platform that enables structured, course specific interaction among students. The primary goals of the database design are:

* To ensure data integrity and consistency across academic content and user interactions

* To support scalable and efficient querying for feeds, course discussions, and Q&A

* To model realistic academic relationships such as course enrollment, question–answer flows, and peer interaction

* To enforce clear business rules (e.g., one accepted answer per problem, one enrollment per course per student)

* To maintain a normalized relational structure (3NF/BCNF) while allowing flexibility through a small NoSQL component

* The database is implemented primarily using a relational model (MySQL), complemented by a MongoDB collection for flexible activity logging and analytics.

### 5.2 Core Entities and Their Responsibilities

The database consists of several core entities that represent users, academic structure, content, and interactions.

#### User

* The User entity represents a verified university student.

* Each user registers using a university-issued email address

* Stores profile level information (name, university, reputation score)

#### Users can:

* Enroll in courses

* Create posts and problems

* Submit answers and comments

* Follow other users

* Upvote content

* Borrow or lend notes/books

* Each user is uniquely identified by a user_id primary key.

#### Course

* The Course entity models university courses (e.g., CSCI 411, MATH 221).

* Courses provide academic scoping for content

* All posts, problems, and shared resources are associated with a course

* Supports filtering feeds and questions by course

#### This entity enables realistic academic queries such as:

* “Most active courses”

* “Questions posted per course”

* “Top contributors in a course”

#### Enrollment

* Enrollment is an associative (junction) entity between User and Course.

* Represents which users are enrolled in which courses

#### Enforces the rule that:

* A user can enroll in many courses

* A course can have many users

* A user can enroll in a given course only once

#### This table is critical for:

* Course specific feeds

* Access control (only enrolled students can post in a course)

* Academic analytics

### 5.3 Content Modeling

Agora supports multiple types of academic content. These are modeled with clear ownership and constraints.

#### Post

The Post entity represents general educational posts (announcements, explanations, shared resources).

#### Each post:

* Is created by a single user

* Belongs to exactly one course

* Can be anonymous or public

* Supports comments, upvotes, and tagging

#### Posts appear in:

* Course feeds

* User following feeds

* Global university feed

#### Problem

The Problem entity represents academic questions.

Problems are:

* Created by users

* Scoped to a specific course

* Designed to receive multiple answers

* Each problem can have exactly one accepted answer

* This structure mirrors real academic Q&A platforms and enables strong business rules.

The Answer entity represents responses to problems.

Each answer:

* Belongs to exactly one problem

* Is authored by one user

A constraint ensures:

* Only one answer per problem can be marked as accepted = TRUE

This allows queries such as:

* “Problems without accepted answers”

* “Users with most accepted answers”

The Comment entity supports discussion on posts, problems, and answers.

A comment:

* Belongs to one content item

* Is authored by one user

* Designed using a polymorphic relationship (via content ID + content type)

### 5.4 Interaction & Social Relationships

The Follow entity models user to user relationships.

Allows students to follow peers

Supports:

* Personalized feeds

* Academic networking

* This is a self referential relationship on the User entity.

Upvote

The Upvote entity tracks user engagement.

Users can upvote:

* Posts

* Problems

* Answers

Enforces:

* One upvote per user per content item

* Upvotes directly contribute to:

* Content ranking

* User reputation score

### 5.5 Resource Sharing (Books & Notes)

The Resource entity represents shared academic materials such as:

* Textbooks

* Lecture notes

* Study guides

Resources:

* Are linked to a course

* Can be offered for borrowing or lending

* Are not assignments or syllabi (enforced by design rules)


The BorrowRequest entity tracks borrowing interactions.

A request includes:

* Requesting user

* Owning user

* Resource

* Status (pending, approved, returned)

This entity enables transaction based workflows and realistic academic sharing.

### 5.6 Tagging and Classification

* The Tag entity supports content organization.

* Tags (e.g., #midterm, #homework) are reusable

* Implemented as a many-to-many relationship with posts and problems

This enables:

* Efficient filtering

* Aggregate analytics (most-used tags)

* Advanced JOIN queries

### 5.7 Business Rules and Constraints

The database enforces several key business rules:

* A user may submit only one answer per problem

* A problem may have only one accepted answer

* A user may enroll in a course only once

* A user may upvote a content item only once

* Only enrolled users may post within a course

* Anonymous posts still retain internal user ownership

These rules are enforced through:

* Primary and foreign keys

* UNIQUE constraints

* CHECK constraints

* Transaction logic

### 5.8 NoSQL Component

A MongoDB collection is used to store user activity logs, such as:

* Page views

* Feed interactions

* Search activity

This component:

* Demonstrates schema flexibility

* Supports future recommendation features

* Complements the relational database without duplicating core data

### 5.9 Normalization Summary

All relational tables are normalized to Third Normal Form (3NF) or BCNF, ensuring:

* No partial dependencies

* No transitive dependencies

* Minimal redundancy

* High data integrity
---

## 6. Query & Transaction Support

The system supports:

* Complex JOIN queries for feeds and course filtering
* Aggregate queries for reputation, popular tags, and active courses
* Subqueries for accepted answers and user statistics
* Indexing on frequently queried attributes (user_id, course_id, timestamps)
* Transaction management for:

  * Posting content
  * Borrow/lend workflows
  * Reputation updates

---

## 7. NoSQL Component

MongoDB is used for:

* Activity logs
* Feed caching or event tracking

JSON documents mirror selected relational entities for analytical queries.

---

## 8. Conclusion

Agora delivers a focused academic social platform that prioritizes structured learning, peer collaboration, and database driven design. The project effectively demonstrates advanced database concepts while solving a real problem faced by university students.

