# Engineering Decisions

## Separate Candidate and Staff Authentication Contexts

Candidate and internal recruitment experiences have different authorization needs and are modeled separately.

## Explicit Recruitment Permissions

Access is not based only on broad roles. Granular permissions are checked for operations such as viewing candidates, changing application stages, managing jobs, and viewing analytics.

## Validated Workflow Transitions

Application stages and job status changes are handled through domain rules rather than unrestricted database updates.

## Candidate Reuse

A candidate is modeled independently from an application so one person can participate in multiple recruitment processes without duplicate profile records.

## Snapshot Screening Answers

Question prompts and types are stored with application answers so historical applications remain understandable even if the job screening configuration changes later.

## Event-Oriented History

Important recruitment actions create structured events instead of relying only on mutable current-state fields.

## Email Outbox

Email generation and delivery are separated through an outbox model, allowing delivery state and attempts to be tracked independently.

## Synthetic Portfolio Dataset

The public demonstration uses fictional companies, candidates, roles, emails, and recruitment histories.
