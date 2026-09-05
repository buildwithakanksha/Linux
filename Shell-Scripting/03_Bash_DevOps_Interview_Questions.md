# Bash Scripting — DevOps Interview Questions & Answers

## Basic

### 1. What is Bash?
Bash is a Unix shell and scripting language used to execute commands and automate Linux tasks.

### 2. What is a shell?
A shell is a command interpreter that provides an interface between users/scripts and the operating system.

### 3. Bash vs shell?
Shell is the general category; Bash is one specific shell.

### 4. What is a shebang?
It specifies the interpreter used to execute a script.
```bash
#!/usr/bin/env bash
```

### 5. How do you make a script executable?
```bash
chmod +x script.sh
```

### 6. `./script.sh` vs `bash script.sh`?
`./script.sh` uses the script's shebang and needs executable permission. `bash script.sh` explicitly invokes Bash.

### 7. What is `$?`?
Exit status of the most recently executed foreground command.

### 8. What is `$#`?
Number of positional arguments.

### 9. What is `$0`?
Usually the script/command name.

### 10. `$@` vs `$*`?
With correct quoting, `"$@"` preserves each positional argument separately; `"$*"` expands them as one word joined by the first character of `IFS`.

### 11. What does `mkdir -p` do?
Creates missing parent directories and does not fail merely because the target directory already exists.

### 12. `>` vs `>>`?
`>` truncates/overwrites; `>>` appends.

### 13. What does `2>&1` mean?
Redirect stderr to the same destination as stdout.

### 14. What is command substitution?
Capturing command output using `$(command)`.

### 15. Why quote variables?
To prevent unwanted word splitting and pathname expansion.

## Intermediate

### 16. How do you read a file safely line by line?
```bash
while IFS= read -r line; do
    ...
done < file
```

### 17. How do you loop over arguments safely?
```bash
for arg in "$@"; do
    echo "$arg"
done
```

### 18. What is `[[ ]]`?
Bash's enhanced conditional syntax. It supports safer string matching, pattern matching and regex compared with traditional test syntax.

### 19. How do you compare numbers?
```bash
(( a > b ))
```

### 20. How do you compare strings?
```bash
[[ "$a" == "$b" ]]
```

### 21. What is a function?
A reusable block of shell commands.
```bash
hello() {
    echo "Hello"
}
```

### 22. Why use `local`?
To keep function variables local instead of accidentally modifying global variables.

### 23. What is an array?
A variable containing multiple values.
```bash
servers=("web1" "web2")
```

### 24. What is `case` used for?
Handling multiple possible values or patterns cleanly.

### 25. What is `trap`?
It runs specified commands when signals or shell events occur.

### 26. What is `getopts`?
A Bash builtin used to parse short command-line options.

### 27. What is `set -e`?
Enables `errexit`, causing Bash to exit on many unhandled command failures, subject to Bash's contextual rules.

### 28. What is `set -u`?
Treats expansion of unset variables as an error.

### 29. What is `pipefail`?
Makes a pipeline fail when a command within it fails rather than relying only on the final command's status.

### 30. What is `set -x`?
Prints commands as Bash executes them; useful for debugging.

## Advanced

### 31. What is strict mode?
A common baseline:
```bash
set -Eeuo pipefail
```
It combines error handling options, but scripts still need explicit checks for important operations.

### 32. Why use `mktemp`?
To create temporary files/directories safely with unpredictable names.

### 33. Why use `flock`?
To prevent concurrent execution of a critical script or section.

### 34. Why is `eval` dangerous?
It executes its argument as shell code, allowing data to become commands. Never use it with untrusted input.

### 35. How do you capture a return code?
```bash
command
rc=$?
```

### 36. `return` vs `exit`?
`return` leaves a function (or sourced script context); `exit` terminates the shell/script.

### 37. What is a subshell?
A child shell environment created by constructs such as `( commands )`.

### 38. `{}` vs `()`?
`{ ...; }` executes a command group in the current shell; `( ... )` executes in a subshell.

### 39. How do you debug Bash?
```bash
bash -n script.sh
bash -x script.sh
shellcheck script.sh
```

### 40. How do you create production-quality Bash?
Use clear structure, strict error handling, input validation, quoting, logging, cleanup, safe temporary files, explicit exit codes, tests and ShellCheck.

## DevOps Scenarios

### 41. Disk is 95%. What would your Bash script do?
Check `df`, identify large files with `find`/`du`, log the event, alert, and only perform approved cleanup actions.

### 42. Nginx is down. How would you automate recovery?
Use `systemctl is-active --quiet nginx`; if down, restart it, verify status, log the action, and return non-zero if recovery fails.

### 43. How would you check application health?
Use `curl -fsS --max-time 10 URL` and evaluate the exit status.

### 44. How would you automate Docker deployment?
Validate Docker, build/tag the image, run tests, push to registry, deploy, health-check and clean up/rollback on failure.

### 45. How would you automate Kubernetes deployment?
Run validation, `kubectl apply`, `kubectl rollout status`, health checks, and rollback with `kubectl rollout undo` when an approved rollback condition occurs.

### 46. How would Bash be used in Jenkins?
As pipeline shell steps for testing, building, packaging, image creation, cloud CLI calls and deployment.

### 47. How do you avoid secrets in Jenkins Bash scripts?
Use Jenkins credentials/secret mechanisms and inject them only where needed; never hardcode them in source.

### 48. How do you make a deployment script idempotent?
Check the current state before changing it, create resources only when missing, use declarative tools where possible, and make repeated runs produce the same desired state.

### 49. How do you prevent two deployment scripts running simultaneously?
Use a lock such as `flock`.

### 50. What is a good Bash interview project?
A production-style health/deployment script combining validation, logging, `getopts`, strict mode, traps, Docker/Kubernetes/AWS integration, health checks and rollback.

## Rapid-Fire Topics to Revise
- `$?`, `$#`, `$@`, `$*`, `$0`
- `"$@"` quoting
- `[[ ]]`
- `case`
- `for`, `while`, `until`
- Functions and `local`
- Arrays
- `grep`, `sed`, `awk`
- `find`, `xargs`
- `cut`, `sort`, `uniq`, `tr`
- Pipes/redirection
- `trap`
- `getopts`
- `set -Eeuo pipefail`
- `mktemp`
- `flock`
- cron
- SSH
- curl/jq
- AWS CLI
- Docker
- kubectl
- Terraform
- Jenkins
- ShellCheck
