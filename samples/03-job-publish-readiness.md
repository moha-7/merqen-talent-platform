# Job Publish Readiness

Before a role can be published, Merqen evaluates domain-level readiness rather than relying only on form-required attributes.

The public excerpt below shows representative blocker rules. Additional readiness checks remain in the private application core.

```ts
export type JobPublishReadiness = {
  ready: boolean
  blockers: string[]
  warnings: string[]
}

export function getJobPublishReadiness(
  job: JobPublishReadinessInput
): JobPublishReadiness {
  const blockers: string[] = []
  const warnings: string[] = []

  if (!job.title?.trim()) {
    blockers.push("Job title")
  }

  if (!job.department?.trim()) {
    blockers.push("Department")
  }

  if (!job.location?.trim()) {
    blockers.push("Location")
  }

  if (
    !job.description ||
    job.description.trim().length < 80
  ) {
    blockers.push(
      "Role summary of at least 80 characters"
    )
  }

  if (stringList(job.responsibilities).length === 0) {
    blockers.push("At least one responsibility")
  }

  if (stringList(job.requirements).length === 0) {
    blockers.push("At least one requirement")
  }

  if (
    typeof job.openings !== "number" ||
    !Number.isInteger(job.openings) ||
    job.openings < 1
  ) {
    blockers.push("Number of openings")
  }

  // Additional readiness checks omitted from the public showcase.
}
```

## Design takeaway

Publish readiness is treated as product logic, not merely form validation.

This makes the same rules usable from UI actions, server operations, and future automation.
