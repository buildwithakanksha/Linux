# Bash Scripting — DevOps Assignments

Complete these in order. For every assignment, create a `.sh` file, test success and failure cases, and document what you learned.

## Beginner

### Assignment 1 — System Information
Create `system-info.sh`.

Display:
- Hostname
- Current user
- Current date/time
- OS information
- Kernel
- Uptime
- Current directory

### Assignment 2 — User Input
Create `user-info.sh`.

Ask for:
- Name
- Age
- City

Print a formatted result.

### Assignment 3 — File Checker
Create `file-check.sh`.

Accept a filename and check:
- Exists?
- Regular file?
- Readable?
- Writable?
- Executable?
- Empty/non-empty?

### Assignment 4 — Number Analyzer
Accept a number and determine:
- Positive/negative/zero
- Even/odd
- Greater than 100

### Assignment 5 — Backup
Create `backup.sh`.

Requirements:
- Source and destination as arguments
- `mkdir -p`
- Timestamped `.tar.gz`
- Verify archive
- Error handling

## Intermediate

### Assignment 6 — Log Analyzer
Given `application.log`:
- Count ERROR
- Count WARN
- Show last 20 errors
- Show unique errors
- Save a report

Use:
`grep`, `awk`, `sort`, `uniq`, `tail`.

### Assignment 7 — Disk Monitoring
Create `disk-monitor.sh`.
- Check `/`
- Alert above 80%
- Print usage
- Log timestamp
- Return non-zero when threshold is exceeded

### Assignment 8 — Service Monitor
Create `service-monitor.sh`.
- Accept service name
- Check status
- Restart if down
- Verify restart
- Log every action

### Assignment 9 — Process Monitor
- Accept process name
- Detect whether it is running
- Show PID(s)
- Show CPU/memory information
- Return failure if missing

### Assignment 10 — User Management
Create a script that:
- Accepts username
- Checks whether user exists
- Creates only when missing
- Adds user to a supplied group
- Reports the result

Use safe validation and appropriate privileges.

## Advanced

### Assignment 11 — Server Health Check
Build `server-health.sh` covering:
- Hostname
- Uptime
- Load
- CPU
- Memory
- Disk
- Top processes
- Listening ports
- Failed systemd services
- Recent critical logs

Produce a readable report.

### Assignment 12 — Deployment Script
Build:
```bash
./deploy.sh -e dev
./deploy.sh -e stage
./deploy.sh -e prod
```

Requirements:
- `getopts`
- Environment validation
- Dependency validation
- Git update
- Build
- Tests
- Docker image build
- Deployment
- Health check
- Clear exit codes
- Logging
- Rollback strategy

### Assignment 13 — AWS EC2 Report
Create a script that:
- Runs `aws sts get-caller-identity`
- Lists running EC2 instances
- Prints instance ID
- Private IP
- Public IP
- Instance type
- Environment tag

### Assignment 14 — Kubernetes Health
Create `k8s-health.sh`:
- Check cluster access
- Nodes
- NotReady nodes
- Unhealthy pods
- Deployment status
- Warning events
- Exit non-zero on critical failure

### Assignment 15 — Docker Automation
Create a script that:
- Checks Docker
- Builds an image
- Tags it
- Runs a container
- Performs an HTTP health check
- Cleans up on failure

### Assignment 16 — Terraform CI Validation
Create a script that:
- Checks Terraform
- Runs `terraform fmt -check`
- `terraform init`
- `terraform validate`
- `terraform plan`
- Stops on failures

### Assignment 17 — Production-Grade Script
Create one script combining:
- `set -Eeuo pipefail`
- Functions
- `getopts`
- Logging
- `trap`
- `mktemp`
- Locking
- Validation
- Error handling
- Cleanup
- Idempotency

### Assignment 18 — Full DevOps Automation Project
Build:
```text
devops-automation.sh
```

Flow:
```text
Validate dependencies
      ↓
Check Git
      ↓
Run tests
      ↓
Build artifact
      ↓
Build Docker image
      ↓
Push image
      ↓
Deploy to Kubernetes
      ↓
Wait for rollout
      ↓
Health check
      ↓
Report result
```

## Assignment Rules
For each assignment:
1. Write the script without copying a solution.
2. Test normal input.
3. Test invalid input.
4. Test missing files/tools.
5. Check exit code.
6. Run ShellCheck.
7. Add comments only where they improve clarity.
8. Add a README explaining usage.
