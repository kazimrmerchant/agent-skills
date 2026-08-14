---
name: shipping-and-launch
description: Prepares production launches safely. Use when deploying to production, running a pre-launch checklist, setting up monitoring, planning a staged rollout, or defining a rollback strategy. Triggers: deploy, release, launch, rollout, ship, canary, feature flag, rollback.
version: 1.0.1
---

# Shipping and Launch

Ship with confidence. The goal is not just to deploy — it's to deploy safely, with monitoring in place, a rollback plan ready, and a clear understanding of what success looks like. Every launch should be **reversible**, **observable**, and **incremental**.

## When to Use

- Deploying a feature to production for the first time
- Releasing a significant change to users
- Migrating data or infrastructure
- Opening a beta or early access program
- Any deployment that carries risk (all of them)

## Prerequisites

- A working CI/CD pipeline that runs tests, lint, and type checks on every commit
- A staging environment that mirrors production as closely as possible
- An error reporting service configured (e.g., Sentry, Datadog, or equivalent)
- A feature flag system available (in-app, LaunchDarkly, or equivalent)
- Database migration tooling with rollback support (e.g., Prisma Migrate)
- Access to production logs, metrics dashboards, and health check endpoints

## Procedure

### 1. Complete the Pre-Launch Checklist

Work through every section below. Do not skip sections because they "look fine." If any item is unchecked, resolve it before proceeding.

#### Code Quality

- [ ] All tests pass (unit, integration, e2e)
- [ ] Build succeeds with no warnings
- [ ] Lint and type checking pass
- [ ] Code reviewed and approved
- [ ] No TODO comments that should be resolved before launch
- [ ] No `console.log` debugging statements in production code
- [ ] Error handling covers expected failure modes

#### Security

- [ ] No secrets in code or version control (search for API keys, tokens, passwords)
- [ ] `npm audit` shows no critical or high vulnerabilities
- [ ] Input validation on all user-facing endpoints
- [ ] Authentication and authorization checks in place
- [ ] Security headers configured (CSP, HSTS, etc.)
- [ ] Rate limiting on authentication endpoints
- [ ] CORS configured to specific origins (not wildcard)

> **Load `references/security-checklist.md`** when any security checklist item is unclear or needs detailed verification steps. Do not guess — read the reference.

#### Performance

- [ ] Core Web Vitals within "Good" thresholds (LCP < 2.5s, INP < 200ms, CLS < 0.1)
- [ ] No N+1 queries in critical paths
- [ ] Images optimized (compression, responsive sizes, lazy loading)
- [ ] Bundle size within budget
- [ ] Database queries have appropriate indexes
- [ ] Caching configured for static assets and repeated queries

> **Load `references/performance-checklist.md`** when performance verification requires detailed benchmarking or profiling steps.

#### Accessibility

- [ ] Keyboard navigation works for all interactive elements
- [ ] Screen reader can convey page content and structure
- [ ] Color contrast meets WCAG 2.1 AA (4.5:1 for text)
- [ ] Focus management correct for modals and dynamic content
- [ ] Error messages are descriptive and associated with form fields
- [ ] No accessibility warnings in axe-core or Lighthouse

> **Load `references/accessibility-checklist.md`** when you need step-by-step accessibility audit instructions or tool configuration.

#### Infrastructure

- [ ] Environment variables set in production (never committed to version control)
- [ ] Database migrations applied (or ready to apply)
- [ ] DNS and SSL configured
- [ ] CDN configured for static assets
- [ ] Logging and error reporting configured
- [ ] Health check endpoint exists and responds

#### Documentation

- [ ] README updated with any new setup requirements
- [ ] API documentation current
- [ ] ADRs written for any architectural decisions
- [ ] Changelog updated
- [ ] User-facing documentation updated (if applicable)

### 2. Ship Behind a Feature Flag

Decouple deployment from release. Deploy the code with the flag OFF, then enable incrementally.

```typescript
// Feature flag check
const flags = await getFeatureFlags(userId);

if (flags.taskSharing) {
  // New feature: task sharing
  return <TaskSharingPanel task={task} />;
}

// Default: existing behavior
return null;
```

**Feature flag lifecycle:**

```
1. DEPLOY with flag OFF     → Code is in production but inactive
2. ENABLE for team/beta     → Internal testing in production environment
3. GRADUAL ROLLOUT          → 5% → 25% → 50% → 100% of users
4. MONITOR at each stage    → Watch error rates, performance, user feedback
5. CLEAN UP                 → Remove flag and dead code path after full rollout
```

**Feature flag rules (hard):**

- Every feature flag MUST have an owner and an expiration date
- Clean up flags within 2 weeks of full rollout
- Do NOT nest feature flags (creates exponential combinations)
- Test both flag states (on and off) in CI

### 3. Execute the Staged Rollout

Follow this sequence exactly. Do not skip stages.

```
1. DEPLOY to staging
   └── Full test suite in staging environment
   └── Manual smoke test of critical flows

2. DEPLOY to production (feature flag OFF)
   └── Verify deployment succeeded (health check returns 200)
   └── Check error monitoring (no new errors)

3. ENABLE for team (flag ON for internal users)
   └── Team uses the feature in production
   └── 24-hour monitoring window

4. CANARY rollout (flag ON for 5% of users)
   └── Monitor error rates, latency, user behavior
   └── Compare metrics: canary vs. baseline
   └── 24-48 hour monitoring window
   └── Advance only if all thresholds pass (see table below)

5. GRADUAL increase (25% → 50% → 100%)
   └── Same monitoring at each step
   └── Ability to roll back to previous percentage at any point

6. FULL rollout (flag ON for all users)
   └── Monitor for 1 week
   └── Clean up feature flag
```

#### Rollout Decision Thresholds

Use these thresholds to decide whether to advance, hold, or roll back at each stage:

| Metric | Advance (green) | Hold and investigate (yellow) | Roll back (red) |
|--------|-----------------|-------------------------------|-----------------|
| Error rate | Within 10% of baseline | 10–100% above baseline | >2x baseline |
| P95 latency | Within 20% of baseline | 20–50% above baseline | >50% above baseline |
| Client JS errors | No new error types | New errors at <0.1% of sessions | New errors at >0.1% of sessions |
| Business metrics | Neutral or positive | Decline <5% (may be noise) | Decline >5% |

### 4. Set Up Monitoring and Observability

Ensure these metrics are being collected and visible on a dashboard before you deploy.

```
Application metrics:
├── Error rate (total and by endpoint)
├── Response time (p50, p95, p99)
├── Request volume
├── Active users
└── Key business metrics (conversion, engagement)

Infrastructure metrics:
├── CPU and memory utilization
├── Database connection pool usage
├── Disk space
├── Network latency
└── Queue depth (if applicable)

Client metrics:
├── Core Web Vitals (LCP, INP, CLS)
├── JavaScript errors
├── API error rates from client perspective
└── Page load time
```

#### Error Reporting Setup

```typescript
// Client-side: error boundary with reporting
class ErrorBoundary extends React.Component {
  componentDidCatch(error: Error, info: React.ErrorInfo) {
    reportError(error, {
      componentStack: info.componentStack,
      userId: getCurrentUser()?.id,
      page: window.location.pathname,
    });
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback onRetry={() => this.setState({ hasError: false })} />;
    }
    return this.props.children;
  }
}

// Server-side: error middleware
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  reportError(err, {
    method: req.method,
    url: req.url,
    userId: req.user?.id,
  });

  // Never expose internals to users
  res.status(500).json({
    error: { code: 'INTERNAL_ERROR', message: 'Something went wrong' },
  });
});
```

### 5. Document the Rollback Plan

Every deployment needs a rollback plan **before** it happens. Use this template:

```markdown
## Rollback Plan for [Feature/Release]

### Trigger Conditions
- Error rate > 2x baseline
- P95 latency > [X]ms
- User reports of [specific issue]

### Rollback Steps
1. Disable feature flag (if applicable)
   OR
1. Deploy previous version: `git revert <commit> && git push`
2. Verify rollback: health check, error monitoring
3. Communicate: notify team of rollback

### Database Considerations
- Migration [X] has a rollback: `npx prisma migrate rollback`
- Data inserted by new feature: [preserved / cleaned up]

### Time to Rollback
- Feature flag: < 1 minute
- Redeploy previous version: < 5 minutes
- Database rollback: < 15 minutes
```

#### When to Roll Back Immediately

Roll back immediately if **any** of the following are true:

- Error rate increases by more than 2x baseline
- P95 latency increases by more than 50%
- User-reported issues spike
- Data integrity issues detected
- Security vulnerability discovered

### 6. Post-Launch Verification

In the first hour after launch, perform these checks in order:

1. Check health endpoint returns 200
2. Check error monitoring dashboard (no new error types)
3. Check latency dashboard (no regression)
4. Test the critical user flow manually
5. Verify logs are flowing and readable
6. Confirm rollback mechanism works (dry run if possible)

## Pitfalls

### Common Rationalizations to Reject

| Rationalization | Reality |
|---|---|
| "It works in staging, it'll work in production" | Production has different data, traffic patterns, and edge cases. Monitor after deploy. |
| "We don't need feature flags for this" | Every feature benefits from a kill switch. Even "simple" changes can break things. |
| "Monitoring is overhead" | Not having monitoring means you discover problems from user complaints instead of dashboards. |
| "We'll add monitoring later" | Add it before launch. You can't debug what you can't see. |
| "Rolling back is admitting failure" | Rolling back is responsible engineering. Shipping a broken feature is the failure. |

### Red Flags — Stop if You See These

- Deploying without a rollback plan
- No monitoring or error reporting in production
- Big-bang releases (everything at once, no staging)
- Feature flags with no expiration or owner
- No one monitoring the deploy for the first hour
- Production environment configuration done by memory, not code
- "It's Friday afternoon, let's ship it"

### Windows-Specific Notes

- On Windows hosts (PowerShell), use `git revert <commit>; git push` (semicolon separator) instead of `&&` if your shell does not support `&&` natively. PowerShell 7+ supports `&&`.
- Environment variables in PowerShell: check with `Get-ChildItem Env:` or `$env:YOUR_KEY` — never hardcode secrets in scripts.
- When running `npm audit` on Windows, ensure Node.js and npm are on the system PATH: `node --version && npm --version`.

## Verification

### Before Deploying

- [ ] Pre-launch checklist completed (all sections green)
- [ ] Feature flag configured (if applicable)
- [ ] Rollback plan documented
- [ ] Monitoring dashboards set up
- [ ] Team notified of deployment

### After Deploying

- [ ] Health check returns 200
- [ ] Error rate is normal
- [ ] Latency is normal
- [ ] Critical user flow works
- [ ] Logs are flowing
- [ ] Rollback tested or verified ready

### Checkable Commands

```powershell
# Verify no secrets are staged in git
git diff --cached --name-only | Select-String -Pattern "\.env|secret|credential"

# Run audit for vulnerabilities
npm audit --audit-level=high

# Verify build succeeds with no warnings
npm run build

# Run full test suite
npm test

# Check health endpoint (replace URL with your production endpoint)
Invoke-RestMethod -Uri "https://YOUR_DOMAIN/health" -Method Get | Select-Object -ExpandProperty StatusCode
```

Expected health check output: HTTP 200 with a JSON body containing status information.

## See Also

- **`references/security-checklist.md`** — Load when verifying security checklist items in detail (CSP headers, CORS, rate limiting, secret scanning).
- **`references/performance-checklist.md`** — Load when performance verification requires benchmarking, profiling, or Core Web Vitals measurement steps.
- **`references/accessibility-checklist.md`** — Load when you need step-by-step accessibility audit instructions or axe-core/Lighthouse configuration.
