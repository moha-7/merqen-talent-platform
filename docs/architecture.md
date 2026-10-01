# Architecture

## System Overview

Merqen separates the candidate-facing experience from internal recruitment operations while sharing a common recruitment domain and tenant-aware data model.

```text
Candidates
    |
    v
Careers Portal
    |
    v
Next.js Application
    |
    +-- Candidate Experience
    +-- Recruitment Workspace
    +-- Platform Administration
    |
    v
Authorization + Domain Services
    |
    +-- Jobs
    +-- Applications
    +-- Candidates
    +-- Screening
    +-- Analytics
    +-- Email Outbox
    +-- Audit Events
    |
    v
Prisma
    |
    v
PostgreSQL
```

## Multi-Tenant Model

A tenant represents a customer organization.

Each tenant can contain multiple companies or business units.

Staff access is constrained using memberships, recruitment roles, permission grants, company scopes, and job-level access.

## Core Domain Areas

- Tenant and company hierarchy
- Staff membership and RBAC
- Job lifecycle
- Candidate identity
- Applications and stage transitions
- Screening questions and answers
- Candidate files
- Audit events
- Recruitment analytics
- Email outbox
