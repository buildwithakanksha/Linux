# Bash DevOps Project — Production-Style Automation

# Project: DevOps Application Deployment & Health Automation

## Objective

Build a Bash-based automation tool that performs:

```text
Input validation
      ↓
Dependency checks
      ↓
Git validation/update
      ↓
Application tests
      ↓
Build
      ↓
Docker image build
      ↓
Registry push
      ↓
Kubernetes deployment
      ↓
Rollout verification
      ↓
Application health check
      ↓
Success / rollback
```

## Suggested Repository

```text
bash-devops-project/
├── README.md
├── deploy.sh
├── config/
│   ├── dev.env
│   ├── stage.env
│   └── prod.env
├── scripts/
│   ├── common.sh
│   ├── health-check.sh
│   ├── docker-build.sh
│   └── rollback.sh
├── logs/
└── tests/
    └── test-deploy.sh
```

## Required Technologies

- Linux
- Bash
- Git
- Docker
- AWS CLI
- kubectl
- Kubernetes
- curl
- jq
- ShellCheck

## Required Bash Concepts

Your project must use:

- Variables
- Functions
- `local`
- Arrays
- `case`
- `getopts`
- `[[ ]]`
- Arithmetic where useful
- Command substitution
- Exit codes
- `set -Eeuo pipefail`
- `trap`
- `mktemp`
- Logging
- Dependency checks
- Input validation

## Command

Target interface:

```bash
./deploy.sh -e dev -a myapp
```

Options:

```text
-e  environment: dev|stage|prod
-a  application name
-v  verbose
-h  help
```

## Phase 1 — Dependency Validation

Check:

```bash
command -v git
command -v docker
command -v kubectl
command -v aws
command -v curl
command -v jq
```

Fail with a useful message if a required tool is missing.

## Phase 2 — Environment Validation

Allowed:

```text
dev
stage
prod
```

Reject:

```text
development
production
test123
empty input
```

Example:

```bash
case "$environment" in
    dev|stage|prod) ;;
    *) die "Invalid environment: $environment" ;;
esac
```

## Phase 3 — Git Validation

Check:
- Repository exists
- Working tree status
- Current branch
- Required branch
- Latest commit

For automated environments, prefer deterministic Git operations such as `fetch` and `pull --ff-only` where appropriate.

## Phase 4 — Testing

Run the application's tests.

Example:

```bash
./run-tests.sh
```

If tests fail:

```text
Deployment FAILED
```

Return non-zero.

## Phase 5 — Docker Build

Example:

```bash
docker build -t "$IMAGE:$TAG" .
```

Verify:

```bash
docker image inspect "$IMAGE:$TAG" >/dev/null
```

## Phase 6 — Push

Push to your configured registry.

Do not hardcode credentials.

Use:
- AWS IAM roles
- CI/CD credentials
- Secure secret mechanisms

## Phase 7 — Kubernetes Deployment

Apply manifests:

```bash
kubectl apply -f k8s/
```

Wait:

```bash
kubectl rollout status deployment/"$APP_NAME" -n "$NAMESPACE"
```

## Phase 8 — Health Check

Use:

```bash
curl -fsS --max-time 10 "$HEALTH_URL"
```

The script should fail when the health endpoint is unavailable.

## Phase 9 — Rollback

If the rollout or health check fails:

```bash
kubectl rollout undo deployment/"$APP_NAME" -n "$NAMESPACE"
```

Then verify rollout again.

## Phase 10 — Logging

Every major step should log:

```text
[2026-09-05 10:00:00] Deployment started
[2026-09-05 10:00:02] Dependencies validated
[2026-09-05 10:00:10] Tests passed
[2026-09-05 10:00:20] Docker image built
[2026-09-05 10:00:30] Deployment completed
```

## Cleanup

Use:

```bash
cleanup() {
    # Remove temporary resources
}

trap cleanup EXIT
```

## Locking

Prevent two deployments of the same application from running simultaneously.

Example:

```bash
exec 9>"/tmp/${APP_NAME}.lock"
flock -n 9 || die "Another deployment is running"
```

## Idempotency Requirements

A second execution should not corrupt the environment.

The script should:
- Validate existing state
- Avoid unnecessary creation
- Use deterministic names
- Handle already-existing resources
- Use Kubernetes desired state
- Fail safely

## Failure Scenarios to Test

Test at least:

1. Missing argument
2. Invalid environment
3. Missing Git repository
4. Missing Docker
5. Failed unit tests
6. Docker build failure
7. Registry push failure
8. Kubernetes API unavailable
9. Deployment rollout failure
10. Health endpoint failure
11. Rollback failure
12. Second deployment started while first is running

## Expected Exit Codes

Suggested:

```text
0   Success
1   General failure
2   Invalid usage/input
10  Dependency failure
20  Build/test failure
30  Deployment failure
40  Health-check failure
50  Rollback failure
```

## Interview Explanation

Be able to explain:

### Why `set -Eeuo pipefail`?
To make failures and unset variables visible and reduce silent errors.

### Why `trap`?
To guarantee cleanup and optionally log unexpected failures.

### Why `flock`?
To prevent concurrent deployments.

### Why `mktemp`?
To create safe temporary resources.

### Why `"$@"`?
To preserve individual arguments correctly.

### Why `[[ ]]`?
It is Bash-native and provides safer conditional behavior.

### Why health checks?
A successful deployment command does not necessarily mean the application is actually serving traffic.

### Why rollback?
To restore the last known-good version when a deployment fails.

## Final Deliverables

Submit:

```text
deploy.sh
README.md
config/
scripts/
tests/
sample logs
architecture diagram
test results
```

## README Must Include

- Project objective
- Architecture
- Prerequisites
- Installation
- Configuration
- Usage
- Examples
- Failure handling
- Rollback strategy
- Security considerations
- Troubleshooting
- Sample output

## Final Challenge

After completing the project manually, integrate it into Jenkins:

```text
Jenkins
  ↓
Bash validation
  ↓
Git
  ↓
Tests
  ↓
Docker build
  ↓
Registry
  ↓
Kubernetes
  ↓
Health check
  ↓
Rollback if required
```

This turns Bash knowledge into a realistic DevOps automation project suitable for portfolio and interview discussion.
