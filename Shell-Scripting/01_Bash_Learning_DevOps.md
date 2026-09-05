# Bash Scripting for DevOps — Learning Guide

## Goal
Learn Bash from scratch to advanced level with a DevOps focus.

## Module 1 — Bash Fundamentals
- What is Bash?
- Shell vs Bash
- Shebang: `#!/usr/bin/env bash`
- Running scripts
- `chmod +x`
- Comments
- `bash script.sh` vs `./script.sh`

```bash
#!/usr/bin/env bash
echo "Hello DevOps"
```

## Module 2 — Variables
```bash
name="Akanksha"
echo "$name"

APP_ENV="dev"
export APP_ENV
```

Important:
- No spaces around `=`
- Quote variable expansions: `"$var"`
- Variables are case-sensitive

Special variables:
`$0`, `$1`, `$#`, `"$@"`, `"$?"`, `$$`, `$!`

## Module 3 — Input and Output
```bash
read -r -p "Enter name: " name
printf 'Hello %s\n' "$name"
```

Redirection:
```bash
command > output.log
command >> output.log
command 2> error.log
command > output.log 2>&1
command >/dev/null 2>&1
```

## Module 4 — Conditions
Prefer Bash `[[ ]]`.

```bash
if [[ -f "$file" ]]; then
    echo "File exists"
else
    echo "Missing"
fi
```

String:
```bash
[[ "$a" == "$b" ]]
```

Numeric:
```bash
(( a > b ))
```

File tests:
`-e`, `-f`, `-d`, `-r`, `-w`, `-x`, `-s`, `-L`

## Module 5 — case
```bash
case "$action" in
  start) echo "Starting" ;;
  stop) echo "Stopping" ;;
  restart) echo "Restarting" ;;
  *) echo "Usage: $0 {start|stop|restart}"; exit 1 ;;
esac
```

## Module 6 — Loops
```bash
for server in web1 web2 web3; do
    echo "$server"
done
```

```bash
while IFS= read -r line; do
    echo "$line"
done < input.txt
```

```bash
for ((i=1; i<=5; i++)); do
    echo "$i"
done
```

## Module 7 — Functions
```bash
log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}

check_service() {
    systemctl is-active --quiet "$1"
}
```

Use `local` for function variables.

## Module 8 — Arguments
```bash
echo "Script: $0"
echo "First argument: $1"
echo "Arguments: $#"
```

Safely iterate:
```bash
for arg in "$@"; do
    echo "$arg"
done
```

## Module 9 — Arrays
```bash
servers=("web1" "web2" "web3")

for server in "${servers[@]}"; do
    echo "$server"
done
```

## Module 10 — Strings
```bash
text="Hello World"
echo "${#text}"
echo "${text^^}"
echo "${text,,}"
echo "${text/World/Linux}"
echo "${text//o/0}"
```

## Module 11 — Arithmetic
```bash
a=10
b=20
sum=$((a+b))
((a++))
```

## Module 12 — Command Substitution
```bash
hostname_value=$(hostname)
today=$(date '+%F')
```

Prefer `$()` over backticks.

## Module 13 — Exit Codes
`0` normally means success; non-zero means failure.

```bash
command
rc=$?
echo "$rc"
```

## Module 14 — grep/sed/awk
```bash
grep -n "ERROR" app.log
grep -E "ERROR|WARN" app.log
sed 's/old/new/g' file.txt
awk -F: '{print $1}' /etc/passwd
```

## Module 15 — find/xargs
```bash
find /var/log -type f -name "*.log"
find . -type f -name "*.log" -exec gzip {} \;
find . -type f -name "*.log" -print0 | xargs -0 gzip
```

## Module 16 — Processes
```bash
ps aux
pgrep nginx
kill PID
jobs
fg
bg
```

## Module 17 — trap
```bash
cleanup() {
    rm -rf "$tmp_dir"
}
trap cleanup EXIT
```

## Module 18 — getopts
Use for professional CLI options:
```bash
./deploy.sh -e prod -v
```

## Module 19 — Strict Mode
Common production baseline:
```bash
set -Eeuo pipefail
```

Understand `-e`, `-u`, `-E`, and `pipefail`; do not treat `set -e` as a substitute for explicit error handling.

## Module 20 — Temporary Files and Locks
```bash
tmp_dir=$(mktemp -d)
```

With `flock`:
```bash
exec 9>/tmp/my-script.lock
flock -n 9 || exit 1
```

## Module 21 — Cron
```text
0 2 * * * /opt/scripts/backup.sh
```

Use absolute paths and redirect logs.

## Module 22 — SSH Automation
```bash
ssh user@server "hostname"
scp file.txt user@server:/tmp/
rsync -av ./app/ user@server:/opt/app/
```

## Module 23 — APIs and JSON
```bash
curl -fsS https://example.com/health
curl -s https://example.com/api | jq .
```

## Module 24 — DevOps CLI Automation

### AWS
```bash
aws sts get-caller-identity
aws ec2 describe-instances
```

### Docker
```bash
docker build -t myapp:latest .
docker run -d --name myapp -p 8080:80 myapp:latest
```

### Kubernetes
```bash
kubectl get pods -A
kubectl apply -f deployment.yaml
kubectl rollout status deployment/myapp
```

### Terraform
```bash
terraform fmt -check -recursive
terraform validate
terraform plan
```

### Jenkins
Use Bash steps for build, test, packaging, image creation, deployment and health checks.

## Module 25 — Production Script Template
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
    log "Started"
    # Main logic
    log "Completed"
}

main "$@"
```

## Module 26 — Security
- Quote variables.
- Never hardcode secrets.
- Avoid `eval`.
- Validate destructive paths.
- Use `mktemp`.
- Use least privilege.
- Prefer IAM roles/secret stores/CI secret mechanisms.
- Be careful with `rm -rf`, `sudo`, `terraform destroy`, and production deployments.

## Learning Sequence
1. Linux commands
2. Variables
3. Conditions
4. Loops
5. Functions
6. Arguments
7. Files
8. grep/sed/awk
9. find/xargs
10. Processes
11. Arrays
12. getopts
13. trap
14. Error handling
15. Strict mode
16. Logging/debugging
17. Cron/SSH
18. AWS/Docker/Kubernetes/Terraform/Jenkins automation
19. Production projects
