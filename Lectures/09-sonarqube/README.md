# SonarQube Tutorial for DevOps | Introduction to DevSecOps

## Video reference for this lecture is the following:


---
## ⭐ Support the Project  
If this **repository** helps you, give it a ⭐ to show your support and help others discover it! 

---

# **Table of Contents**

* [Introduction](#introduction)
* [Pre-requisites for this lecture](#pre-requisites-for-this-lecture)
* [What is DevSecOps and why it is important](#what-is-devsecops-and-why-it-is-important)
* [Key DevSecOps terms and concepts](#key-devsecops-terms-and-concepts)
* [What is SonarQube and where it fits in CICD](#what-is-sonarqube-and-where-it-fits-in-cicd)
* [SonarQube issue types: bugs, smells, duplication, vulnerabilities, security hotspots](#sonarqube-issue-types-bugs-smells-duplication-vulnerabilities-security-hotspots)
* [End-to-End Production DevSecOps Flow (Java + Maven)](#end-to-end-production-devsecops-flow-java--maven)
* [**Lab 1:** Install SonarQube on EC2](#lab-1-install-sonarqube-on-ec2)
* [Quality Profiles & Quality Gates](#quality-profiles-and-quality-gates)
* [SonarQube architecture: server and scanner](#sonarqube-architecture-server-and-scanner)
* [**Lab 2:** Install Jenkins on EC2 (With Maven & Docker)](#lab-2-install-jenkins-on-ec2-with-maven--docker)
* [SonarQube with Jenkins: Integration Steps](#sonarqube-with-jenkins-integration-steps)
* [**Demo:** SonarQube with Docker, Maven & Jenkins](#demo-sonarqube-with-docker-maven--jenkins)
* [Conclusion](#conclusion)
* [References](#references)

---

# **Introduction**

Modern software delivery is no longer just about shipping features quickly — it’s about shipping them **quickly, reliably, and securely**.
This lecture walks you through a **complete DevSecOps workflow**, combining **Maven, Jenkins, Docker, and SonarQube** to enforce code quality and security directly inside your CI pipeline.

You’ll learn how security shifts **left** in the SDLC, how SonarQube performs **SAST, code quality checks, and coverage analysis**, and how a real CI job can be wired to **fail automatically** when code does not meet your security or quality thresholds.

By the end, you will understand not just **how** to integrate SonarQube, but more importantly **why** DevSecOps pipelines are designed this way in modern engineering teams.

---

# Pre-requisites for this lecture

To follow the DevSecOps and SonarQube flow end-to-end, these three sessions will really help, especially because we’ll **conclude this lecture with a CI/CD pipeline that uses both Maven and SonarQube**, understanding these foundations makes that final pipeline much easier to follow.


1. **Modern SDLC Explained | Build Automation & Real-World Insights**

   * YouTube Video: [Modern SDLC Explained](https://youtu.be/imEHsgvJbYo)
   * GitHub Notes: [ Modern SDLC](https://github.com/CloudWithVarJosh/Jenkins-Basics-To-Production/tree/main/Day%2001)
     **Why this pre-requisite:** Gives you the big picture of how modern SDLC, builds and environments are structured, so you know *where* DevSecOps controls will eventually sit.


2. **CI/CD Explained | How Pipelines Work & Branching Strategies**

   * YouTube Video: [CI/CD Explained](https://www.youtube.com/watch?v=szPE1NKc614&ab_channel=CloudWithVarJosh)
   * GitHub Notes: [CI/CD & Branching Strategies](https://github.com/CloudWithVarJosh/Jenkins-Basics-To-Production/tree/main/Day%2002)
     **Why this pre-requisite:** Explains pipeline stages, promotions and branching, so it’s clear *at which stage* in CI/CD we plug in SAST, DAST and SonarQube quality gates.

> Both the above videos are from my **Jenkins: Basics to Production** course, but the concepts are **tool-agnostic** and give you a solid, real-world view of how production-grade CI/CD implementations are designed.

3. **Maven Tutorial for DevOps | Maven Beginner Tutorial**

   * YouTube Video: [Maven Tutorial for DevOps](https://www.youtube.com/watch?v=3OKc5y_3wMM&ab_channel=CloudWithVarJosh)
   * GitHub Notes: [Maven Lecture Notes](https://github.com/CloudWithVarJosh/YouTube-Standalone-Lectures/tree/main/Lectures/08-maven)
     **Why this pre-requisite:** In this lecture SonarQube is wired through **Maven phases**, so knowing how Maven builds, tests and packages your app makes it easier to understand exactly *when* SAST (Sonar) runs.


---

## What is DevSecOps and why it is important

### Why DevSecOps

* **Late security checks create failures at release time.** Traditional models rely on a penetration test or manual review near go-live, which triggers last-minute defects, costly rework, and friction between security and engineering.
* **Manual, non-repeatable checks hurt speed and compliance.** Many security checks like SAST, SCA, and DAST were run manually or in silos. Even when automated, they often ran out of sequence and without a single gate. This made runs slow, hard to audit, and inconsistent across teams, so proving compliance was difficult. What we want is a pipeline that orchestrates these checks in the required order and records auditable results.
* **Hidden risk accumulates in code and dependencies.** Without continuous checks, issues in custom code and third-party libraries slip through until production, increasing incident likelihood and remediation cost.

> **Penetration testing:** a simulated cyber attack on your app, network, or infra to find weaknesses before real attackers do. Testers probe like an attacker, attempt exploitation, then report findings for remediation.

### What is DevSecOps

DevSecOps stands for **Development, Security, and Operations**. It is a software engineering approach that integrates security practices into **every phase of the Software Development Life Cycle (SDLC)** and the **DevOps lifecycle**. The goal is to make security **continuous, automated, and shared** across teams, not a separate phase at the end.

> **SDLC:** The end-to-end product process (requirements, design, build, test, deploy, maintain); **defines what and why**.
**DevOps lifecycle:** The continuous delivery loop inside SDLC (build, test, release, deploy, operate, monitor, feedback); **focuses on how** to deliver and operate quickly and reliably.



> DevOps, DevSecOps, and the SDLC are **not tools** or products you can buy. They are **methodologies and practices** you apply while designing, building, testing, deploying, and operating software. Tools help implement parts of these methods, but the real value comes from **people, process, and principles**. Understand the **why and how** first, then pick tools that support your process, not the other way around.


**How DevSecOps fits into DevOps:**

* DevOps focuses on **speed, collaboration, and reliability**; automating build, test, and deployment to deliver software faster.
* DevSecOps **extends DevOps** by embedding security into the same workflows, ensuring every build and release is automatically checked for **vulnerabilities, compliance, and code quality**.
* You can think of it as **DevOps + Security**; same automation principles, but with **secure-by-design** guardrails built into the pipeline.

**In short:** DevOps delivers software quickly and reliably; DevSecOps delivers it **quickly, reliably, and securely.**

**Core DevSecOps practices:**

* **Security integrated across planning, coding, build, testing, deploy.** Not a separate late activity, security controls are part of everyday delivery work.
* **Continuous, automated checks inside CI/CD.** Every pipeline run is evaluated for quality and security using repeatable SAST, coverage, and dependency scans, not just major releases.
* **Clear ownership across developers, security, and operations.** Policies define who fixes what by when; gates and metrics make accountability visible.


---

## Key DevSecOps terms and concepts

**Before we dive into tools,** map the **key DevSecOps check types** you will use in pipelines: **SAST** for code security, **DAST** for runtime security, **SCA** for dependency risk, and **Code Quality checks** for maintainability. Know **what each does** and **where it runs** so you can place them in the right stages and interpret results correctly.

> **SAST, DAST, and SCA are not tools**; they are **methodologies** and categories of checks. Specific tools (for example SonarQube, ZAP, Snyk) **implement** these methods. Keep the distinction clear: **method first**, then choose tools to perform it in your pipeline.



**SAST (Static Application Security Testing)**

* SAST means testing an application for security issues *from the source code, bytecode, or even compiled binaries / machine code*, **without running the application**; these tools scan code and configuration to find vulnerabilities such as **SQL injection, hard coded credentials, insecure APIs** and other dangerous patterns.
* SAST tools **integrate with IDEs and CI pipelines** (for example VS Code, IntelliJ, Jenkins) so developers get instant feedback while they code and every build is automatically checked for security issues.
* **Popular tools:** SonarQube, Checkmarx, Fortify Static Code Analyzer, Snyk Code, GitHub Advanced Security (CodeQL), Semgrep.

**DAST (Dynamic Application Security Testing)**

* DAST means testing an application *while it is running*, usually from the outside **like an attacker would**, by sending real HTTP requests with different payloads and patterns to see whether they can trigger **injection, XSS, authentication issues** or other runtime security problems.
* DAST tools focus on the **running application and its endpoints** rather than the source code, and are often used for penetration testing and dynamic pre production security assessments.
* **Popular tools:** OWASP ZAP, Burp Suite, Netsparker / Invicti, Acunetix, AppSpider.


**Code quality checks**

* Code quality checks focus on **correctness and maintainability** rather than pure security, flagging long or complex methods, duplicated logic, poor naming, dead code and other **code smells** that make systems harder to understand, test and evolve.
* They help keep the codebase clean so that future changes are safer and less error prone.
* **Popular tools:** SonarQube, SonarLint, ESLint (JavaScript), Pylint and Flake8 (Python), PMD and Checkstyle (Java), RuboCop (Ruby).


**Dependency scanning (SCA – Software Composition Analysis)**

* Dependency scanning analyzes the **third party libraries and frameworks** your application uses and compares their versions against vulnerability and license databases to detect **known vulnerable or non compliant components**.
* It is especially important because a large part of modern applications is built from open source and vendor packages, not just your own code.
* **Popular tools:** Snyk, OWASP Dependency-Check, GitHub Dependabot, GitLab Dependency Scanning, WhiteSource or Mend, JFrog Xray, Aqua Trivy, Grype.

  > SAST only checks for vulnerabilities in **your own code and configuration**; it does not detect vulnerabilities inside the **third party dependencies** you declare.
  Think of it as:
  > SAST = vulnerabilities in **your code**
  > SCA = vulnerabilities in **your dependencies**

**SBOM (Software Bill of Materials)**

* An SBOM is a **machine readable inventory** of all components, versions and licenses in your application, container image and base OS layers.
* Generate SBOMs in CI and **attach them to artifacts** so you can rescan builds later when new CVEs are published and maintain supply chain transparency.
* **Popular tools and formats:** Syft, CycloneDX Maven plugin, SPDX tools; formats **SPDX** and **CycloneDX**.

---
**SCA vs SBOM — clarified**

* **SCA (Software Composition Analysis):** Actively *analyzes* an application’s dependencies against vulnerability and license databases, enforces allow/deny policies, and raises alerts or blocks builds when risks are found.
* **SBOM (Software Bill of Materials):** A *machine-readable inventory* of components, versions and licenses attached to an artifact — it does **not** scan for issues itself, but enables traceability, reproducible audits, post-release rescans and fast impact analysis when new CVEs are published.

One-line takeaway:
**SCA = active risk detection and policy enforcement. SBOM = authoritative inventory for traceability and rescans.**

---

### **Important notes**

* Many modern platforms perform **multiple DevSecOps functions**. For example, **SonarQube** combines **SAST** and **code quality** checks while ingesting **test coverage reports** from tools like **JaCoCo**. **Snyk** integrates **dependency scanning**, **SAST**, and **container image scanning** in a single platform. Similarly, **GitHub** and **GitLab** include **built-in SAST**, **dependency**, and **container security scanners** within their CI/CD pipelines.

* Combined, **SAST, DAST, code quality, test coverage, dependency scanning, and SBOM** ensure that code is not only **functional** but also **secure, maintainable, and compliant** before promotion through the pipeline. In this lecture, we’ll focus on **SAST, code quality, and coverage**, demonstrating how **SonarQube** enforces these checks in a CI workflow.

* **SBOM complements SCA** by providing a **post-build inventory** of all components and versions used in an artifact. Generate SBOMs during CI for every build or image using formats like **SPDX** or **CycloneDX**. These SBOMs can later be **re-evaluated** against updated vulnerability feeds to identify newly published CVEs without re-scanning the artifacts themselves.

---

## What is SonarQube and where it fits in CI/CD

**SonarQube** is a **comprehensive platform for continuous code inspection, quality governance, and static analysis**. It automatically analyzes source code, bytecode, or binaries on every commit, identifying **bugs, security vulnerabilities, code smells, duplications, and test coverage gaps** long before the code reaches production. In essence, it turns static analysis and code quality management into a **continuous, automated process** that runs across every repository and every pipeline.

When integrated into a **CI/CD pipeline**, SonarQube becomes a **quality and security gate**—a checkpoint every build must pass before packaging, deployment, or release. It shifts defect detection left by embedding SAST and maintainability checks directly into developers’ daily workflow and enforces measurable standards through **quality gates and policies**. For DevOps and platform teams, it provides **central visibility** into the health of codebases, ensuring consistency, compliance, and traceability across projects.

SonarQube’s capabilities span three major pillars that together deliver full-spectrum assurance for both developers and DevSecOps teams:

**1. Code coverage**

* **What it is:** Code coverage is the percentage of your source code that automated tests actually execute. If tests run 80 out of 100 lines, coverage is **80%** — it shows what’s being exercised, not whether tests are good.

* **How teams use it:** Teams usually enforce minimum coverage on **new code** with quality gates. SonarQube does not run tests; it **imports coverage reports** from language-specific tools and fails the gate if the new-code threshold is missed. Example reporters: **JaCoCo (Java)**, **Cobertura (Java)**, **LCOV / Istanbul (JavaScript)**, **coverage.py (Python)**.

* **Compiled vs interpreted (short):**

  * **Compiled (Java, C#):** test runner (JUnit, NUnit) executes tests; a coverage agent (JaCoCo, OpenCover) instruments bytecode during the run and writes reports.
  * **Interpreted (JS, Python):** test runner (Jest, pytest) executes tests; a reporter (Istanbul, coverage.py) records executed lines and emits reports.

* **Who does what:** Coverage tools do not run tests; test frameworks run them and coverage tools only record what ran, then SonarQube reads those reports.

* **One practical tip:** Use coverage to find untested areas, but pair it with good assertions or mutation testing — high coverage alone is not a guarantee of correct tests.

> Surefire and Failsafe are Maven plugins that run tests in CI: Surefire runs unit tests in the `test` phase, Failsafe runs integration tests in `integration-test`/`verify`.
You still need a test framework (JUnit or TestNG) to write tests; the plugins invoke those libraries to execute test methods.
JaCoCo instruments the JVM while tests run and writes coverage reports (XML/HTML).
SonarQube reads those reports to show coverage and enforce quality gates.



**2. Code security (SAST)**

* SonarQube runs **static application security tests** to flag **vulnerabilities, security hotspots, and security-sensitive bugs**, catching risks early in pull requests or CI rather than in production.
* Findings are categorized by **type and severity** for clear prioritization, helping teams meet security SLAs as part of the pipeline.

**3. Code quality (maintainability and reliability)**

* The code is not well written but still performs its intended function.
* SonarQube detects **code smells & code duplication,** that make systems harder to understand, test, and evolve, even if the code “works” today.
* Treating these as first-class findings helps teams manage **technical debt** with data and keep the codebase healthy over time.

> **Technical debt:** Shortcuts in code or design taken to move fast now that make the code harder and slower to change later. Treat these shortcuts as work to be fixed later, prioritize by impact, and schedule time to pay them down.


Overall, think of SonarQube as a **central, always-on code review assistant for DevOps teams** that runs on every build and every change, with **security (SAST)** and **quality** checks enforced through **quality gates**.


---

## SonarQube issue types: bugs, smells, duplication, vulnerabilities, security hotspots

SonarQube raises *issues* whenever something does not meet your quality or security standards. Different issue types help you understand the **impact** and **urgency** of what needs to be fixed.

**Bug**

* A **bug** is a defect in the code that can cause incorrect behaviour, crashes or unexpected results at runtime, for example a null pointer dereference, wrong `if` condition, resource leak (file or connection not closed) or a concurrency issue.
* Bugs directly affect how the application works, so they usually carry higher severity and should be **fixed early** to avoid production incidents and hard to debug failures.

**Code smell**

* A **code smell** is a sign of poor design, bad coding style or low maintainability, even though the code “works” today; examples include very long methods, deeply nested conditionals, magic numbers and large classes doing too many things.
* Smells do not usually break production immediately, but they make code **harder to understand and change**, increasing the risk of future bugs, so SonarQube highlights them as places where refactoring will pay off.

**Duplication**

* **Duplication** refers to repeated code blocks in multiple places, often copy paste; instead of repeating the same logic, it is better to extract a function or method and reuse it.
* High duplication makes maintenance harder, because every bug fix or change has to be applied in many locations and it is easy to miss one; SonarQube measures duplicated lines so you can **target refactoring** and reduce this risk.

**Vulnerability**

* A **vulnerability** is an exposed part of your code that could be exploited by an attacker, such as insecure handling of user input, hard coded credentials, unsafe deserialization, SQL injection, XSS or weak cryptographic algorithms.
* Vulnerabilities are directly related to **application security** and are treated as high priority findings; SonarQube helps you catch them during development rather than discovering them during a security incident.

**Security hotspot**

* A **security hotspot** is a piece of code that is *potentially sensitive* from a security point of view and **requires human review**; the pattern itself is not always wrong, but it becomes risky if used incorrectly.
* Typical examples include use of cryptographic APIs, manual construction of SQL queries, or configuration that affects authentication and authorization; SonarQube marks these as hotspots so a developer or security engineer can review them and either confirm they are safe or create proper vulnerability issues.

By combining these issue types, SonarQube gives you a complete view of **reliability** (bugs), **maintainability** (code smells and duplication) and **security** (vulnerabilities and hotspots).

---

## **End-to-End Production DevSecOps Flow (Java + Maven)**

This workflow shows how a modern Java application moves from source code to production using DevSecOps practices. The flow is **CI/CD tool agnostic** — the same stages work whether you use **Jenkins, GitHub Actions, GitLab CI, or Bitbucket Pipelines**.
Each phase embeds security, quality, and compliance checks to ensure production-ready software.


#### 1. Checkout private Git repo

* The pipeline pulls source, `pom.xml`, and `Dockerfile` from a **private Git repo**, triggered by a push or a pull request.
* Best practice: protect main branches and run CI on **pull requests**, not direct pushes; require PR checks before merging so pipelines validate changes.


#### 2. Build + Test (CI Phase)

* **JUnit / TestNG** are the test frameworks; **Surefire** runs unit tests and **Failsafe** runs integration tests in the appropriate Maven phases.
* **Run unit tests:** `mvn test` — executes Surefire-bound unit tests during the test phase.
* **Run full build + integration tests:** `mvn verify` — runs package then Failsafe integration tests and verification.
* **Tests run in CI** so code behaviour is validated early; integration tests run against temporary environments or containers when needed.
* **JaCoCo** instruments the JVM during tests and writes a coverage report consumed later by SonarQube.
* **Coverage shows which code** was exercised and helps gates ensure new code is tested.

#### 3. Run SCA & Generate SBOM

* **SCA tools** (Snyk, Dependency-Check) scan dependencies during build to detect known vulnerabilities.
* **This catches risky third-party libraries** early and can create fix PRs or fail the build depending on policy.
* **Generate an SBOM** (CycloneDX / Syft) and persist it with the build artifact.
* **The SBOM is a machine-readable inventory** you can rescan after release when new CVEs are published.

> Note: SCA can be configured to **fail the pipeline**; for example if critical/high severity vulnerabilities are found or if a dependency has a disallowed license. Use policy-driven severity thresholds to control blocking behavior.

#### 4. SonarQube Analysis (static)

* **SonarQube** imports the JaCoCo coverage report, runs SAST rules, and checks code quality and maintainability.
* **Results are visible centrally** so teams can triage security hotspots, bugs, and code smells across repos.
* A **Quality Gate** enforces thresholds; fail the gate stops the pipeline, pass allows packaging to continue.
* **Quality Gates turn policy into automation** so problematic changes are blocked before packaging.

> Note: A failing quality gate (for example low coverage or new critical issues) will cause the scanner to return a non-zero exit and **fail the pipeline** when `-Dsonar.qualitygate.wait=true` or equivalent check is used.



#### 5. Package & Containerize

* The app is **packaged with Maven** and a **Docker image** is built and tagged for registry upload.
* **Packaging produces the deployable artifact** that will be promoted through staging and production.

> Note: if you already ran `mvn verify` in the Build + Test stage, running `mvn package` here will re-run earlier lifecycle phases and slow the pipeline.
> Instead, prefer one of these lightweight options to avoid duplicate work:
>
> * Use the existing artifact from `target/` produced earlier (recommended).
> * Run `mvn package -DskipTests` to produce the JAR without re-running tests.
> * Invoke the jar plugin directly after compile if you only need the archive: `mvn org.apache.maven.plugins:maven-jar-plugin:3.4.2:jar` (ensure classes are already compiled).
Refer this for for info.: https://github.com/CloudWithVarJosh/YouTube-Standalone-Lectures/tree/main/Lectures/08-maven#common-pluginsgoals-each-goal-shows-its-phase

Use the option that best fits your CI job layout so packaging and containerization stay fast and deterministic.


#### 6. Push image + SBOM to registry (ECR)

* **Push both the image and its SBOM** to the registry using ORAS or attestation tooling like Cosign.
* **SBOM travels with the image** so security teams can rescan it later against new CVEs.
* In normal setups, the **image and its SBOM are pushed separately** but stored under the same tag or digest for easy lookup.
* For Maven/JAR builds, teams similarly **upload the JAR and its SBOM together** to Nexus or Artifactory so every artifact has an attached SBOM for later audits.


#### 7. Deploy to Staging

* The **CI/CD orchestrator** (Jenkins, Argo, GitHub Actions, GitLab) deploys the image to staging.
* **Deploying to a realistic environment** exposes integration points so pre-prod tests and targeted dynamic scans can run.
Here is the refined version with a crisp best practice note added:


#### 8. Run DAST & Smoke tests

* Run runtime checks (**OWASP ZAP**, Burp Suite) and lightweight smoke tests (curl, Postman) against deployed endpoints.
* **DAST finds runtime vulnerabilities** and smoke tests verify basic app readiness before promotion.

> **Best practice:** If DAST reports critical or high severity vulnerabilities, the pipeline should **block promotion to production** until the issues are fixed. This is a standard DevSecOps control.


#### 9. Promote to Production

* **Promote only when the Quality Gate and DAST/smoke checks pass**, using a canary then full rollout pattern.
* **Canary rollouts limit blast radius**, letting you observe live behaviour before a full deployment.



#### 10. Continuous Monitoring & SBOM Rescans

* **Continuously monitor production** with observability tools and periodically rescan stored SBOMs against new CVE feeds.
* **This keeps you aware of emerging risks** and enables targeted remediation for specific released images.

> **Production note:** Many teams run **passive or minimal DAST** in production (for example ZAP in *passive mode*) which observes traffic for signs of insecure headers, missing protections or weak configurations **without sending attack payloads**. Active DAST is avoided in prod because it can impact real users, but **passive DAST + WAF + continuous monitoring** adds an extra safety layer.



---

## Lab 1: Install SonarQube on EC2

For the lab, we will keep SonarQube and PostgreSQL on the **same VM**. This is ok for training, and close enough to a small production style deployment on VMs.

### 1. Create the EC2 instance

* AMI: Ubuntu 22.04 LTS
* Instance type: c7i-flex.large (SonarQube likes RAM)
  > **Note:** The instance type **c7i-flex.large** is eligible under the AWS **credits-based free tier**, so you will not incur charges as long as you remain within your credit balance.

* Root volume: 20 GB or more

**Security group (SonarQube VM)**

* Allow SSH (22) from your IP
* Allow HTTP (9000) from your IP and from the Jenkins VM


### 2. Connect to the instance

```bash
ssh -i <your-key>.pem ubuntu@<sonarqube-ec2-public-ip>
```
**Note:** Ensure your SSH private key is readable only by you.
Run:

```bash
chmod 600 ~/.ssh/<your-prvate-key>  
chown $(whoami):$(whoami) ~/.ssh/<your-prvate-key>  
```

**Suggested hostname:** `sonarqube`
This makes the server easy to identify in SSH sessions, monitoring dashboards, and DNS-based service discovery.

```bash
# Set hostname and reload your shell
sudo hostnamectl set-hostname sonarqube
exec bash
```

**Setting the correct timezone**
Set the machine timezone so logs and timestamps are consistent with your locality.
I'll use `Asia/Kolkata`; pick the timezone that matches your location.

```bash
# set timezone to Asia/Kolkata
sudo timedatectl set-timezone Asia/Kolkata

# verify
timedatectl status
# or
date
```

### 3. Install Java 17

SonarQube currently requires Java 17.

```bash
sudo apt-get update
sudo apt-get install -y openjdk-17-jre
java -version
```

You should see Java 17.

### 4. Install PostgreSQL and create DB for SonarQube

In SonarQube, the **database is critical**. It stores:

* All **projects, analyses and measures** (issues, coverage, duplications, ratings, technical debt).
* **Rules, quality profiles, quality gates**, user accounts, tokens and general configuration.

Because this data is so important, in production you will often see enterprises run SonarQube’s database on a **separate, self managed PostgreSQL VM** or use a **managed service such as Amazon RDS for PostgreSQL**, so that performance, backups and high availability can be handled independently from the SonarQube application VM.

For **development and small test setups**, you will sometimes see **H2** being used. H2 is an **embedded, in memory or file based relational database** that is very easy to start but not designed for heavy, multi user production use. It is fine for quick local trials, but for any serious team environment SonarQube recommends **PostgreSQL** instead.


```bash
# Install PostgreSQL and common extensions
sudo apt install -y postgresql postgresql-contrib

# Enable PostgreSQL to start automatically on boot
sudo systemctl enable postgresql

# Start the PostgreSQL service now
sudo systemctl start postgresql
```

**Create SonarQube database and user**

Below are the exact commands to create the database role and the `sonarqube` database, plus a quick connectivity test.
Run the SQL commands as the `postgres` superuser.

```bash
# open the PostgreSQL shell as the postgres superuser
sudo -u postgres psql
```

Inside `psql`, run:

```sql
-- create a login role for SonarQube with a password
CREATE ROLE sonar WITH LOGIN ENCRYPTED PASSWORD 'StrongPasswordHere';
-- Example: CREATE ROLE sonar WITH LOGIN ENCRYPTED PASSWORD 'sonar';

-- create the SonarQube database owned by that role
CREATE DATABASE sonarqube OWNER sonar;

-- grant privileges on the database to the sonar role
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;

-- exit psql
\q
```

Quick verification (run on the VM shell):

```bash
# list roles to confirm the 'sonar' role exists
sudo -u postgres psql -c "\du"

# list databases and owners to confirm the 'sonarqube' DB exists
sudo -u postgres psql -c "\l"

# test connecting exactly as SonarQube will (replace password if needed)
PGPASSWORD='StrongPasswordHere' psql -U sonar -h localhost -d sonarqube -c "\dt"
```

If the last command shows an empty relation list (no tables) and no authentication error, the database and user are configured correctly.
Update `sonar.jdbc.username`, `sonar.jdbc.password`, and `sonar.jdbc.url` in `/opt/sonarqube-current/conf/sonar.properties` to match these values and restart SonarQube.



### 5. Tune kernel settings required by SonarQube

SonarQube uses Elasticsearch internally and needs some kernel tweaks.

```bash
# Increase the maximum number of memory map areas (required by Elasticsearch)
echo 'vm.max_map_count=524288' | sudo tee -a /etc/sysctl.d/99-sonarqube.conf

# Increase the maximum number of file handles
echo 'fs.file-max=131072' | sudo tee -a /etc/sysctl.d/99-sonarqube.conf

# Apply the new sysctl settings
sudo sysctl --system
```

Set ulimits for the `sonar` user by adding to `/etc/security/limits.d/99-sonarqube.conf`:

```bash
# Allow many open files for the sonar user
echo 'sonar   -   nofile   131072' | sudo tee /etc/security/limits.d/99-sonarqube.conf

# Allow many processes for the sonar user
echo 'sonar   -   nproc    8192'   | sudo tee -a /etc/security/limits.d/99-sonarqube.conf
```

> **Note:** These kernel and ulimit tweaks give Elasticsearch (used by SonarQube) enough memory mappings and file descriptors to index code reliably.
> Apply them before starting SonarQube to avoid startup, indexing, or resource-limit failures.


### 6. Create a dedicated sonar user

```bash
sudo useradd -m -d /opt/sonarqube -s /bin/bash sonar
```

### 7. Download and install SonarQube

Reference: https://www.sonarsource.com/products/sonarqube/downloads/

Check the LTS download URL from SonarQube site; as an example:

```bash
# switch to a temporary folder for downloads (keeps /tmp clean and avoids permission issues)
cd /tmp

# download the SonarQube community zip, follow redirects (-L) and write output to sonarqube.zip (-o)
curl -L -o sonarqube.zip "https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.11.0.114957.zip"

# install unzip non-interactively (-y auto-accepts prompts)
sudo apt-get update
sudo apt-get install -y unzip

# extract the downloaded archive into /tmp (unzip sonarqube.zip)
unzip sonarqube.zip

# move the extracted folder to a stable location for SonarQube deployment
sudo mv sonarqube-25.11.0.114957 /opt/sonarqube-current

# change ownership recursively (-R) so the sonar system user owns all files and directories
sudo chown -R sonar:sonar /opt/sonarqube-current
```


You can keep `/opt/sonarqube-current` as a symlink later when upgrading.

> In enterprise-grade implementations you would typically see the **Data Center Edition (DCE)** used because it provides application-level high availability.
**DCE** enables clustered SonarQube: multiple app nodes behind a load balancer, dedicated Elasticsearch search nodes, and a shared external database for resilience, scale and zero-downtime maintenance.


### 8. Configure SonarQube to use PostgreSQL

We are configuring SonarQube to connect to the PostgreSQL database you created.
**PostgreSQL default port is `5432`** — use it in the JDBC URL unless your DB uses a different port.

```bash
# edit the main SonarQube configuration file as the 'sonar' user
# (use your preferred editor: vim/nano)
sudo -u sonar vim /opt/sonarqube-current/conf/sonar.properties
```

Uncomment and set these lines (replace the password/host if needed):

```properties
# DB username for SonarQube (the DB user you created)
sonar.jdbc.username=sonar

# DB password for that user; keep this secret and match your DB setup
sonar.jdbc.password=StrongPasswordHere

# JDBC URL: jdbc:postgresql://<host>:<port>/<database>
# default PostgreSQL port is 5432 — using localhost because DB is on same VM
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
```

Notes and quick checks:

* If PostgreSQL runs on a different host, replace `localhost` with the DB host or IP and ensure the DB accepts remote connections (`listen_addresses` and `pg_hba.conf`).
* After saving, restart SonarQube so it picks up the new DB settings.
* If the SonarQube process cannot connect, check PostgreSQL is running and that the `sonar` user has access to the `sonarqube` database.


### 9. Create a systemd service for SonarQube

**What a systemd unit file is:** a small config that tells the OS how to start, stop, and manage a service, which user runs it, what it depends on, restart policy, and resource limits.

Create the unit file (edit as root):

```bash
sudo vim /etc/systemd/system/sonarqube.service
```

Paste this exact unit file (no inline comments inside the file):

```ini
[Unit]
Description=SonarQube service
After=network.target postgresql.service

[Service]
Type=forking
User=sonar
Group=sonar
ExecStart=/opt/sonarqube-current/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube-current/bin/linux-x86-64/sonar.sh stop
Restart=on-failure
LimitNOFILE=131072
LimitNPROC=8192

[Install]
WantedBy=multi-user.target
```

Reload systemd and start the service:

```bash
# reload systemd to pick up the new unit
sudo systemctl daemon-reload

# enable SonarQube to start on boot
sudo systemctl enable sonarqube

# start SonarQube now
sudo systemctl start sonarqube

# check current status; it should become active (running)
sudo systemctl status sonarqube
```

**Note: unit file directive explanations (keep these out of the unit file)**

* `Description` — human-friendly name for the service.
* `After` — start this service after the listed units are up (network and PostgreSQL here).
* `Type=forking` — the service forks to the background; systemd treats the original process as the starter.
* `User` / `Group` — the system account that runs the service (`sonar`) for least privilege.
* `ExecStart` — full path to the command that starts SonarQube. It must be executable.
* `ExecStop` — full path to the command that stops SonarQube cleanly.
* `Restart=on-failure` — automatically restart the service if it crashes or exits with an error.
* `LimitNOFILE` — raise the open-files limit; Elasticsearch needs many file descriptors.
* `LimitNPROC` — raise the allowed process count for the service user.
* `WantedBy=multi-user.target` — makes the service start during normal system boot.

**Operational tips:**

* If status is not `active (running)`, check live logs: `sudo journalctl -u sonarqube -f`.
* Confirm the `sonar` user owns `/opt/sonarqube-current` and that the `sonar` user has the ulimits set earlier.
* After editing the unit file, always run `sudo systemctl daemon-reload` to apply changes.

**Troubleshooting**
Check the SonarQube logs (look for ERROR / bootstrap failures)

```bash
# show recent sonar logs (general), web and elasticsearch logs
sudo tail -n 200 /opt/sonarqube-current/logs/sonar.log
sudo tail -n 200 /opt/sonarqube-current/logs/web.log
sudo tail -n 200 /opt/sonarqube-current/logs/es.log
```


### 10. Access the SonarQube UI

In your browser:

```text
http://<sonarqube-ec2-public-ip>:9000
```

Default login:

* Username: `admin`
* Password: `admin`

You will be asked to change the password.

From here you can:

* Explore **Quality Profiles**, **Quality Gates**, **Rules**
* Later, create a **project** and a **token** that Jenkins will use
* Configure `SONAR_HOST_URL` in Jenkins as `http://<sonarqube-ec2-public-ip>:9000`

### 11. Production notes for SonarQube

In many production deployments:

* SonarQube runs on **one or more VMs**, with PostgreSQL often on a separate DB server or managed service
* Resources are sized based on number of projects and users, often more CPU and RAM than in lab
* Access is via **HTTPS**, fronted by an ALB or Nginx
* Authentication integrates with **LDAP, SSO or OIDC**
* Backups are planned for both **database** and **configuration**
* Some teams move SonarQube to **Kubernetes**, but the VM pattern like this is still very common

---

## Quality Profiles and Quality Gates

To make analysis meaningful, SonarQube uses three key concepts: **quality profiles**, **quality gates** and **metrics**. Together, they control *what* is checked, *how strict* you are and *how you measure* health over time.

**1. Quality profiles**

* A **quality profile** is the rule set for a particular language; it tells SonarQube which rules to run, which severity to assign and which rules to ignore. You usually start from the default **“Sonar way”** profile, then clone and customize it for your team so that Java, JavaScript, Python and other languages each have a profile that matches your standards.
* Profiles are where you decide things like “treat this pattern as a bug, that one as a code smell, and completely disable this noisy rule”, so they become your **coding standard in machine readable form**.

**2. Quality gates**

* A **quality gate** defines what “good enough to move forward” means for a project. It is a set of conditions on metrics such as coverage, duplications, number of new critical issues, reliability rating and security rating. A typical gate might say: *no new blocker or critical issues on new code, coverage on new code at least eighty percent, duplicated lines on new code less than three percent*.
* If any condition fails, the quality gate status becomes **failed**, and your CI pipeline can be wired to fail the build, block merges or stop deployments, turning those rules into a **hard guardrail** instead of a polite report that nobody reads.

---

## SonarQube architecture: server and scanner

> **Quick note:** in the labs you installed the **SonarQube Server** on the VM; the **Scanner** is a separate client component that runs inside your CI jobs or on build agents.

### 1) SonarQube Server

* The **SonarQube Server** is the central service that receives and stores analysis results, provides the web UI and runs the compute engine that evaluates rules, metrics and quality gates.
* Internally it comprises a **web UI** for browsing projects and managing rules, a **compute engine** that processes incoming analysis reports, and a **database** (PostgreSQL in production) that stores projects, measures, issues, quality profiles, gates, users and tokens.
* The server is the authoritative store and the place teams visit to triage issues, review hotspots and track metrics over time; it is not where analysis is executed.

### 2) SonarQube Scanner (the client)

* The **Scanner** is the component that actually inspects code and produces an analysis report. It runs where your build runs: developer laptop, Jenkins agent, GitLab runner, GitHub Actions runner, etc.
* Typical integrations are **SonarScanner for Maven** (`sonar:sonar`), Gradle scanner, MSBuild scanner or the generic `sonar-scanner` CLI; the scanner reads config (project key, server URL, token), collects source, test and coverage data, and sends a packaged report to the SonarQube Server over HTTP.
* The scanner does not persist long term data. It is a stateless client that pushes results to the server, which allows many parallel build agents to analyze code and upload to one central SonarQube instance.

### How CI systems typically use the scanner

* **Jenkins:** you usually install the SonarQube Scanner plugin or call `mvn sonar:sonar` inside a pipeline stage; Jenkins supplies the token (via credentials) and waits for the analysis and quality gate result. **We'll see this in the demo**.
* **GitLab CI:** add a job that runs the Sonar scanner (or Maven/Gradle goal) and publish the token via CI variables; GitLab can show merge request decorations when configured.
* **GitHub Actions:** use `sonarsource/sonarcloud-github-action` or call `sonar-scanner` in a workflow step; Actions can publish PR annotations and status checks when integrated with SonarQube or SonarCloud.

In short: **Server = central store and UI; Scanner = CI/build-time client that analyzes code and uploads results.** This separation is what makes SonarQube scalable in CI environments while keeping a single source of truth for analysis history and quality gates.

---

## Lab 2: Install Jenkins on EC2 (With Maven & Docker)

### 1. Create the EC2 instance

* AMI: Ubuntu 22.04 LTS
* Instance type: c7i-flex.large (SonarQube likes RAM)
  > **Note:** The instance type **c7i-flex.large** is eligible under the AWS **credits-based free tier**, so you will not incur charges as long as you remain within your credit balance.
* Root volume: 20 GB or more

**Security group (Jenkins VM)**

* Allow SSH (22) from your IP.
* Allow Jenkins UI (8080) from your IP or CI admin network.


### 2. Connect to the instance

```bash
ssh -i <your-key>.pem ubuntu@<jenkins-ec2-public-ip>
```

**Note:** Ensure your SSH private key is readable only by you.
Run:

```bash
chmod 600 ~/.ssh/<your-prvate-key>  
chown $(whoami):$(whoami) ~/.ssh/<your-prvate-key>  
```
**Suggested hostname:** `jenkins`
This makes the server easy to identify in SSH sessions, monitoring dashboards, and DNS-based service discovery.

```bash
# Set hostname and reload your shell
sudo hostnamectl set-hostname jenkins
exec bash
```

**Setting the correct timezone**
Set the machine timezone so logs and timestamps are consistent with your locality.
I'll use `Asia/Kolkata`; pick the timezone that matches your location.

```bash
# set timezone to Asia/Kolkata
sudo timedatectl set-timezone Asia/Kolkata

# verify
timedatectl status
# or
date
```

### 3. Install Java

Jenkins current LTS works well with Java 17.

```bash
# update package lists
sudo apt update

# install OpenJDK 17 includes the JRE (runtime) plus javac and dev tools needed by Maven
sudo apt install -y openjdk-17-jdk

# verify Java runtime and compiler are available
java -version
javac -version
```

You should see Java 17 and a `javac` version in the output.

### 4. Add Jenkins repository and install

Reference: https://www.jenkins.io/doc/book/installing/linux/

```bash
# add Jenkins GPG key to trusted keyring
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

# add Jenkins APT repository (signed)
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# refresh package lists and install Jenkins
sudo apt update
sudo apt install -y jenkins
```

Notes:

* `openjdk-17-jdk` provides `javac` required for Maven builds.
* You can keep `fontconfig` for compatibility with headless JVM operations.


### 5. Start and enable Jenkins

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

Status should show `active (running)`.

Add a 7th step after the unlock section that installs Maven and explains why we need it. Keep it short, practical and copy-paste ready.

### 6. Access Jenkins UI and unlock

In your browser:

```text
http://<jenkins-ec2-public-ip>:8080
```

Jenkins asks for the initial admin password. On the VM:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

* Paste this into the unlock screen.
* Select **Install suggested plugins**.
* Create your admin user.

From here, this Jenkins will be your CI server to run `mvn clean verify sonar:sonar` later and talk to SonarQube on another VM.

### 7. Install Maven (so Jenkins can build Maven projects)

**Why:** Jenkins is just an automation server. To build a Maven project it must invoke `mvn` on the controller or agent that runs the job. Installing Maven on the VM ensures `mvn` is available system-wide for pipeline steps like `mvn clean verify sonar:sonar`.

**Install (Ubuntu 22.04, quick method):**

```bash
sudo apt update
sudo apt install -y maven
```

**Verify:**

```bash
mvn -v
# Example expected output:
# Apache Maven 3.x.y
# Java version: 17.x
```

**Notes / alternatives**

* Apt provides a quick, system-wide Maven install which is fine for labs.
* For newer Maven versions or strict reproducibility, you can download the binary distribution from the Apache Maven site, extract to `/opt/maven` and add `/opt/maven/bin` to `/etc/profile.d/maven.sh`.
* In Jenkins you can also use **Global Tool Configuration → Maven** to let Jenkins auto-install Maven for jobs. If you prefer that, you still might install a system `mvn` for ad-hoc CLI use.

Now your Jenkins VM is ready to run Maven builds and invoke the SonarQube scanner as part of the pipeline.

---

### 8. Install Docker (so Jenkins can build and push container images)

**Why:**
Modern CI/CD pipelines often end with packaging applications into **Docker images** and pushing them to a container registry. Jenkins itself doesn’t include Docker, so we install it on the same VM (controller or agent) that will execute Docker build steps.

**Install Docker (Ubuntu 22.04):**
> Installation Reference: https://docs.docker.com/engine/install/ubuntu/

```bash
# 1. Uninstall older versions (if any)
sudo apt remove docker docker-engine docker.io containerd runc

# 2. Set up Docker's official repository
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 3. Add the Docker repository to Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine, CLI, containerd, and plugins
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



If successful, you’ll see a confirmation message from the Docker test container.

**Allow Jenkins user to run Docker without sudo:**
This adds the `jenkins` user to the `docker` group so Docker commands can run without elevated privileges.
> The `jenkins` user is the service account that runs Jenkins and owns its home and job workspaces.
Keep it least-privilege, avoid human logins, and grant specific rights (for example, docker group) only when necessary.


```bash
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu  # add ubuntu to docker group so ubuntu can run docker without sudo
sudo systemctl restart jenkins
```

> Log out and back in, or restart Jenkins, for the new group membership to apply.

**Verify installation:**

```bash
docker --version
```



**Production note:**
In production, enterprises often **separate build agents** that perform Docker builds from the main Jenkins controller for better isolation and security.
Alternatively, Jenkins agents can connect to a **remote Docker daemon** or use **Docker-in-Docker** in Kubernetes environments.

---


# **Demo: SonarQube with Docker, Maven & Jenkins**

### **Demo Introduction: What We’re Going to Build**

In this demo we’ll take a **private GitHub repository**, create a **fresh Maven project**, intentionally inject **bad code**, and then use **Jenkins + SonarQube** to scan, detect issues, enforce a **Quality Gate**, and even build and run a **Docker image** of the application.

This is a complete, end to end, real world flow:

* Create a Maven project
* Push it to a **private repo** using a GitHub PAT
* Inject bugs, code smells and vulnerabilities
* Configure a Jenkins Freestyle job (so everyone can follow)
* Run **Maven + JaCoCo + SonarQube scan** automatically
* Create a **strict Quality Gate** so the job fails
* Finally build a Docker image and run the app inside a container

By the end, you will clearly understand **how code quality, CI pipelines and containers come together**, and how SonarQube catches issues before code reaches production.

---

## **1) Create a personal access token and a private GitHub repo**

1. Create a private repo on GitHub: **New → Repository → name:** `cwvj-private-repo` → **Private** → Create repository.
2. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**.
3. Click **Generate new token (classic)**.
4. Give it a name like **sonarqube**.
5. Select scopes:

   * `repo` (full repository access is fine for demo).
6. Copy the token immediately.

   * You will use it in the `git remote set-url` command.
   * It will not be displayed again.
7. Reminder: delete this token once the video is recorded.

> Purpose: give Jenkins a machine credential that allows it to both **checkout** the repo during pipeline runs and **push** commits or tags back when needed (for example updating branches, publishing artifacts, or creating demo commits).
Use a dedicated machine token and private repo so access is scoped, auditable, and can be revoked when the demo is finished.



---

## **2) Create the Maven project with predictable structure**

```bash
cd /tmp
mvn archetype:generate \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.5 \
  -DinteractiveMode=false
cd my-app
```

> Note: I run these commands on the **Jenkins server** so most viewers can follow along. You can run them from **any machine** with Git, Maven, and Java (for example your laptop).
> Reason for using `archetypeVersion=1.5`: predictable structure and avoids Java 1.5 compiler issues.


---

## **3) Add JaCoCo plugin to the pom.xml (for coverage reports)**

Open the POM:

```bash
vim pom.xml
```

Inside `<build><plugins>` add:

```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.12</version>
  <executions>
    <execution>
      <goals>
        <goal>prepare-agent</goal>
      </goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals>
        <goal>report</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

> Purpose: SonarQube uses JaCoCo XML report for code coverage.

---

## **4) Inject intentional issues into App.java (for SonarQube demo)**

```bash
vim src/main/java/com/mycompany/app/App.java
```

Remove old content and paste:

```java
package com.mycompany.app;
import java.util.Scanner;

public class App {
    public static void main(String[] args) {
        // Code Smell: Use of System.out instead of a logger
        System.out.println("Welcome to CloudWithVarJosh Demo!");

        // Vulnerability: Hardcoded secret
        String password = "admin123";

        // Bug: Incorrect string comparison using '=='
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter username: ");
        String user = sc.nextLine();
        if (user == "admin") {
            System.out.println("Access granted to admin user.");
        } else {
            System.out.println("Access denied.");
        }

        // Code Smell: Resource (Scanner) not closed properly
        // sc.close(); // intentionally commented out
    }
}
```

SonarQube will beautifully detect:

* Hardcoded secret
* Wrong string comparison
* Missing resource close
* System.out instead of logger

---

## **5) Initialize Git and push code to private GitHub repo**

**Make sure you're inside:**

```
/tmp/my-app
```

Run:

```bash
git init                                   # initialize a new git repo in this folder  
git add .                                  # stage all files for first commit  
git commit -m "Initial commit with code and pom"  # record the initial snapshot  
git branch -v                              # show current branch and last commit (likely 'master')  
git branch -M main                         # rename current branch to 'main' (force move)  
git remote add private-repo https://github.com/CloudWithVarJosh/cwvj-private-repo.git  # add remote named 'private-repo'

```

Now update the remote to include your **GitHub token** (replace `<TOKEN>`):

```bash
git remote set-url private-repo https://<TOKEN>@github.com/CloudWithVarJosh/cwvj-private-repo.git
```

Push:

```bash
git push private-repo main
```

> This confirms your token works and your Jenkins VM can push to the private repo.

---



## **6) Create a Jenkins Freestyle Job (using Execute Shell)**

### 6.1 Create the job

1. Jenkins → **New Item** → name: `my-app-sonar-job` → choose **Freestyle project** → OK.

### 6.2 Configure Git checkout (Jenkins Git plugin auto-clones)

1. Under **Source Code Management → Git** set:

  * **Repository URL:** `https://github.com/CloudWithVarJosh/cwvj-private-repo.git`
  * **Credentials:** select your GitHub token credential (Kind: username/password)
  * **Branch:** `main`
2. Note: Jenkins Git plugin will automatically perform the `git clone`. Do not clone manually in the shell.



### **6.3 Create the project in SonarQube and attach a strict Quality Gate**

#### **Step 1: Create the SonarQube project**

1. Open SonarQube UI: `http://<sonarqube-vm-ip>:9000` and log in as admin.
2. Go to **Projects → Create Project**.
3. Enter:

   * **Project key:** `my-app`
   * **Display name:** `my-app`
   * **Visibility:** Public or Private
4. Click **Create**.

From the Quality Gate UI: Assign the newly created **`my-app-qg`** Quality Gate to this project.

**Notes:**

* Your Maven command’s `-Dsonar.projectKey=my-app` must match this project key exactly.
* SonarQube can auto-create projects on first analysis, but manual creation lets you assign the Quality Gate immediately.
---
#### **Step 2: Create a custom Quality Gate (my-app-qg)**

1. In SonarQube, go to **Quality Gates → Create**.
2. Name it **`my-app-qg`**.
3. Click **Add Condition → Coverage**.
4. Set:

   * **On:** Overall Code
   * **Operator:** is greater than
   * **Value:** `80%`
5. Save the Quality Gate.

> This forces SonarQube to fail the project if **overall coverage is below 80 percent**, making your demo failure predictable and consistent.

---

#### **Step 3: Generate a SonarQube token (for Jenkins / CI)**

1. Log in to the **SonarQube UI** with your admin or developer account.
2. Go to **Administration → Security → Users → Create User**.

   * Create a machine account such as `jenkins-sonar` or `sonar-user` and add it to the `sonar-administrators` group if needed.
   * **Do not** use a human personal username for CI tokens — use a machine-only account.
3. Under **Generate Tokens**, enter a token name (for example, `jenkins-token`) and click **Generate**.
4. **Copy the generated token immediately** — it will not be shown again.

**Tip:** Store this token in Jenkins as a **Secret text** credential (ID: `sonarqube-token`) and bind it into builds as `SONAR_TOKEN`.


#### **Step 4: Bind SonarQube token for secure injection**

1. Under **Build Environment** select **Use secret text(s) or file(s)**.
2. Add → **Bind secret text**:

  * **Variable:** `SONAR_TOKEN`
  * **Credential:** select your SonarQube token credential (`sonarqube-token`).

This exposes `$SONAR_TOKEN` only during the build.

### 6.4 Add the Execute Shell step (Maven + Sonar + Docker build/run)

Scroll to **Build → Add build step → Execute shell** and paste the following script:

```bash
# 1) Show workspace contents
echo "===== Workspace files ====="
ls -la

# 2) Define SonarQube IP (update if needed). Change this to your SonarQube IP
SONAR_IP="18.116.20.248"

# 3) Maven build + test + coverage + SonarQube scan  
echo "===== Maven clean verify + Sonar scan ====="  
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=my-app \
  -Dsonar.host.url="http://$SONAR_IP:9000" \
  -Dsonar.token="$SONAR_TOKEN" \
  -Dsonar.qualitygate.wait=true  
echo "===== Maven + Sonar analysis completed ====="  

# 4) Package the JAR for Docker (OPTIONAL)  
# Reason: `mvn verify` already runs package and creates the JAR.  
# Keep this only if you want an explicit packaging step, a different profile,  
# or to avoid rebuilding in a later pipeline stage.  
echo "===== Packaging JAR (optional) ====="  

# Option A: explicit package (run lifecycle, can skip tests) - recommended for CI  
mvn package -DskipTests  

# Option B: create JAR from already compiled classes (no lifecycle)  
# Use this when compile/verify already ran and you want a fast jar creation.  
# mvn jar:jar


# 5) Create Dockerfile (added because we never created one earlier)
echo "===== Creating Dockerfile ====="
cat << 'EOF' > Dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/my-app-1.0-SNAPSHOT.jar app.jar
ENTRYPOINT ["sh", "-c", "java -jar app.jar"]
EOF

# 6) Build Docker image
echo "===== Building Docker image ====="
docker build -t my-app:1.0 .

# 7) Build Docker image
echo "===== Cleanup before container creation ====="
docker rm --force my-java-app

# 8) Run the Docker container (visible logs for demo)
echo "===== Running Docker container ====="
docker run -d --rm --name=my-java-app my-app:1.0
```

What this script does:

* `clean verify` produces JaCoCo XML and runs Sonar scanner.
* `sonar:sonar` pushes analysis to SonarQube using `$SONAR_TOKEN`.
* `mvn package` guarantees the JAR exists for the Docker build.
* `docker build` produces image `my-app:1.0`.
* `docker run` streams app logs to Jenkins console for visual closure.

**Gotchas:**

* Ensure Docker is installed and the Jenkins agent has Docker permissions.
* If you prefer a detached container, replace `docker run --rm my-app:1.0` with `docker run -d --name my-app-demo my-app:1.0` and use `docker logs -f my-app-demo`.

---

### 6.5 Save and run the Jenkins job

1. Click **Save → Build Now**.
2. Open the build console output and watch for:

```
ANALYSIS SUCCESSFUL
More about the report processing at http://<sonarqube-vm-ip>:9000/dashboard?id=my-app
```

3. If you added `-Dsonar.qualitygate.wait=true` the Maven step will wait for SonarQube to compute the Quality Gate and **fail the build** if the gate is not passed.
4. If the build fails, check the console for clear hints: missing token, wrong host URL, Docker permission error, or Maven compile/test failures.

---


### 6.6 Why the job failed and what to do next

**Why it failed**
The Sonar scanner uploaded the report and SonarQube evaluated the project’s Quality Gate. Because the gate had a failing condition (coverage < 80%), the `-Dsonar.qualitygate.wait=true` option caused Maven to return a non-zero exit and Jenkins marked the build as failed.

**Quick demo fix (not for production)**
Remove the **Coverage** condition from `my-app-qg`:

1. SonarQube → Quality Gates → select **my-app-qg**.
2. Click the trash icon next to the **Coverage** condition.
3. Save and re-run the Jenkins job.
   This will make the pipeline pass immediately. **Do not** use this approach in production — it just silences the symptom for demos.

**Production best practice (recommended)**
Have developers fix the issues: improve tests to raise coverage, remove hardcoded secrets, replace `System.out` with logging, and fix bugs. Enforce the gate in CI and use branch-based or PR gating to keep mainline quality high.


## **7) Verify results inside SonarQube (where to look)**

1. Open `http://<sonarqube-vm-ip>:9000` and go to **Projects → my-app**.
2. Check **Overview**: Quality Gate status, summary counts.
3. Open **Issues → All Issues** and filter by Type: Bug, Vulnerability, Code Smell.

   * Search for `admin123`, `System.out`, `Scanner`, `== "admin"`.
4. Open **Measures → Coverage** to confirm JaCoCo import and percent.
5. Open **Activity → Analysis history** to confirm timestamp matches Jenkins run.


---

## **8) Post-demo cleanup and tips**

1. Delete any temporary tokens created for the demo.
2. If you started a detached container, stop and remove it:

```bash
docker stop my-app-demo
docker rm my-app-demo
```

3. If you used ephemeral Jenkins workspace, clean up if required.
4. To enforce quality gate in CI later, add a script to fail the build when gate fails using SonarQube web API or `sonar.qualitygate.wait=true`.

---


# **Conclusion**

DevSecOps becomes powerful when security and quality checks run **continuously** and **automatically**; not as optional reviews or last-minute audits.
By integrating SonarQube into Jenkins and wiring it through Maven phases, your pipeline now enforces a **real, measurable Quality Gate** that blocks bad code from progressing further.

You’ve installed SonarQube, configured Jenkins, built a Maven project, injected intentional bugs, scanned it, enforced a gate, and packaged everything into a Docker image.
This is the exact foundation used in real-world CI/CD systems; automated, scalable, reproducible.

From here, you can extend the workflow with DAST, SCA, SBOM generation, container scanning, and environment promotions to build a full production-ready DevSecOps pipeline.

---

# **References**

Below are all official and relevant resources used throughout this lecture:

### **SonarQube**

* **SonarQube Official Site:**
  [https://www.sonarsource.com/](https://www.sonarsource.com/)
* **SonarQube Documentation:**
  [https://docs.sonarsource.com/sonarqube](https://docs.sonarsource.com/sonarqube)
* **SonarQube Downloads:**
  [https://www.sonarsource.com/products/sonarqube/downloads/](https://www.sonarsource.com/products/sonarqube/downloads/)
* **Quality Gates & Profiles Docs:**
  [https://docs.sonarsource.com/sonarqube/latest/project-administration/quality-gates/](https://docs.sonarsource.com/sonarqube/latest/project-administration/quality-gates/)

### **Maven**

* **Apache Maven Official Site:**
  [https://maven.apache.org/](https://maven.apache.org/)
* **JaCoCo Coverage Plugin:**
  [https://www.jacoco.org/jacoco/](https://www.jacoco.org/jacoco/)

### **Jenkins**

* **Jenkins Official Documentation:**
  [https://www.jenkins.io/doc/](https://www.jenkins.io/doc/)
* **Installing Jenkins on Linux:**
  [https://www.jenkins.io/doc/book/installing/linux/](https://www.jenkins.io/doc/book/installing/linux/)
* **Jenkins SonarQube Plugin:**
  [https://plugins.jenkins.io/sonar/](https://plugins.jenkins.io/sonar/)

### **DevSecOps, SAST, DAST, SCA**

* **OWASP DevSecOps Overview:**
  [https://owasp.org/www-project-devsecops-guideline/](https://owasp.org/www-project-devsecops-guideline/)
* **OWASP ZAP (DAST):**
  [https://www.zaproxy.org/](https://www.zaproxy.org/)
* **Snyk (SCA + SAST + Container Scanning):**
  [https://snyk.io/](https://snyk.io/)

### **Containers**

* **Docker Documentation:**
  [https://docs.docker.com/](https://docs.docker.com/)
* **Docker Engine Install (Ubuntu):**
  [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

  ---
