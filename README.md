# Secure Role-Based Academic Management Portal

<p align="center">
  <strong>A security-focused academic portal for centralized student services, administrative monitoring, and reliable communication.</strong>
</p>

<p align="center">
  <a href="https://academicportal2.vercel.app/">Live Demo</a>
</p>

---

## Overview

The **Secure Role-Based Academic Management Portal** is a web-based academic management system designed to bring essential student and administrative workflows into one place. It separates access by user role and combines authentication controls, session validation, academic-resource access, administrative monitoring, and automated email notifications.

The project was presented during **IBM Day** at **Babu Banarasi Das University (BBDU)**.

## Problem Statement

Academic workflows can become difficult to manage when information and activities are scattered across different channels. Common challenges include:

- Managing academic records and resources centrally.
- Restricting sensitive administrative functions to authorized users.
- Monitoring login activity and account status.
- Tracking assignments, deadlines, notes, previous-year questions, and announcements.
- Delivering registration and password-related emails reliably when several users perform actions at the same time.

## Key Features

### 1. Role-Based Access Control

- Separate **Admin** and **Student** experiences.
- Role permissions are enforced by the backend, not just by hiding frontend pages.
- Protected backend routes validate requests before returning protected data.
- A student cannot gain administrative privileges simply by opening an admin page or modifying the frontend.

### 2. Authentication and Account Protection

- Password-based registration and login.
- SHA-256-based password hashing is used in the current implementation.
- Login validation compares the submitted credential hash with the stored value.
- Five consecutive failed login attempts trigger account-lock protection.
- Password-reset emails provide a reset link.
- Reset requests are subject to the project's configured rate limit.

> **Security note:** SHA-256 alone is not the recommended choice for password storage in a production system. For production hardening, use a password-specific hashing algorithm such as Argon2id, bcrypt, or scrypt, with appropriate parameters and salts.

### 3. Session Token Lifecycle

- A session token is generated after successful login.
- Protected backend requests require a valid session token.
- The configured token lifetime is **one hour**.
- The backend validates the token on protected requests and denies requests with missing, invalid, expired, or mismatched tokens.
- On a subsequent login, the stored session token is replaced with a newly generated token, supporting one active token per user.
- When a token expires, protected access is denied and the client should return the user to the login screen.

**Implementation detail to verify:** If a user manually logs out, confirm whether the backend immediately invalidates the token or leaves it valid until expiry. The README should describe the deployed behaviour accurately.

### 4. Admin Dashboard and Monitoring

The admin dashboard provides a consolidated view of account and security activity, including:

- Total, active, locked, and blocked accounts.
- Failed-login tracking.
- Login-growth comparison.
- Password-reset and account activity information.
- Activity-log monitoring.

These indicators help administrators understand account status and investigate unusual activity.

### 5. Student Dashboard

The student dashboard centralizes commonly used academic resources and status information:

- Assignment overview, including pending and expired assignments.
- Notes and previous-year question (PYQ) access.
- Latest announcements and notice documents.
- Upcoming assignment deadlines.
- Subject/resource progress indicators.

The subject-progress panel currently reflects the academic items available to the portal. It should not be described as marks-based academic performance unless marks or completion data are explicitly tracked.

### 6. Automated Email Notifications

The portal supports automated email workflows such as:

- Welcome email after a student's first registration.
- New-registration alert to the administrator.
- Password-reset email.
- Other configured academic or security notifications.

Screenshots of the student welcome email and administrator registration alert were included in the IBM Day presentation.

### 7. Queue-Based Background Processing

Queue processing is used for email jobs and activity-log events to improve reliability when multiple users perform actions concurrently.

- Email jobs are queued for background processing.
- Activity events are queued to reduce conflicts during concurrent requests.
- Processing queued work helps prevent events from overwriting one another and supports more consistent delivery and logging.

A queue improves reliability, but it does not by itself guarantee 100% email delivery. Delivery failures, retries, monitoring, and dead-letter handling should be considered for a production deployment.

## System Architecture

The system can be understood in three logical layers:

1. **Client Layer** — Admin and student web interfaces.
2. **Application Layer** — Authentication, role checks, session validation, business rules, and request handling.
3. **Data and Monitoring Layer** — Persistent academic/account data and security/activity records, with queued processing for supported background tasks.

The backend acts as the trust boundary: frontend visibility is not treated as authorization.

## Technology and Deployment

- **Application type:** Web-based academic portal
- **Deployment:** Vercel live application
- **Live URL:** https://academicportal2.vercel.app/
- **Backend and database:** Used by the application; list the exact hosting provider, framework, database engine, and queue implementation here before publishing this README if you want the repository to serve as a technical handover document.

> Vercel deployment does not automatically mean that the database or all persistent storage is hosted on Vercel. Frontend hosting, backend hosting, database storage, and file storage should be documented separately according to the actual deployment configuration.

## Future Enhancements

The following items are **planned**, not current features:

- **Location-based attendance:** Admin enables an attendance window for 10 minutes and selects the subject and batch. The system generates the date and time. Students can mark attendance only within a 100-metre radius of the administrator's configured location, and attendance details are generated by the backend rather than being editable by students.
- **AI-powered student assistance:** Integrate an AI API to help students understand topics, ask academic questions, and prepare assignment drafts, with appropriate academic-integrity guidance.
- **Teacher role:** Introduce teacher accounts with access limited to assigned subjects and batches. Teachers could share notes, upload assignments, and review relevant student activity without access to unrelated batches or admin-only controls.
- **Operational hardening:** Add structured error handling, audit-log retention policies, queue retries, monitoring, and stronger password hashing before production-scale use.

## Live Demo

**Open the portal:** https://academicportal2.vercel.app/

For a presentation or review, use test accounts and non-sensitive sample data. Do not include real passwords, session tokens, reset links, private student details, or production secrets in screenshots or repository files.

## Project Presentation

Presented by **Ajay Chauhan**  
**Course:** BCA — Data Science & Artificial Intelligence  
**Institution:** Babu Banarasi Das University (BBDU)  
**Event:** IBM Day

## Closing Note

The project focuses on combining academic convenience with backend-enforced access control, session management, administrative visibility, and more reliable notification processing. Its next stage is to validate security behaviour thoroughly and expand the portal with carefully scoped attendance, AI assistance, and teacher access.
