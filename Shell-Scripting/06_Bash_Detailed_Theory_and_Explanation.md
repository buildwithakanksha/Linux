# Bash Scripting — Detailed Theory & Explanation

> **Purpose:** This document is a theory-first reference for learning Bash deeply, from fundamentals to advanced DevOps automation.
>
> Use this file to understand **what Bash does, why it behaves that way, and when to use each feature**. Use the separate assignments, interview questions, cheat sheet, and project files for practice.

---

# 1. What Is Bash?

**Bash** stands for **Bourne Again SHell**.

Bash is both:

1. A **shell** — a program that accepts commands and asks the operating system to execute them.
2. A **scripting language** — a language used to combine commands, variables, conditions, loops, functions, and other logic into reusable automation.

A simple command:

```bash
ls
```

A simple script:

```bash
#!/usr/bin/env bash

echo "Starting deployment"
mkdir -p /opt/myapp
echo "Deployment preparation complete"
```

Bash is particularly important in DevOps because Linux systems, CI/CD tools, cloud CLIs, containers, and many infrastructure tools can be controlled from the command line.

---

# 2. What Happens When You Run a Bash Command?

When you type:

```bash
ls -lah /var/log
```

Bash roughly performs these stages:

```text
Read command
    ↓
Parse command
    ↓
Perform expansions
    ↓
Set up redirections
    ↓
Execute builtin/function/external command
    ↓
Wait if required
    ↓
Store exit status
```

Understanding this execution model helps explain many Bash behaviors.

For example:

```bash
echo "$HOME"
```

Bash expands `$HOME` before executing `echo`.

---

# 3. Shell vs Bash

A **shell** is a general concept.

Examples include:

```text
sh
bash
zsh
ksh
fish
```

Bash is one shell implementation.

Check the configured login shell:

```bash
echo "$SHELL"
```

Check the shell process:

```bash
ps -p $$ -o comm=
```

The two commands are not always equivalent because `$SHELL` generally represents the user's configured shell, while `ps` shows the current process.

---

# 4. Bash Script Structure

A professional script commonly looks like:

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

readonly SCRIPT_NAME="$(basename "$0")"

log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}

die() {
    log "ERROR: $*"
    exit 1
}

main() {
    log "Script started"

    # Main logic

    log "Script completed"
}

main "$@"
```

The important sections are:

```text
Shebang
↓
Shell options
↓
Constants/configuration
↓
Functions
↓
Main function
↓
main "$@"
```

This structure makes larger scripts easier to understand and test.

---

# 5. Shebang

A shebang is the first line:

```bash
#!/usr/bin/env bash
```

It tells the operating system which interpreter should run the script when the script is executed directly.

Another common form is:

```bash
#!/bin/bash
```

Using:

```bash
#!/usr/bin/env bash
```

allows the environment to locate Bash through `PATH`.

---

# 6. Comments

Comments are ignored by Bash.

```bash
# This is a comment
echo "Hello"
```

Use comments to explain:

- Why something is done
- Important assumptions
- Non-obvious logic
- Safety considerations

Avoid comments that simply repeat the code.

Poor:

```bash
# Increment i
((i++))
```

Better:

```bash
# Retry up to five times because the API may be temporarily unavailable.
((attempt++))
```

---

# 7. Commands, Builtins, Functions, and External Programs

Not every command you type is an external executable.

Bash can execute:

### Shell builtins

Examples:

```bash
cd
echo
read
export
unset
printf
```

### Functions

```bash
hello() {
    echo "Hello"
}
```

### External commands

Examples:

```bash
ls
grep
awk
sed
curl
docker
kubectl
terraform
```

Check how Bash resolves a command:

```bash
type cd
type echo
type grep
```

Or:

```bash
command -v grep
```

This is useful when troubleshooting PATH or command conflicts.

---

# 8. Variables

A variable stores a value.

```bash
name="Akanksha"
```

Read it:

```bash
echo "$name"
```

The assignment syntax is important.

Correct:

```bash
name="Akanksha"
```

Incorrect:

```bash
name = "Akanksha"
```

Bash interprets the latter as a command named `name`.

---

# 9. Why Should Variables Usually Be Quoted?

Consider:

```bash
file="my application.log"
```

This can cause problems:

```bash
cat $file
```

Bash may perform word splitting and interpret it as multiple words.

Prefer:

```bash
cat "$file"
```

Quoting tells Bash:

> Treat the expanded value as one word.

This is one of the most important Bash habits.

---

# 10. Single Quotes vs Double Quotes

## Single quotes

```bash
name="Akanksha"
echo '$name'
```

Output:

```text
$name
```

Single quotes prevent normal variable and command expansion.

## Double quotes

```bash
echo "$name"
```

Output:

```text
Akanksha
```

Double quotes allow variable expansion while protecting the result from word splitting and pathname expansion.

---

# 11. Environment Variables

A normal shell variable:

```bash
APP_ENV="dev"
```

is available to the current shell.

An exported variable:

```bash
export APP_ENV="dev"
```

is placed into the environment inherited by child processes.

Example:

```bash
export APP_ENV="production"

bash -c 'echo "$APP_ENV"'
```

The child Bash can see the variable because it was exported.

---

# 12. Local Variables in Functions

Without `local`, a function variable can affect the surrounding shell.

Prefer:

```bash
deploy() {
    local environment="$1"
    echo "$environment"
}
```

`local` limits the variable's scope to the function.

This reduces accidental side effects.

---

# 13. Special Parameters

Important Bash parameters:

| Parameter | Meaning |
|---|---|
| `$0` | Script/command name |
| `$1` | First positional argument |
| `$2` | Second positional argument |
| `$#` | Number of positional arguments |
| `"$@"` | All positional arguments separately |
| `"$*"` | All positional arguments as one word |
| `$?` | Previous command's exit status |
| `$$` | Current shell PID |
| `$!` | PID of most recent background command |

Example:

```bash
#!/usr/bin/env bash

echo "Script: $0"
echo "First argument: $1"
echo "Argument count: $#"
```

---

# 14. `$@` vs `$*`

This is a common interview question.

Suppose:

```bash
./script.sh "hello world" linux
```

Using:

```bash
for arg in "$@"; do
    echo "$arg"
done
```

produces two arguments:

```text
hello world
linux
```

`"$@"` preserves the original argument boundaries.

With:

```bash
"$*"
```

the arguments are treated as a single word, joined using the first character of `IFS`.

For forwarding arguments, prefer:

```bash
"$@"
```

---

# 15. Command Substitution

Command substitution captures command output.

```bash
hostname_value=$(hostname)
```

Then:

```bash
echo "$hostname_value"
```

Another example:

```bash
today=$(date '+%F')
```

Modern Bash scripts should generally use:

```bash
$(command)
```

instead of old backticks:

```bash
`command`
```

---

# 16. Exit Status

Commands return an exit status.

Convention:

```text
0       success
non-zero failure
```

Example:

```bash
ls /tmp
echo "$?"
```

A script can explicitly return:

```bash
exit 0
```

or:

```bash
exit 1
```

The exact non-zero value can communicate different failure categories when you design your own scripts.

---

# 17. Why Exit Codes Matter in DevOps

CI/CD systems depend heavily on exit codes.

Example:

```bash
./run-tests.sh
```

If the tests fail and the script returns non-zero, Jenkins or another CI system can mark the build as failed.

Good:

```bash
if ./run-tests.sh; then
    echo "Tests passed"
else
    echo "Tests failed"
    exit 1
fi
```

Bad:

```bash
./run-tests.sh
echo "Deployment successful"
```

The second example can incorrectly report success if the test command failed and the script continues.

---

# 18. `&&`, `||`, and `;`

## AND

```bash
command1 && command2
```

Run `command2` only if `command1` succeeds.

Example:

```bash
mkdir -p /opt/app && echo "Directory ready"
```

## OR

```bash
command1 || command2
```

Run `command2` if `command1` fails.

Example:

```bash
systemctl is-active --quiet nginx || echo "Nginx is down"
```

## Semicolon

```bash
command1 ; command2
```

Attempt both commands regardless of the first command's exit status.

---

# 19. Conditional Statements

Bash provides conditional execution.

```bash
if [[ condition ]]; then
    commands
fi
```

Example:

```bash
if [[ -f "$config" ]]; then
    echo "Config exists"
fi
```

With else:

```bash
if [[ -f "$config" ]]; then
    echo "Config exists"
else
    echo "Config missing"
fi
```

With multiple branches:

```bash
if [[ "$environment" == "prod" ]]; then
    echo "Production"
elif [[ "$environment" == "stage" ]]; then
    echo "Staging"
else
    echo "Development"
fi
```

---

# 20. Why Prefer `[[ ]]`?

Bash supports older test syntax:

```bash
[ "$a" = "$b" ]
```

For Bash-specific scripts, prefer:

```bash
[[ "$a" == "$b" ]]
```

`[[ ]]` provides more predictable behavior and supports features such as:

- Pattern matching
- Regex with `=~`
- Better handling of special characters
- Logical expressions

It is especially useful when writing Bash rather than generic POSIX `sh`.

---

# 21. File Tests

Bash can test filesystem properties.

```bash
[[ -e "$path" ]]
```

means the path exists.

Common tests:

```text
-e  exists
-f  regular file
-d  directory
-r  readable
-w  writable
-x  executable
-s  non-empty
-L  symbolic link
```

Example:

```bash
if [[ -d "$DEST" ]]; then
    echo "Directory exists"
fi
```

---

# 22. `mkdir -p` Explained

Command:

```bash
mkdir -p "$DEST"
```

The `-p` option means:

1. Create missing parent directories.
2. Do not report an error merely because the target directory already exists.

Example:

```bash
DEST="/opt/myapp/logs"
mkdir -p "$DEST"
```

If `/opt/myapp` does not exist, Bash asks `mkdir` to create the required directory hierarchy.

This is extremely common in DevOps scripts because it makes directory creation repeatable.

---

# 23. String Comparison

Use:

```bash
[[ "$environment" == "prod" ]]
```

Not:

```bash
[[ "$environment" -eq "prod" ]]
```

`-eq` is for numeric comparison.

Useful string operators:

```bash
[[ -z "$value" ]]   # empty
[[ -n "$value" ]]   # non-empty
[[ "$a" == "$b" ]]  # equal
[[ "$a" != "$b" ]]  # not equal
```

---

# 24. Numeric Comparison

Bash arithmetic uses:

```bash
(( ... ))
```

Example:

```bash
if (( usage >= 80 )); then
    echo "Disk threshold exceeded"
fi
```

Common operators:

```text
>   greater than
>=  greater than or equal
<   less than
<=  less than or equal
==  equal
!=  not equal
```

---

# 25. Case Statements

`case` is useful when a variable can have multiple known values.

```bash
case "$action" in
    start)
        echo "Starting"
        ;;
    stop)
        echo "Stopping"
        ;;
    restart)
        echo "Restarting"
        ;;
    *)
        echo "Unknown action"
        exit 1
        ;;
esac
```

This is often cleaner than a long chain of `if/elif`.

---

# 26. Loops

## `for`

```bash
for server in web1 web2 web3; do
    echo "$server"
done
```

## C-style `for`

```bash
for ((i=1; i<=5; i++)); do
    echo "$i"
done
```

## `while`

```bash
count=1

while (( count <= 5 )); do
    echo "$count"
    ((count++))
done
```

## `until`

```bash
count=1

until (( count > 5 )); do
    echo "$count"
    ((count++))
done
```

---

# 27. Reading Files Correctly

Preferred pattern:

```bash
while IFS= read -r line; do
    printf '%s\n' "$line"
done < input.txt
```

Why these options?

### `IFS=`

Prevents unwanted trimming of leading/trailing whitespace.

### `read -r`

Prevents backslashes from being interpreted as escape characters.

This is safer than:

```bash
cat input.txt | while read line; do
    ...
done
```

---

# 28. Pipelines

A pipeline connects stdout of one command to stdin of another.

```bash
ps aux | grep nginx
```

Conceptually:

```text
ps aux
  ↓
stdout
  ↓
pipe
  ↓
grep nginx
  ↓
stdout
```

Pipelines are powerful for Linux administration and log processing.

---

# 29. Redirection

Linux programs normally have three standard file descriptors:

```text
0 = stdin
1 = stdout
2 = stderr
```

## stdout

```bash
command > output.txt
```

## Append stdout

```bash
command >> output.txt
```

## stderr

```bash
command 2> error.txt
```

## stdout + stderr

```bash
command > output.txt 2>&1
```

## Discard everything

```bash
command >/dev/null 2>&1
```

Understanding file descriptors is essential for CI/CD logging.

---

# 30. Here Documents

A here document provides multiline input.

```bash
cat <<EOF
Application: myapp
Environment: $APP_ENV
Port: 8080
EOF
```

Because the delimiter is unquoted, variables are expanded.

With:

```bash
cat <<'EOF'
$APP_ENV
EOF
```

the value remains literal.

Here documents are useful for:

- Configuration generation
- SQL
- SSH commands
- Multi-line messages
- Automated configuration

---

# 31. Functions

Functions improve reuse.

```bash
log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}
```

Call:

```bash
log "Deployment started"
```

Function with argument:

```bash
check_service() {
    local service="$1"

    systemctl is-active --quiet "$service"
}
```

---

# 32. Function Return Values

Functions normally communicate success/failure through exit status.

```bash
is_running() {
    systemctl is-active --quiet nginx
}
```

Use:

```bash
if is_running; then
    echo "Running"
fi
```

A function can return a status:

```bash
return 1
```

Do not confuse `return` with output.

If you want to return data:

```bash
get_hostname() {
    hostname
}

server=$(get_hostname)
```

Here the function prints data, which command substitution captures.

---

# 33. Arrays

Indexed array:

```bash
servers=("web1" "web2" "web3")
```

Access:

```bash
echo "${servers[0]}"
```

All elements:

```bash
echo "${servers[@]}"
```

Length:

```bash
echo "${#servers[@]}"
```

Safe iteration:

```bash
for server in "${servers[@]}"; do
    echo "$server"
done
```

---

# 34. Associative Arrays

Associative arrays provide key/value storage.

```bash
declare -A server_ip

server_ip[web1]="10.0.1.10"
server_ip[web2]="10.0.1.11"

echo "${server_ip[web1]}"
```

They are useful when your data naturally has names/keys.

---

# 35. String Manipulation

Length:

```bash
text="Hello"
echo "${#text}"
```

Uppercase:

```bash
echo "${text^^}"
```

Lowercase:

```bash
echo "${text,,}"
```

Replace first:

```bash
echo "${text/old/new}"
```

Replace all:

```bash
echo "${text//old/new}"
```

Remove prefix:

```bash
echo "${path#/var/log/}"
```

Remove suffix:

```bash
echo "${file%.log}"
```

---

# 36. Parameter Expansion

Parameter expansion is one of Bash's most powerful features.

Default:

```bash
echo "${ENV:-dev}"
```

Meaning:

> Use `$ENV` if it is set and non-empty; otherwise use `dev`.

Require a value:

```bash
echo "${ENV:?ENV is required}"
```

Assign default:

```bash
: "${ENV:=dev}"
```

Remove prefix:

```bash
filename="/tmp/app.log"
echo "${filename#/tmp/}"
```

Remove suffix:

```bash
echo "${filename%.log}"
```

---

# 37. `source` vs Executing a Script

Suppose:

```bash
config.sh
```

contains:

```bash
export APP_ENV=production
```

Executing:

```bash
./config.sh
```

runs it in a separate process environment. Changes generally do not modify the current shell.

Sourcing:

```bash
source config.sh
```

or:

```bash
. config.sh
```

runs the commands in the current shell.

Therefore:

```bash
source config.sh
echo "$APP_ENV"
```

can access the exported variable in the current shell.

This distinction is important in CI/CD scripts and environment configuration.

---

# 38. Subshells

Parentheses create a subshell:

```bash
(
    cd /tmp
    echo "$PWD"
)
```

The directory change does not affect the parent shell.

This is different from:

```bash
{
    cd /tmp
    echo "$PWD"
}
```

which is a command group executed in the current shell.

---

# 39. Command Group vs Subshell

Current shell:

```bash
{
    command1
    command2
}
```

Subshell:

```bash
(
    command1
    command2
)
```

Subshells are useful when you want to isolate shell state.

For example:

```bash
(
    cd /some/directory
    run_commands
)
```

The parent shell remains in its original directory.

---

# 40. `set -e`

```bash
set -e
```

enables `errexit`.

It causes Bash to exit when a command fails in contexts where the `errexit` rule applies.

Important:

> `set -e` has exceptions and contextual behavior. It should not be treated as a universal error handler.

For critical operations, explicit checks are still useful:

```bash
if ! terraform apply; then
    echo "Terraform failed" >&2
    exit 1
fi
```

---

# 41. `set -u`

```bash
set -u
```

Treats expansion of unset variables as an error.

This can catch bugs such as:

```bash
echo "$UNDEFINED_VARIABLE"
```

Use defaults when a variable is intentionally optional:

```bash
echo "${OPTIONAL_VALUE:-}"
```

---

# 42. `pipefail`

Normally:

```bash
command1 | command2
```

gets the exit status of the last command.

With:

```bash
set -o pipefail
```

the pipeline can fail when an earlier command fails.

This matters in CI/CD.

Example:

```bash
set -o pipefail

curl -fsS "$URL" | jq .
```

If `curl` fails, the pipeline should not silently look successful just because another command exits successfully.

---

# 43. Strict Mode

A common Bash baseline is:

```bash
set -Eeuo pipefail
```

It combines:

```text
-e   errexit
-u   nounset
-E   ERR trap inheritance
pipefail
```

Use it as a foundation, but understand the behavior of each option.

Good production scripts still use:

- Explicit validation
- Error messages
- Exit codes
- Cleanup
- Tests

---

# 44. `trap`

`trap` lets you respond to signals and shell events.

Example:

```bash
cleanup() {
    rm -rf "$tmp_dir"
}

trap cleanup EXIT
```

This means cleanup runs when the shell exits normally or because of many common exit paths.

Handle Ctrl+C:

```bash
trap 'echo "Interrupted"; exit 130' INT
```

Useful events include:

```text
EXIT
ERR
INT
TERM
HUP
```

---

# 45. Temporary Files

Avoid predictable temporary names.

Risky:

```bash
tmp="/tmp/myfile.txt"
```

Prefer:

```bash
tmp=$(mktemp)
```

Temporary directory:

```bash
tmp_dir=$(mktemp -d)
```

Cleanup:

```bash
cleanup() {
    rm -rf "$tmp_dir"
}

trap cleanup EXIT
```

This is safer and easier to manage.

---

# 46. Locking and Concurrency

Suppose Jenkins starts the same deployment twice.

Both scripts may try to deploy simultaneously.

Use `flock`:

```bash
exec 9>/tmp/deploy.lock

if ! flock -n 9; then
    echo "Another deployment is running"
    exit 1
fi
```

The lock prevents simultaneous execution of the protected section.

This is useful for:

- Deployment scripts
- Backups
- Scheduled jobs
- Database maintenance
- Cleanup scripts

---

# 47. `getopts`

`getopts` allows scripts to support command-line options.

Example interface:

```bash
./deploy.sh -e prod -v
```

Typical options:

```text
-e environment
-v verbose
-h help
```

Example:

```bash
while getopts ":e:vh" opt; do
    case "$opt" in
        e) environment="$OPTARG" ;;
        v) verbose=true ;;
        h) usage; exit 0 ;;
        \?) echo "Invalid option"; exit 2 ;;
        :) echo "Option requires an argument"; exit 2 ;;
    esac
done
```

This is much better than manually interpreting arguments for larger scripts.

---

# 48. Regular Expressions

Bash can use regular expressions with:

```bash
[[ string =~ regex ]]
```

Example:

```bash
if [[ "$environment" =~ ^(dev|stage|prod)$ ]]; then
    echo "Valid environment"
else
    echo "Invalid environment"
fi
```

Regex is useful for validation, but avoid building excessively complex validation logic when a simpler method is clearer.

---

# 49. `grep`

`grep` searches text.

```bash
grep "ERROR" app.log
```

Case insensitive:

```bash
grep -i "error" app.log
```

Line number:

```bash
grep -n "ERROR" app.log
```

Recursive:

```bash
grep -R "TODO" .
```

Extended regex:

```bash
grep -E "ERROR|WARN" app.log
```

Invert:

```bash
grep -v "INFO" app.log
```

---

# 50. `sed`

`sed` is a stream editor.

Replace:

```bash
sed 's/old/new/' file
```

Replace all occurrences per line:

```bash
sed 's/old/new/g' file
```

Delete matching lines:

```bash
sed '/DEBUG/d' app.log
```

Print selected lines:

```bash
sed -n '1,10p' file
```

In-place edit:

```bash
sed -i 's/old/new/g' file
```

Be careful with `-i` because it modifies the original file.

---

# 51. `awk`

`awk` is especially useful for structured text and columns.

```bash
awk '{print $1}' file
```

Delimiter:

```bash
awk -F: '{print $1}' /etc/passwd
```

Conditional:

```bash
awk '$3 > 80 {print $1, $3}' data.txt
```

Aggregation:

```bash
awk '{sum += $1} END {print sum}' numbers.txt
```

A DevOps engineer should be comfortable using `awk` for logs, reports, system information and command output.

---

# 52. `find`

`find` searches filesystem objects.

```bash
find /var/log -type f
```

By name:

```bash
find /var/log -type f -name "*.log"
```

By size:

```bash
find / -type f -size +500M 2>/dev/null
```

By modification age:

```bash
find /var/log -type f -mtime +7
```

Execute:

```bash
find . -type f -name "*.log" -exec gzip {} \;
```

---

# 53. `xargs`

`xargs` builds command arguments from input.

Example:

```bash
printf '%s\n' file1 file2 | xargs rm --
```

For filenames with spaces or special characters, use null delimiters:

```bash
find . -type f -print0 | xargs -0 ...
```

Understanding `-print0` and `-0` is important for robust filesystem automation.

---

# 54. Processes

A process is a running program.

View processes:

```bash
ps aux
```

Find process IDs:

```bash
pgrep nginx
```

Terminate:

```bash
kill PID
```

Force termination:

```bash
kill -9 PID
```

`SIGTERM` is generally preferable to `SIGKILL` when graceful shutdown is possible.

---

# 55. Background Jobs

Run in background:

```bash
long_command &
```

View jobs:

```bash
jobs
```

Foreground:

```bash
fg
```

Background:

```bash
bg
```

Wait for background jobs:

```bash
wait
```

Capture PID:

```bash
long_command &
pid=$!
wait "$pid"
```

This is useful when scripts need controlled parallel execution.

---

# 56. Cron

Cron schedules recurring commands.

Edit:

```bash
crontab -e
```

Example:

```text
0 2 * * * /opt/scripts/backup.sh
```

Meaning:

```text
minute hour day-of-month month day-of-week
```

For production cron scripts:

- Use absolute paths
- Set a known `PATH` if required
- Redirect output
- Handle failures
- Use locks when overlap is possible
- Log useful information

---

# 57. SSH Automation

Run remote command:

```bash
ssh user@server "hostname"
```

Copy file:

```bash
scp app.tar.gz user@server:/tmp/
```

Synchronize directories:

```bash
rsync -av ./app/ user@server:/opt/app/
```

For automation, SSH keys are generally preferable to interactive passwords.

Never put passwords directly into scripts.

---

# 58. `curl` and APIs

`curl` is commonly used in DevOps automation.

Health check:

```bash
curl -fsS --max-time 10 https://example.com/health
```

Important flags:

```text
-f  fail on HTTP errors
-s  silent
-S  show errors with silent mode
-L  follow redirects
```

POST JSON:

```bash
curl -fsS \
    -X POST \
    -H 'Content-Type: application/json' \
    -d '{"name":"demo"}' \
    https://example.com/api
```

---

# 59. JSON and `jq`

Do not reliably parse JSON using regular text tools.

Use:

```bash
jq
```

Pretty print:

```bash
curl -s https://example.com/api | jq .
```

Extract:

```bash
curl -s https://example.com/api | jq -r '.name'
```

Filter:

```bash
jq '.items[] | select(.status == "running")'
```

This is extremely useful with cloud and Kubernetes APIs.

---

# 60. AWS CLI and Bash

Bash often wraps AWS CLI commands.

Identity:

```bash
aws sts get-caller-identity
```

EC2:

```bash
aws ec2 describe-instances
```

S3:

```bash
aws s3 ls
```

Use structured AWS output:

```bash
aws ec2 describe-instances \
    --query 'Reservations[].Instances[].InstanceId' \
    --output text
```

Never hardcode long-lived access keys.

Prefer:

- IAM roles
- Instance profiles
- CI/CD credential stores
- Short-lived credentials

---

# 61. Docker and Bash

Bash can automate Docker workflows.

```bash
docker build -t myapp:latest .
docker run -d --name myapp -p 8080:80 myapp:latest
```

Check:

```bash
docker ps
docker logs myapp
```

A robust script should verify that each important Docker command succeeds.

---

# 62. Kubernetes and Bash

Bash commonly orchestrates `kubectl`.

```bash
kubectl get nodes
kubectl get pods -A
kubectl apply -f deployment.yaml
```

Wait for rollout:

```bash
kubectl rollout status deployment/myapp
```

Rollback:

```bash
kubectl rollout undo deployment/myapp
```

Example:

```bash
if kubectl apply -f deployment.yaml; then
    kubectl rollout status deployment/myapp
else
    echo "Apply failed" >&2
    exit 1
fi
```

---

# 63. Terraform and Bash

Bash can create a validation wrapper:

```bash
terraform fmt -check -recursive
terraform init
terraform validate
terraform plan
```

The script should not automatically run destructive operations such as:

```bash
terraform destroy
```

without deliberate safeguards.

For infrastructure provisioning, prefer Terraform's declarative model and use Bash as orchestration/glue rather than recreating Terraform's functionality.

---

# 64. Jenkins and Bash

A Jenkins pipeline may run:

```bash
#!/usr/bin/env bash
set -Eeuo pipefail

./test.sh
docker build -t "$IMAGE:$BUILD_NUMBER" .
docker push "$IMAGE:$BUILD_NUMBER"
kubectl apply -f k8s/
kubectl rollout status deployment/myapp
```

Bash should:

- Fail on important errors
- Produce useful logs
- Avoid secrets
- Return meaningful exit codes
- Be deterministic
- Work non-interactively

---

# 65. Logging

A basic logging function:

```bash
log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}
```

Usage:

```bash
log "Starting deployment"
```

Error:

```bash
log_error() {
    printf '[%s] ERROR: %s\n' "$(date '+%F %T')" "$*" >&2
}
```

Good logs answer:

```text
What happened?
When?
Where?
Which application?
Which environment?
What failed?
```

---

# 66. Error Handling

Use a dedicated error function:

```bash
die() {
    echo "ERROR: $*" >&2
    exit 1
}
```

Example:

```bash
[[ -f "$config" ]] || die "Config file missing: $config"
```

For important external commands:

```bash
if ! command; then
    die "Command failed"
fi
```

Good error handling should tell the operator what failed and what context matters.

---

# 67. ERR Trap

Example:

```bash
trap 'rc=$?; echo "ERROR line=$LINENO status=$rc command=$BASH_COMMAND" >&2' ERR
```

This can provide useful diagnostics.

However, Bash error handling has contextual rules, so traps should complement—not replace—explicit validation.

---

# 68. Debugging Bash

Syntax check:

```bash
bash -n script.sh
```

Trace:

```bash
bash -x script.sh
```

Inside a script:

```bash
set -x
```

Disable:

```bash
set +x
```

A useful trace prefix:

```bash
PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]}: '
```

Then:

```bash
set -x
```

Use ShellCheck as well:

```bash
shellcheck script.sh
```

---

# 69. ShellCheck

ShellCheck is a static analysis tool for shell scripts.

It can detect issues involving:

- Quoting
- Variables
- Tests
- Redirections
- Common shell pitfalls
- Suspicious constructs

Run:

```bash
shellcheck script.sh
```

Treat warnings as learning opportunities rather than blindly suppressing them.

---

# 70. Idempotency

An idempotent operation can be run repeatedly without producing unwanted changes after the desired state has been reached.

Example:

```bash
mkdir -p /opt/myapp
```

is naturally repeatable.

A script should avoid doing something destructive every time it runs when the desired state already exists.

DevOps tools such as Terraform and Kubernetes are strongly oriented around desired state.

Bash scripts should follow the same principle where practical.

---

# 71. Input Validation

Never assume users provide valid input.

Example:

```bash
environment="${1:-}"

case "$environment" in
    dev|stage|prod)
        ;;
    *)
        echo "Invalid environment: $environment" >&2
        exit 2
        ;;
esac
```

Validate:

- Required arguments
- File paths
- Environment names
- Numeric values
- URLs
- Resource names
- Dangerous operations

---

# 72. Bash Security

## Never hardcode secrets

Avoid:

```bash
PASSWORD="secret123"
```

Avoid:

```bash
curl -u admin:password ...
```

Prefer secure secret mechanisms.

## Avoid `eval`

Dangerous:

```bash
eval "$user_input"
```

`eval` converts text into shell code.

## Quote variables

Prefer:

```bash
rm -- "$file"
```

## Be careful with destructive commands

Especially:

```bash
rm -rf
sudo
chmod -R
chown -R
terraform destroy
kubectl delete
```

Validate targets before destructive actions.

---

# 73. Safe `rm -rf` Thinking

Never assume a variable is populated.

Dangerous:

```bash
rm -rf "$TARGET"/*
```

A safe script should validate the variable and intended directory before deletion.

Example:

```bash
[[ -n "$TARGET" ]] || die "TARGET is empty"
[[ "$TARGET" != "/" ]] || die "Refusing to operate on /"
```

Additional validation should be based on the exact application's requirements.

---

# 74. File Descriptors — Deeper Concept

File descriptors are integer handles.

```text
0 stdin
1 stdout
2 stderr
```

Open a file descriptor:

```bash
exec 9>/tmp/my.lock
```

Use it for locking:

```bash
flock -n 9
```

Close:

```bash
exec 9>&-
```

Understanding file descriptors helps with:

- Logging
- Parallel processes
- Locks
- Redirection
- Advanced shell automation

---

# 75. Process Substitution

Bash supports process substitution.

Example:

```bash
diff <(sort file1.txt) <(sort file2.txt)
```

The commands produce file-like streams that `diff` can read.

Another example:

```bash
while IFS= read -r line; do
    echo "$line"
done < <(generate_data)
```

This is powerful but should be used only when it improves clarity.

---

# 76. Command Substitution vs Process Substitution

Command substitution:

```bash
output=$(command)
```

captures command output into a variable.

Process substitution:

```bash
diff <(command1) <(command2)
```

provides command output through a file-like interface.

Think:

```text
$(...)  → data as text
<(...)  → file-like input
```

---

# 77. Shell Expansion Order — Practical View

Bash performs several transformations before executing a command.

Important concepts include:

```text
Parameter expansion
Command substitution
Arithmetic expansion
Word splitting
Pathname expansion
Quote removal
```

Example:

```bash
echo "$HOME/*.log"
```

Because the expression is quoted, pathname expansion does not expand the wildcard.

Whereas:

```bash
echo $HOME/*.log
```

can be affected by word splitting and globbing.

This is why quoting matters.

---

# 78. Globbing

Globbing expands patterns against filenames.

Examples:

```bash
*.log
app-*.txt
file?.txt
```

Example:

```bash
for file in *.log; do
    echo "$file"
done
```

Be careful when there are no matches; robust scripts may need to handle that case explicitly.

---

# 79. Bash `nullglob`

In Bash, you can use:

```bash
shopt -s nullglob
```

Then an unmatched pattern such as:

```bash
*.log
```

expands to nothing rather than remaining literally `*.log`.

Example:

```bash
shopt -s nullglob

files=(/var/log/*.log)

for file in "${files[@]}"; do
    echo "$file"
done
```

This is useful when processing optional file sets.

---

# 80. Environment and Configuration Design

Do not mix application configuration and business logic unnecessarily.

A common pattern:

```text
script
  ↓
load configuration
  ↓
validate configuration
  ↓
execute
```

Example:

```bash
: "${APP_ENV:?APP_ENV is required}"
: "${IMAGE_TAG:?IMAGE_TAG is required}"
```

This makes missing configuration obvious.

---

# 81. Configuration Files

Example:

```bash
# config.env
APP_ENV=dev
APP_PORT=8080
```

Load:

```bash
source config.env
```

But remember:

> `source` executes shell code.

Do not source an untrusted file.

For safer configuration formats, consider formats/tools that treat configuration as data rather than executable shell code.

---

# 82. Bash and YAML

Bash can generate YAML, but YAML is not a shell language.

Avoid complicated YAML construction with string concatenation when possible.

For Kubernetes automation, prefer:

- Existing manifests
- Helm
- Kustomize
- `kubectl`
- Proper templating tools

Use Bash as orchestration.

---

# 83. Bash and JSON

Similarly, do not manually build complex JSON with string concatenation.

Risky:

```bash
json="{\"name\":\"$name\"}"
```

For non-trivial JSON, use:

```bash
jq
```

For example:

```bash
jq -n --arg name "$name" '{name: $name}'
```

This handles JSON escaping correctly.

---

# 84. Bash and YAML/JSON Injection

If user-controlled input is inserted into generated configuration, escaping and validation become important.

Examples of unsafe assumptions:

- Environment names are trusted
- Application names contain only safe characters
- User input is valid JSON
- User input is valid YAML

Always validate data before using it in commands or generated configuration.

---

# 85. Parallel Execution

Simple background execution:

```bash
task1 &
task2 &
wait
```

Capture statuses:

```bash
task1 &
pid1=$!

task2 &
pid2=$!

wait "$pid1"
status1=$?

wait "$pid2"
status2=$?
```

Parallelism can improve performance, but it also creates complexity around:

- Error handling
- Resource contention
- Logging
- Cleanup
- Ordering
- Race conditions

Use it deliberately.

---

# 86. Race Conditions

A race condition occurs when correctness depends on timing between operations.

Example concept:

```text
Check file exists
      ↓
Use file
```

Another process may modify/delete the file between those steps.

Safer patterns often use atomic operations, locks, temporary files, or dedicated tools.

This is one reason `mktemp` and `flock` are useful.

---

# 87. Cron vs systemd Timers

Cron is simple and widely available.

Systemd timers can provide deeper integration with systemd services, logging, dependencies, and execution controls.

For modern Linux infrastructure, know both concepts.

---

# 88. Bash in CI/CD

Bash is often the glue between DevOps tools.

Example:

```text
Git
 ↓
Bash
 ↓
Tests
 ↓
Docker
 ↓
Registry
 ↓
kubectl
 ↓
Kubernetes
```

The Bash layer should:

- Validate prerequisites
- Stop on critical failures
- Log actions
- Pass correct exit codes
- Avoid interactive prompts
- Handle credentials securely

---

# 89. Production Bash Principles

A production script should ideally be:

### Readable
Another engineer should understand it.

### Safe
It should not accidentally destroy production resources.

### Idempotent
Repeated runs should be predictable.

### Observable
Logs should explain what happened.

### Testable
Failure paths should be testable.

### Deterministic
Same inputs should produce predictable results.

### Secure
Secrets and privileges should be handled correctly.

### Maintainable
Avoid unnecessary cleverness.

---

# 90. Recommended Production Template

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

readonly SCRIPT_NAME="$(basename "$0")"
readonly SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd)"

log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}

die() {
    log "ERROR: $*"
    exit 1
}

cleanup() {
    if [[ -n "${TMP_DIR:-}" && -d "$TMP_DIR" ]]; then
        rm -rf -- "$TMP_DIR"
    fi
}

trap cleanup EXIT

usage() {
    cat <<EOF
Usage: $SCRIPT_NAME -e ENVIRONMENT

Options:
  -e ENVIRONMENT   dev|stage|prod
  -h               Show help
EOF
}

main() {
    local environment=""

    while getopts ":e:h" opt; do
        case "$opt" in
            e) environment="$OPTARG" ;;
            h) usage; exit 0 ;;
            :) die "Option -$OPTARG requires an argument" ;;
            \?) die "Invalid option: -$OPTARG" ;;
        esac
    done

    case "$environment" in
        dev|stage|prod) ;;
        *) die "Invalid environment: $environment" ;;
    esac

    TMP_DIR=$(mktemp -d)

    log "Environment: $environment"
    log "Script completed"
}

main "$@"
```

---

# 91. Common Mistakes to Avoid

## Mistake 1

```bash
name = "Akanksha"
```

Correct:

```bash
name="Akanksha"
```

## Mistake 2

```bash
rm $file
```

Prefer:

```bash
rm -- "$file"
```

## Mistake 3

```bash
cat file | grep ERROR
```

Usually prefer:

```bash
grep ERROR file
```

## Mistake 4

Using `grep` to parse JSON.

Use:

```bash
jq
```

## Mistake 5

Hardcoding secrets.

Use secure credential systems.

## Mistake 6

Using `eval` with input.

Avoid it.

## Mistake 7

Ignoring exit codes.

Check important commands.

## Mistake 8

Using predictable temporary files.

Use:

```bash
mktemp
```

## Mistake 9

Assuming `set -e` solves every error-handling problem.

Understand its exceptions and explicitly check critical operations.

## Mistake 10

Writing giant scripts without functions.

Break logic into meaningful functions.

---

# 92. Bash vs Python — When to Use Which?

Bash is excellent for:

- Linux command orchestration
- Simple automation
- CI/CD glue
- File operations
- Process control
- Calling cloud/container tools
- Short system administration scripts

Python is often better for:

- Complex business logic
- Large applications
- Advanced data structures
- Complex API clients
- Extensive testing
- Cross-platform applications
- Sophisticated error handling

Rule of thumb:

> Use Bash to orchestrate commands; use a general-purpose language when the logic becomes complex enough that Bash becomes difficult to maintain.

---

# 93. Bash vs Ansible

Bash:

```text
Imperative
"Do these commands"
```

Ansible:

```text
Declarative/idempotent-oriented
"Make the system look like this"
```

Example Bash:

```bash
apt-get update
apt-get install -y nginx
systemctl enable --now nginx
```

Ansible can describe the desired package/service state more declaratively.

Bash remains useful for custom orchestration and small automation tasks.

---

# 94. Bash vs Terraform

Terraform is designed for infrastructure as code and desired-state management.

Bash is usually the surrounding orchestration layer.

Prefer:

```text
Terraform → infrastructure
Bash      → orchestration/glue
```

Do not use Bash to manually recreate complex infrastructure state management that Terraform already handles.

---

# 95. Bash vs Kubernetes

Kubernetes manages desired application state.

Bash can call:

```bash
kubectl
```

to automate workflows.

Prefer:

```text
Kubernetes → desired workload state
Bash       → automation around Kubernetes
```

---

# 96. Bash Troubleshooting Method

When a Bash script fails:

## Step 1 — Syntax

```bash
bash -n script.sh
```

## Step 2 — Shell trace

```bash
bash -x script.sh
```

## Step 3 — Identify failing command

Check:

```bash
$?
```

## Step 4 — Check environment

```bash
env
echo "$PATH"
```

## Step 5 — Check dependencies

```bash
command -v docker
command -v kubectl
```

## Step 6 — Check permissions

```bash
ls -l
id
```

## Step 7 — Check filesystem/network/service state

Use:

```bash
df -h
du -sh
ss -tulnp
systemctl status
```

This systematic approach is more effective than randomly changing commands.

---

# 97. DevOps Bash Mental Model

When writing a Bash automation script, think:

```text
INPUT
 ↓
VALIDATE
 ↓
PRECHECK
 ↓
EXECUTE
 ↓
VERIFY
 ↓
CLEANUP
 ↓
REPORT
```

Example deployment:

```text
Environment
 ↓
Validate environment
 ↓
Check tools
 ↓
Check Git
 ↓
Build
 ↓
Test
 ↓
Docker build
 ↓
Push
 ↓
Deploy
 ↓
Health check
 ↓
Rollback if required
 ↓
Log result
```

This mental model is extremely useful in DevOps interviews.

---

# 98. Final Theory Checklist

Before considering your Bash fundamentals complete, you should be able to explain:

- What Bash is
- How a command is executed
- Shebang
- Variables
- Environment variables
- Quoting
- Word splitting
- Globbing
- Command substitution
- Exit codes
- `&&`, `||`, `;`
- `if`
- `case`
- `for`
- `while`
- `until`
- Functions
- `local`
- Arguments
- `"$@"` vs `"$*"`
- Arrays
- Associative arrays
- Parameter expansion
- File tests
- Pipes
- stdin/stdout/stderr
- Redirection
- Here documents
- Subshells
- `source`
- `trap`
- `getopts`
- `set -e`
- `set -u`
- `pipefail`
- `set -x`
- `mktemp`
- `flock`
- `grep`
- `sed`
- `awk`
- `find`
- `xargs`
- Cron
- SSH
- curl
- jq
- AWS CLI
- Docker
- kubectl
- Terraform
- Jenkins
- ShellCheck
- Security
- Idempotency
- Production script design

---

# 99. Recommended Practice

Study this theory in three passes.

## Pass 1 — Understand

Read each concept and run the examples manually.

## Pass 2 — Reproduce

Close the document and write the commands yourself.

## Pass 3 — Automate

Turn the concepts into:

```text
Small script
   ↓
DevOps assignment
   ↓
Production-style script
   ↓
CI/CD integration
```

The goal is not to memorize Bash syntax.

The goal is to be able to look at a Linux/DevOps problem and think:

> "I can automate this safely with Bash."
