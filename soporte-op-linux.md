# Linux Skills Checklist for L2 Production Support & Operational Continuity

# Chaining Linux Commands for Diagnostics and Automation

[cite_start]Chaining Linux commands via pipes (`|`) or logical operators (`&&`, `;`) is one of the most powerful skills for automating daily tasks, troubleshooting in production environments, and performing system diagnostics[cite: 12].

[cite_start]Expanding on your pattern (`du -hd1 | sort -hr`), here are some highly useful and commonly chained Linux commands categorized by real-world operations[cite: 12]:

### 1. Advanced Storage & Diagnostics (Finding the Culprit)

[cite_start]When a server throws an out-of-space or out-of-inodes alert, you need to find exact files quickly[cite: 12].

* **Find the top 10 largest files/folders in a specific directory:**
  ```bash
  du -a /var/log/ | sort -n -r | head -n 10
  ```
  * **How it works:** `du -a` lists sizes for all files/folders; `sort -n -r` sorts numerically in reverse; [cite_start]`head -n 10` restricts the output to just the highest 10[cite: 12].

* **Find and safely remove empty or leftover application logs:**
  ```bash
  find /tmp -type f -name "*.log" -size 0 -exec rm {} \;
  ```
  * [cite_start]**How it works:** `find` queries the `/tmp` directory specifically for files (`-type f`) named `*.log` with exactly 0 bytes, passing them natively to `rm` for safe automated disposal[cite: 12].

### 2. Log Analysis & Triage (Hunting for Errors)

[cite_start]When troubleshooting service failures or debugging APIs, parsing huge logs line-by-line is counterproductive[cite: 12].

* **Count occurrences of specific HTTP Error Codes in an access log:**
  ```bash
  grep "HTTP/1.1\" 500" /var/log/nginx/access.log | wc -l
  ```
  * [cite_start]**How it works:** `grep` filters out lines containing the structural 500 server error string, and `wc -l` counts the lines to immediately give you an incident metric[cite: 12].

* **Extract unique IP addresses attacking or hitting an application:**
  ```bash
  awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -n 20
  ```
  * **How it works:** `awk` extracts the very first column (usually the IP address); [cite_start]`sort` prepares the data for `uniq -c` (which merges identical lines and counts repetitions); the second `sort -nr` organizes the IPs by the highest hit rate, and `head` trims to the top 20[cite: 12]. [cite_start]Very helpful for spotting a sudden DDoS or scraping attempt[cite: 12].

* **Isolate and inspect exact error stack traces live:**
  ```bash
  tail -f /var/log/syslog | grep -i "error"
  ```
  * [cite_start]**How it works:** `tail -f` streams live log updates, and piping to `grep -i "error"` filters the output in real-time to show only lines containing errors (case-insensitive)[cite: 12].


## 🟢 Junior L2 Support (Foundations & Reactive Troubleshooting)
*Focus: Monitoring, basic diagnostics, execution of standard procedures, and simple containment.*

- [ ] **File System & Storage Operations**:
  - Check disk space availability and file system health (`df -h`, `du -sh`).
  - Search, filter, and inspect files (`find`, `grep`, `less`, `tail -f`, `head`).
  - Manage basic file permissions and ownership (`chmod`, `chown`).
- [ ] **Basic Process & Resource Monitoring**:
  - Identify running processes and overall CPU/Memory health (`top`, `htop`, `ps aux`).
  - Terminate hung or unresponsive applications cleanly (`kill`, `kill -9`).
- [ ] **Basic Network Validation**:
  - Verify local interface configuration and basic connectivity (`ping`, `ifconfig`, `ip a`).
  - Check domain name resolution locally (`nslookup`, testing with `/etc/hosts`).
- [ ] **Access & Secure Operations**:
  - Connect safely via SSH and handle basic key authorization (`ssh`, `ssh-copy-id`).
  - Securely transfer files or updates across environments (`rsync`, `scp`).
- [ ] **Standard Container Execution**:
  - Inspect basic status of containers and basic runtimes (`docker ps`, `docker logs -f`).
  - Perform environment cleanups safely (`docker prune`).

---

## 🔵 Mid L2 Support (Advanced Diagnostics & Infrastructure Maintenance)
*Focus: Root Cause Analysis (RCA), automation of repetitive tasks, and service runtime configuration.*

- [ ] **Advanced Log Analysis & Text Processing**:
  - Isolate multi-line stack traces or errors over specific time windows using text streaming (`awk`, `sed`, `grep -E`, `xargs`).
  - Analyze native OS audit logs and journal systems (`journalctl -u`, `dmesg`).
- [ ] **Service & Process Automation Management (Systemd)**:
  - Create, modify, and troubleshoot custom unit scripts (`/etc/systemd/system/`).
  - Control and audit runtime parameters of background daemons (`systemctl status/restart/enable`).
- [ ] **Scripting & Preventative Automation**:
  - Write robust scripts (Bash/Python) to automate sanity checks, rotate heavy database or application files, or trigger preventative maintenance workflows.
  - Implement dynamic scheduling for background cron tasks (`crontab`).
- [ ] **Advanced Network & Port Validation**:
  - Troubleshoot blocked connections, deadlocks, or routing mismatches (`netstat`, `ss`, `lsof -i`, `curl -Iv`).
  - Inspect basic security firewall mechanisms (`iptables`, `ufw`).
- [ ] **Container & Environment Orchestration**:
  - Troubleshoot microservices topologies configured via `docker-compose.yml`.
  - Validate container networking, volumes storage constraints, and status restarts in active environments.

---

## 🔴 Senior L2 / SRE Support (System Performance, Architecture & Prevention)
*Focus: Bottleneck identification, cloud integration, orchestration debugging, and building automated guardrails.*

- [ ] **Deep Storage & I/O Performance Tuning**:
  - Diagnose underlying disk bottleneck or slow queries degrading databases (`iostat`, `iotop`, `vmstat`).
  - Analyze kernel structures or memory traps impacting backend runtimes.
- [ ] **Kubernetes Core Troubleshooting**:
  - Isolate microservice deployment errors and track failures across multi-pod environments (`kubectl get pods`, `kubectl logs -f`, `kubectl exec -it`, `kubectl describe`).
  - Inspect network and access failures within the cluster mesh.
- [ ] **Observability & Log Aggregation Platforms**:
  - Correlate distributed telemetry metrics and logs from distributed Linux nodes using central logging pipelines (Splunk, ELK Stack / Kibana, GCP Cloud Logging, or AWS CloudWatch).
- [ ] **Infrastructure-as-Code (IaC) Readability**:
  - Interpret cloud target topologies defined in declarative languages (Terraform, AWS CDK) to quickly understand logical structures, target configurations, or access permissions (IAM policies) before service manipulation.
- [ ] **Incident Response Governance & Prevention**:
  - Author and refine rigorous technical Runbooks / Playbooks to abstract system anomalies into repeatable, zero-risk manual steps.
  - Design and configure granular threshold alerts (Prometheus/Grafana) to address application degradation before it triggers a production outage.


