# Bash Scripting — DevOps Cheat Sheet

## Script
```bash
#!/usr/bin/env bash
set -Eeuo pipefail
```

## Variables
```bash
name="value"
echo "$name"
export APP_ENV=prod
unset APP_ENV
```

## Special Variables
```text
$0  script name
$1  first argument
$#  argument count
"$@" all arguments separately
$?  last exit status
$$  current shell PID
$!  last background PID
```

## Input
```bash
read -r -p "Name: " name
printf '%s\n' "$name"
```

## Conditions
```bash
if [[ -f "$file" ]]; then
    echo "File"
fi
```

## File Tests
```text
-e exists
-f regular file
-d directory
-r readable
-w writable
-x executable
-s non-empty
-L symlink
```

## Strings
```bash
${#var}
${var^^}
${var,,}
${var/old/new}
${var//old/new}
${var#prefix}
${var%suffix}
${var:-default}
${var:?required}
```

## Arithmetic
```bash
sum=$((a+b))
((a++))
((a > b))
```

## Loops
```bash
for x in "${arr[@]}"; do echo "$x"; done
```

```bash
while IFS= read -r line; do echo "$line"; done < file
```

## Functions
```bash
log() {
    local message="$1"
    printf '%s\n' "$message"
}
```

## Case
```bash
case "$1" in
  start) ... ;;
  stop) ... ;;
  *) exit 1 ;;
esac
```

## Pipes
```bash
ps aux | grep nginx
```

## Redirection
```bash
> file
>> file
2> error.log
2>&1
&> all.log
>/dev/null 2>&1
```

## Search/Text
```bash
grep -n "ERROR" app.log
sed 's/old/new/g' file
awk -F: '{print $1}' /etc/passwd
find . -type f -name "*.log"
sort file | uniq -c
cut -d: -f1 /etc/passwd
tr 'a-z' 'A-Z'
```

## Processes
```bash
ps aux
pgrep nginx
kill PID
jobs
fg
bg
command &
wait
```

## Debugging
```bash
bash -n script.sh
bash -x script.sh
set -x
set +x
shellcheck script.sh
```

## Temporary Files
```bash
tmp=$(mktemp)
tmp_dir=$(mktemp -d)
trap 'rm -rf "$tmp_dir"' EXIT
```

## Lock
```bash
exec 9>/tmp/script.lock
flock -n 9 || exit 1
```

## Logging
```bash
log() {
    printf '[%s] %s\n' "$(date '+%F %T')" "$*"
}
```

## Error
```bash
die() {
    log "ERROR: $*"
    exit 1
}
```

## Dependency Check
```bash
command -v kubectl >/dev/null 2>&1 || die "kubectl required"
```

## Health Check
```bash
curl -fsS --max-time 10 "$URL" >/dev/null
```

## AWS
```bash
aws sts get-caller-identity
aws ec2 describe-instances
```

## Docker
```bash
docker build -t app:latest .
docker run -d --name app -p 8080:80 app:latest
docker ps
docker logs app
```

## Kubernetes
```bash
kubectl get nodes
kubectl get pods -A
kubectl apply -f app.yaml
kubectl rollout status deployment/app
kubectl rollout undo deployment/app
```

## Terraform
```bash
terraform fmt -check -recursive
terraform init
terraform validate
terraform plan
```

## SSH
```bash
ssh user@host "hostname"
scp file user@host:/tmp/
rsync -av ./app/ user@host:/opt/app/
```

## Cron
```text
*/5 * * * * /opt/scripts/check.sh
0 2 * * * /opt/scripts/backup.sh
```

## Safety Rules
1. Quote variables: `"$var"`
2. Never hardcode secrets.
3. Avoid `eval`.
4. Validate paths before `rm -rf`.
5. Use `mktemp`.
6. Use least privilege.
7. Use explicit error handling.
8. Test failure paths.
9. Run ShellCheck.
10. Prefer idempotent automation.
