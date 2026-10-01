# Email Outbox Pattern

Merqen separates recruitment workflow state from candidate communication.

Only selected application milestones trigger candidate-facing email events.

```ts
const candidateEmailMilestoneStages =
  new Set<ApplicationStageType>([
    ApplicationStage.APPLIED,
    ApplicationStage.HR_REVIEW,
    ApplicationStage.INTERVIEW,
    ApplicationStage.HIRED,
  ])

export function isApplicationEmailMilestone(
  stage: ApplicationStageType
) {
  return candidateEmailMilestoneStages.has(stage)
}

export async function queueApplicationStageEmail(
  tx: Prisma.TransactionClient,
  input: Input
) {
  if (!isApplicationEmailMilestone(input.stage)) {
    return null
  }

  const templateKey = `APPLICATION_${input.stage}`

  const existing = await tx.emailOutbox.findFirst({
    where: {
      applicationId: input.applicationId,
      templateKey,
    },
    orderBy: {
      createdAt: "asc",
    },
  })

  if (existing) {
    return existing
  }

  return tx.emailOutbox.create({
    data: {
      tenantId: input.tenantId,
      candidateId: input.candidateId,
      applicationId: input.applicationId,
      toEmail: input.toEmail,
      templateKey,
      subject:
        `${candidateStageLabels[input.stage]}: ${input.jobTitle}`,
      payload: {
        candidateName: input.candidateName,
        jobTitle: input.jobTitle,
        companyName: input.companyName,
        stage: input.stage,
        publicStatus: candidateStageLabels[input.stage],
        statusDescription:
          candidateStageDescription(input.stage),
      },
    },
  })
}
```

## Design takeaway

The outbox is idempotent at the application + milestone level.

For example, moving `INTERVIEW → ON_HOLD → INTERVIEW` does not create a duplicate interview email.
