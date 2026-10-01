# Access Policy

This excerpt demonstrates how Merqen separates tenant membership from company- and job-level scope.

The full authorization layer also performs session resolution, database lookups, permission-grant checks, and route enforcement. Those implementation details are intentionally not included in the public showcase.

```ts
export function hasCompanyAccess(
  principal: AccessPrincipal,
  tenantId: string,
  companyId: string
): boolean {
  const membership = getActiveMembership(principal, tenantId)

  if (!membership) return false

  if (membership.scope === MembershipScope.ALL_COMPANIES) {
    return true
  }

  if (membership.scope === MembershipScope.SELECTED_COMPANIES) {
    return membership.companyAccesses.some(
      (access) => access.companyId === companyId
    )
  }

  return false
}

export function hasJobAccess(
  principal: AccessPrincipal,
  target: JobAccessTarget
): boolean {
  const membership = getActiveMembership(
    principal,
    target.tenantId
  )

  if (!membership) return false

  if (membership.scope === MembershipScope.ALL_COMPANIES) {
    return true
  }

  if (membership.scope === MembershipScope.SELECTED_COMPANIES) {
    return membership.companyAccesses.some(
      (access) => access.companyId === target.companyId
    )
  }

  if (membership.scope === MembershipScope.ASSIGNED_JOBS) {
    return membership.jobAccesses.some(
      (access) => access.jobId === target.jobId
    )
  }

  return false
}
```

## Design takeaway

Authorization is modeled as explicit policy evaluation rather than UI visibility alone.

This keeps company and job access rules reusable across server-side operations.
