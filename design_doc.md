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

### 5.1 Key Entities

* **User** (user_id, email, name, reputation, role)
* **Course** (course_id, course_code, title)
* **Enrollment** (user_id, course_id)
* **Post** (post_id, user_id, course_id, content, is_anonymous)
* **Problem** (problem_id, user_id, course_id, title, description)
* **Answer** (answer_id, problem_id, user_id, content, is_accepted)
* **Comment** (comment_id, user_id, post_id/answer_id)
* **Tag** (tag_id, name)
* **PostTag** (post_id, tag_id)
* **Follow** (follower_id, following_id)
* **Vote** (user_id, target_id, vote_type)
* **Resource** (resource_id, owner_id, type, status)
* **BorrowRequest** (request_id, resource_id, requester_id, status)

### 5.2 Relationships & Constraints

* One user can enroll in many courses (M:N)
* One problem can have many answers, but only one accepted answer
* Posts belong to either a course or the university feed
* Many-to-many relationships for tags, follows, and bookmarks

The schema is normalized to **Third Normal Form (3NF)** to eliminate redundancy.

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

