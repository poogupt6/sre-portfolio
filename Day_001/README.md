# Day 1 Log: Linux Streams & Core SRE Foundations

Today I mastered the fundamentals of low-level Linux I/O stream routing and established a secure cloud cost perimeter on AWS.

## 💻 Linux Commands Executed & Verified

### 1. Channeling Standard Output (stdout)
* **Concept:** Redirecting a successful process execution stream (File Descriptor 1) from displaying in the terminal to writing cleanly into a storage file.
* **Command Used:**
```bash
echo "SRE Sensei Day 1" > success.log


2. Isolate Application Errors (stderr)
Concept: Forcing the system to intercept error codes (File Descriptor 2) caused by operational failures so that failures do not corrupt standard output streams.

Command Used:

Bash
cat non_existent_file.txt 2> error.log


3. Stream Merging & Unification
Concept: Combining both stdout (1) and stderr (2) streams together into one unified monitoring log file using the 2>&1 operator.

Command Used:

Bash
ls -la existing_file.txt non_existent_file.txt > combined.log 2>&1



AWS Cloud Infrastructure Track
Task Completed: Deployed a global cost monitoring firewall.

Action: Configured a $1.00 monthly Zero-Spend budget via AWS Budgets with instant Amazon CloudWatch SNS alarms routed to my personal inbox to enforce strict financial guardrails.



ersion Control Track
Task Completed: Local branch isolation verification.

Command Sequence:

Bash
git init
git checkout -b feature/experimental-pipeline

---

### Step 4: Commit and Push to Generate Your Merge Request (MR) Link
Now that your notes are saved in your code directory, run these Git operational commands to push the structured workspace up to GitHub:

```bash
# Stage the new Day_001 folder and your markdown documentation
git add Day_001/

# Commit the changes with a clean, professional commit message
git commit -m "docs: compile Day 1 Linux stream documentation and workspace logs"

# Push the updated branch up to your cloud repository
git push origin feature/experimental-pipeline
