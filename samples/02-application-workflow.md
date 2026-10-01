# Application Workflow State Rules

Merqen treats recruitment stages as a controlled workflow rather than allowing arbitrary status updates.

```ts
export function canTransitionApplicationStage(
  fromStage: ApplicationStage,
  toStage: ApplicationStage,
  resumeFromStage: ApplicationStage | null = null
) {
  if (fromStage === toStage) {
    return true
  }

  if (terminalStages.has(fromStage)) {
    return false
  }

  // This state is assigned by a hiring invariant,
  // not selected manually by a recruiter.
  if (toStage === ApplicationStage.CLOSED_HIRED_ELSEWHERE) {
    return false
  }

  if (sideExitStages.has(toStage)) {
    return true
  }

  if (toStage === ApplicationStage.ON_HOLD) {
    return fromStage !== ApplicationStage.ON_HOLD
  }

  if (fromStage === ApplicationStage.ON_HOLD) {
    if (!resumeFromStage) {
      return false
    }

    const resumeRank = forwardStageRank[resumeFromStage]
    const targetRank = forwardStageRank[toStage]

    if (resumeRank === undefined || targetRank === undefined) {
      return false
    }

    return targetRank >= resumeRank
  }

  const fromRank = forwardStageRank[fromStage]
  const toRank = forwardStageRank[toStage]

  if (fromRank === undefined || toRank === undefined) {
    return false
  }

  return toRank > fromRank
}
```

## Design takeaway

The workflow protects business invariants at the domain layer.

Recruiters can move applications forward, place them on hold, or use supported exit states without bypassing lifecycle rules.
