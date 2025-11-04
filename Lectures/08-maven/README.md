# Maven Tutorial for DevOps

## Video reference for this lecture is the following:


---
## ⭐ Support the Project  
If this **repository** helps you, give it a ⭐ to show your support and help others discover it! 

---

# Table of Contents

* [Introduction](#introduction)  
* [Compiled & Interpreted Programming Languages](#compiled--interpreted-programming-languages)  
  * [Compiled languages (translated before execution)](#compiled-languages-translated-before-execution)  
  * [Interpreted languages (executed at runtime by an interpreter)](#interpreted-languages-executed-at-runtime-by-an-interpreter)  
  * [Quick side-by-side](#quick-side-by-side)  
* [Why build tools? (DevOps/SRE edition)](#why-build-tools-devops-sre-edition)  
  * [What must happen before code can be deployed (and how it differs by stack)](#what-must-happen-before-code-can-be-deployed-and-how-it-differs-by-stack)  
  * [Dependency management (and why it gets tricky fast)](#dependency-management-and-why-it-gets-tricky-fast)  
  * [What a build automation tool typically does](#what-a-build-automation-tool-typically-does)  
* [What is Maven?](#what-is-maven)  
  * [Maven vs Ant vs Gradle](#maven-vs-ant-vs-gradle)  
* [Installing Maven (Ubuntu, with context)](#installing-maven-ubuntu-with-context)  
  * [0) Java prerequisite](#0-java-prerequisite)  
  * [Option A (recommended for most): install via APT](#option-a-recommended-for-most-install-via-apt)  
  * [Option B: install from the official binary (good to learn paths & env)](#option-b-install-from-the-official-binary-good-to-learn-paths--env)  
* [Demo 1: Create a Maven project (with POM basics)](#demo-1-create-a-maven-project-with-pom-basics)  
  * [Step 1: Generate the project](#step-1-generate-the-project)  
  * [Explore the generated layout](#explore-the-generated-layout)  
  * [Understanding `pom.xml` (XML basics via an example)](#understanding-pomxml-xml-basics-via-an-example)  
  * [The sample `pom.xml` explained (section by section)](#the-sample-pomxml-explained-section-by-section)  
    * [GAV = Coordinates (for every Maven project)](#gav--coordinates-for-every-maven-project)  
    * [SNAPSHOT vs RELEASE](#snapshot-vs-release)  
    * [Properties](#properties)  
    * [Dependency Management (BOMs = version rulebook)](#dependency-management-boms--version-rulebook)  
    * [Dependencies (what actually lands on classpaths)](#dependencies-what-actually-lands-on-classpaths)  
    * [Scope (where a dependency is visible)](#scope-where-a-dependency-is-visible)  
    * [Build / `pluginManagement` (pin plugin versions)](#build--pluginmanagement-pin-plugin-versions)  
* [The Maven Build: Lifecycles, Phases, Plugins, and Goals](#the-maven-build-lifecycles-phases-plugins-and-goals)  
  * [1) Core concepts (clear and concise)](#1-core-concepts-clear-and-concise)  
  * [2) The three built-in lifecycles](#2-the-three-built-in-lifecycles). 
    * [A) default — build & publish software (most used)](#a-default--build--publish-software-most-used)  
    * [B) clean — remove outputs from previous builds](#b-clean--remove-outputs-from-previous-builds)  
    * [C) site — generate project documentation](#c-site--generate-project-documentation)  
  * [3) Plugins and goals (the actual work)](#3-plugins-and-goals-the-actual-work)  
    * [Running a phase vs running a goal directly](#running-a-phase-vs-running-a-goal-directly)  
    * [Minimal example: adding your own binding](#minimal-example-adding-your-own-binding)  
* [Step 2: Run commands and observe](#step-2-run-commands-and-observe)  
  * [Validate and compile](#21-validate-and-compile)  
  * [Run the app from compiled classes](#22-run-the-app-from-compiled-classes)  
  * [Package a JAR and run from the JAR](#23-package-a-jar-and-run-from-the-jar)  
  * [Install to your local repository](#24-install-to-your-local-repository)  
  * [Deploy to a remote repository (will currently fail)](#25-deploy-to-a-remote-repository-will-currently-fail)  
  * [Clean build outputs](#26-clean-build-outputs)  
  * [Combine phases (mixed lifecycles run left→right)](#27-combine-phases-mixed-lifecycles-run-leftright)  
  * [Day-to-day DevOps cheat-sheet](#28-day-to-day-devops-cheat-sheet)  
* [Repositories & roles (clean breakdown)](#repositories--roles-clean-breakdown)  
  * [What is a repository (and why)?](#what-is-a-repository-and-why). 
  * [Local repository (cache)](#local-repository-cache)  
  * [Remote repositories (three common types)](#remote-repositories-three-common-types)  
  * [Where you configure them](#where-you-configure-them)  
  * [Resolution order (who gets checked, in what order)](#resolution-order-who-gets-checked-in-what-order)  
  * [What is a “mirror”?](#what-is-a-mirror)  
  * [Recommended prod pattern](#recommended-prod-pattern)  
  * [Deploy vs resolve (don’t mix them up)](#deploy-vs-resolve-dont-mix-them-up)  
  * [Configuration hierarchy (which wins)](#configuration-hierarchy-which-wins)  
  * [Examples](#examples)  
* [Super POM & Parent POM: Maven Defaults and Org-Level Standards](#super-pom--parent-pom-maven-defaults-and-org-level-standards)  
  * [Super POM (and how real projects use parents)](#super-pom-and-how-real-projects-use-parents)  
  * [Parent POM (what you control in production)](#parent-pom-what-you-control-in-production)  
    * [Example: what a child POM inherits from the parent](#example-what-a-child-pom-inherits-from-the-parent)  
* [Demo 2: Build with Maven in Jenkins and run as a Docker container](#demo-2-build-with-maven-in-jenkins-and-run-as-a-docker-container)  
  * [Step 1: Install Maven inside the Jenkins container](#step-1-install-maven-inside-the-jenkins-container)  
  * [Step 2: Create a Freestyle job in Jenkins](#step-2-create-a-freestyle-job-in-jenkins). 
  * [Step 3 — Verify the run](#step-3--verify-the-run)  
* [Conclusion](#conclusion)  
* [References](#references)  

---

# Introduction

This masterclass is a practical, DevOps-first tour of **Maven**—the JVM ecosystem’s build and project management workhorse. We’ll start with just enough background on **compiled vs interpreted** stacks, then dive into Maven’s **POM** as the single source of truth (GAV, properties, dependencies, dependencyManagement, build/plugins). You’ll learn how **lifecycles → phases → plugins → goals** fit together, what **scopes** really control (compile/test/runtime/packaging classpaths), and how **SNAPSHOT vs RELEASE** behaves in production. We’ll demystify **repositories** (local, mirror, Central, vendor) and resolution order, then finish with a hands-on **Jenkins demo**: generate a project, `mvn package`, build a minimal Docker image, and run the container—end to end, reproducibly.

---

## Compiled & Interpreted Programming Languages

### Compiled languages (translated **before** execution)

* **What happens:** Source is converted to:

  * **Machine code** (CPU-native) → C, C++, Go produce ELF/EXE.
  * **Bytecode** (needs a runtime) → Java → `.class/.jar`, C# → `.dll` for JVM/.NET CLR.
* **Why a build is mandatory:** You must **compile** and often **link**; the output is an artifact (binary/JAR) not human-readable.
* **Traits:** Fast execution, code is hidden inside binaries, reproducible when built the same way.
* **Examples:** Java, C/C++, Go, Rust, C#.

**Build tools used**

| Language | Compiler              | Common Build Tools           | Artifact Format              |
| -------- | --------------------- | ---------------------------- | ---------------------------- |
| **Java** | `javac`               | Maven, Gradle                | `.class`, `.jar`, `.war`     |
| **C**    | `gcc`, `clang`        | make, CMake                  | Executable (`a.out`, `.exe`) |
| **C++**  | `g++`, `clang++`      | CMake, Bazel                 | Executable / `.so`, `.dll`   |
| **Go**   | `go build` (built-in) | Go modules (built-in), Bazel | Single binary executable     |


**Typical pipeline**

```
resolve deps → compile → test → package (jar/war/bin) → publish → (optional) dockerize
```

---

### Interpreted languages (executed **at runtime** by an interpreter)

* **What happens:** The interpreter runs source directly (line by line or via bytecode it generates internally).
* **No explicit compilation required**, but…
* **Why a build still helps:** modern delivery still benefits from a **build stage** to:

  * Install & **lock dependencies** (including transitives).
  * **Transpile/bundle/optimize** (e.g., TS→JS, minify, tree-shake; asset pipelines).
  * **Package** a reproducible artifact (wheel/PEX/zipapp, `/dist` bundle) or **Docker image**.
  * Run **linters/tests/security scans**, generate **SBOM**.
* **Traits:** Typically slower than native, code often remains visible (scripts shared as-is).
* **Examples:** Python, JavaScript/TypeScript, Ruby, PHP.

**Build tools used**


| Language                    | Runtime     | Common Build/Package Tools                                                                        | Artifact Format                                       |
| --------------------------- | ----------- | ------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| **Python**                  | CPython     | **pip**, **Poetry**; build backends (**setuptools**, **hatch**); optional **PyInstaller**/**PEX** | Wheel `.whl`, zipapp/PEX, Docker image                |
| **JavaScript / TypeScript** | Node (V8)   | **npm**/**pnpm**/**yarn**; bundlers **esbuild**/**Rollup**/**Webpack**/**Vite**                   | `/dist` bundle, Node service image                    |
| **Ruby**                    | MRI/YJIT    | **Bundler** + **Rake**; Rails **assets:precompile**                                               | Gem, app source + assets, Docker image                |
| **PHP**                     | Zend Engine | **Composer** (autoload optimize)                                                                  | App source + `vendor/`, PHAR (optional), Docker image |


**Typical pipeline**

```
create venv / install deps → lint/test/scan → bundle/transpile (if any) → package (zip/wheel/dist) → dockerize → publish
```

---

## Quick side-by-side

| Aspect             | Compiled                                    | Interpreted                                                |
| ------------------ | ------------------------------------------- | ---------------------------------------------------------- |
| Execution model    | Translate **before** run (machine/bytecode) | Translate/execute **at runtime**                           |
| Must compile?      | **Yes**                                     | **No** (but build still valuable)                          |
| Build tool purpose | Compile/link + full CI gates + package      | Manage deps, bundle/transpile, CI gates, package/dockerize |
| Artifact           | Binary/JAR/WAR                              | Wheel/PEX/zipapp, `/dist` bundle, Docker image             |
| Examples           | Java, C/C++, Go, Rust, C#                   | Python, JS/TS, Ruby, PHP                                   |

**Bottom line:**

* Compiled stacks **require** build tools to turn code into executables.
* Interpreted stacks **benefit** from build tools for dependency control, optimization, packaging, and reproducibility—so you still get a **clean, promotable artifact** for Dev → Stage → Prod.

---

# Why build tools? (DevOps/SRE edition)

When you write code, you **can’t deploy the raw source** as-is. Before it can run reliably in any environment, a few prerequisites must be met. You need **build tools** to automate the conversion from human-readable code to a **deployable, verified artifact**—the same way, every time.

---

## What must happen before code can be deployed (and how it differs by stack)

### 1) Automate source-to-artifact conversion

Turn raw code into a deployable file predictably; encode the steps so builds aren’t ad-hoc.

* **Compiled:** Automate **compile → link → sign** into a binary/JAR (e.g., `javac` via Maven, `gcc` via CMake).
* **Interpreted:** Automate **env setup, dependency install, bundling**, and **image build** (e.g., `pip install`, `npm run build`, `docker build`).

### 2) Resolve dependencies (including transitives) and lock versions

Fetch everything your app needs—including what your libraries need—and pick compatible versions.

* **Compiled:** Maven/Gradle, Cargo, CMake + package managers (**Conan/vcpkg**) resolve the full graph; use **BOMs**, `go.mod`, `Cargo.lock`.
* **Interpreted:** `pip/Poetry` with `requirements.txt`/`poetry.lock`, npm/pnpm with `package-lock.json`/`pnpm-lock.yaml`, Composer’s `composer.lock`.

### 3) Compile or prepare the code

Produce machine/bytecode—or transpile/bundle assets—using consistent toolchains and flags.

* **Compiled:** `javac`/`kotlinc`/`rustc`/`go build` with fixed versions and options (e.g., `-release`, `-O`, `-tags`).
* **Interpreted:** TypeScript→JavaScript, minify/tree-shake CSS/JS, generate autoload maps, warm bytecode caches (e.g., Python `.pyc`, PHP OPcache preload).

### 4) Run checks: tests, linters, security, licenses

Catch defects early and enforce quality/security gates before shipping.

* **Compiled:** Surefire/Failsafe, JaCoCo coverage, Enforcer rules, dependency/License/SAST scans.
* **Interpreted:** `pytest` + coverage, ESLint/Flake8/RuboCop, Bandit/npm-audit/Composer audit; same SAST/dependency scans.

### 5) Package into a reproducible artifact

Create something you can promote across environments without rebuilding.

* **Compiled:** `.jar/.war`, native **binaries**, shared libs; sometimes a **fat/uber-jar**.
* **Interpreted:** **wheel/PEX/zipapp** (Python), `/dist` bundles (Node), or a **Docker image** that bakes in source + deps.

### 6) Publish to a repository/registry

Store artifacts in a system designed for versioning, retention, and provenance.

* **Compiled:** Publish to **Maven/NuGet/Cargo** repos (Nexus/Artifactory), or OS package repos; attach SBOM/signatures.
* **Interpreted:** Upload wheels to PyPI/private index, push images to **Docker Hub/ECR/GHCR**, publish npm packages.

### 7) Eliminate environment drift and manual errors

Standardize toolchains; replace click-ops with scripted, auditable pipelines.

* **Compiled:** Pin **JDK/SDK/compilers** in CI; containerize builds for determinism.
* **Interpreted:** Pin interpreter versions (e.g., `pyenv`, `asdf`, `.node-version`), use virtualenvs/Poetry/conda, lockfiles, and containerized builds.

> **Result:** Build tools automate these steps so every build is **repeatable, testable, and shippable**—not a snowflake that “only works on my machine.”

---

## Dependency management (and why it gets tricky fast)

Your app declares library **A**. A depends on **B**, and B depends on **C/D/E**. These are **transitive dependencies**. A build tool:

* Resolves the full graph, avoids conflicts, and picks **compatible versions**.
* Caches artifacts locally; fetches missing ones from configured repos.
* Enforces **policies** (e.g., block known vulnerable versions).

**Example:** In a banking app, you wouldn’t re-write a calculator or date library—you’d depend on a vetted library. That library pulls its own building blocks. A build tool ensures the **whole set** is consistent and secure.

---

## What a build automation tool typically does

* **Download/resolve dependencies** (and cache them).
* **Compile** (where applicable) to bytecode or native code.
* **Run tests** (unit/integration) and produce **coverage**.
* **Static checks/scans** (lint, SAST, license checks).
* **Package** into an artifact (JAR/WAR/binary/zip/Docker image).
* **Publish** to a repository/registry (local `~/.m2`, Nexus/Artifactory, Docker registry).

> DevOps mantra: **Build once → Promote everywhere.**

---


# What is Maven?

* **Maven is Apache’s build and project management tool for JVM**
  Developed by the **Apache Software Foundation**, written in **Java**, and used widely for **Java, Kotlin, and Scala** projects.
    > **Java vs JVM:** Java is a programming language; the **JVM** is the engine that runs **compiled JVM bytecode**.
    >
    > **Why Scala/Kotlin/Groovy fit:** These languages also **compile to JVM bytecode**, so the same JVM can run them—and the same build approach applies.
    >
    > **Note:** Java, Kotlin, Scala, and Groovy **compile to JVM bytecode**. Because they share the same runtime target (the JVM), **Maven can build and manage projects in all of these languages**, not just Java.
    >
    > **Note 2:** Maven *can* orchestrate non-JVM work via plugins (e.g., `exec`, frontend), but ecosystems like .NET, Python, Rust, Go, and JS have better native tools. Prefer Maven mainly when your project’s center of gravity is **JVM**.



* **It goes beyond “compile & package.”**
  From one declarative file, Maven standardizes **dependency resolution (with transitives), testing, quality gates, reporting, packaging, and publishing**.

* **The POM (Project Object Model) is the single source of truth.**
  A `pom.xml` defines **coordinates (GAV), dependencies, plugins, properties, profiles, and distribution**—driving predictable builds.

* **Convention over configuration = fewer moving parts.**
  With the standard layout (`src/main/java`, `src/test/java`, resources), lifecycles run **validate → compile → test → package → install → deploy** automatically.

* **Rich plugin ecosystem powers real work.**
  Compiler, Surefire (unit tests), Failsafe (integration tests), JaCoCo (coverage), Enforcer (policies), Shade/Assembly (uber-JARs), CycloneDX (SBOM), Jib/Buildpacks (images) and more.

* **Reproducible, CI/CD-friendly builds.**
  Maven caches artifacts, honors lock/BOMs, and integrates cleanly with **Nexus/Artifactory** and modern pipelines for **repeatable, auditable artifacts**.

### Maven vs Ant vs Gradle

* **Ant**: task-oriented, imperative XML—you script every step. Powerful but **low convention**, more maintenance.
* **Maven**: **model-driven, declarative** POM + strong conventions; batteries-included lifecycles and dependency management.
* **Gradle**: flexible **DSL (Groovy/Kotlin)**, fast incremental builds and configuration avoidance; great when you need **fine-grained customization/perf**.

> Rule of thumb: **Start with Maven** for standardized, convention-first builds; **choose Gradle** when you need maximal flexibility or performance tuning; **use Ant** for legacy or highly bespoke tasks.

---

# Installing Maven (Ubuntu, with context)

Maven requires **Java (JDK)**. Any modern JDK works; Ubuntu packages are easiest.
**Reference:** https://maven.apache.org/download.cgi

## 0) Java prerequisite

```bash
sudo apt update
apt search openjdk | grep -E 'openjdk-[0-9]+-jdk\b'   # see available JDKs
sudo apt install -y openjdk-17-jdk                     # good default for CI
java -version
javac -version
```

**JDK vs JRE:**

* **JRE (Java Runtime Environment)** = runtime only (**JVM – Java Virtual Machine** + standard libraries) → can **run** apps.
* **JDK (Java Development Kit)** = JRE + developer tools (e.g., `javac`, `jar`) → **build and run** apps.
  For Maven/CI you need the **JDK**.


## Option A (recommended for most): install via APT

```bash
sudo apt install -y maven
mvn -v
```

Pros: easy updates, integrates with system paths.
Cons: version tied to distro repos.

## Option B: install from the official binary (good to learn paths & env)

This teaches how `PATH` works and keeps optional tools in `/opt`.

1. **Download & extract** (get the current link from the docs if needed):

```bash
cd /opt
sudo wget https://dlcdn.apache.org/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz
# flags: x=extract, v=verbose, z=gzip, f=file
sudo tar -xvzf apache-maven-3.9.11-bin.tar.gz
sudo mv apache-maven-3.9.11 maven   # simplify folder name
```

2. **Explain PATH briefly**
   When you type a command, your shell searches directories listed in `$PATH`. If `mvn` isn’t in any of those directories, the shell won’t find it.

3. **Add Maven to PATH (for your user)**

```bash
echo 'export PATH="$PATH:/opt/maven/bin"' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
echo $PATH
which mvn
mvn -v
```

> Note: You **could** skip manual steps with `sudo apt install maven -y`. I showed the binary path so learners understand **how CLI tools become available** and why we often keep third-party tools under `/opt`.

---


# Demo 1: Create a Maven project (with POM basics)

## Step 1: Generate the project

```bash
mvn archetype:generate \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.5 \
  -DinteractiveMode=false
```
Reference: https://maven.apache.org/guides/getting-started/index.html

### What this command does (quick decode)

* `archetype:generate` → use the **Archetype plugin** to create a project from a template.
* `-DgroupId` → your **namespace** (reverse DNS style).
* `-DartifactId` → the **project name** (also the folder and jar basename).
* `-DarchetypeArtifactId` / `-DarchetypeVersion` → pick the **Quickstart** template (JUnit 5 in 1.5).
* `-DinteractiveMode=false` → non-interactive (“accept my values and build it”).

> **Archetype** = **project template** (folders, `pom.xml`, sample code/tests) so you start with a runnable skeleton.

---

## Explore the generated layout

```bash
cd my-app
# optional: sudo apt install tree
tree -L 2 .
```

You’ll see:

```
my-app/
  pom.xml
  src/
    main/java/...
    test/java/...
```

* `src/main/java` → your app code
* `src/test/java` → tests
* `target/` → appears after you build (compiled classes, jar)

> **Real-world:** these live in your SCM (GitHub/GitLab). CI simply runs Maven on this layout.
> **Security MUST-do:** **Never put secrets/keys/passwords in `pom.xml`** (it’s version-controlled). Store credentials in `~/.m2/settings.xml` `<servers>`, CI secret stores, or environment vars—**not** in the POM or source code. We will discuss this later.


---
# Understanding `pom.xml` (XML basics via an example)


**What is `pom.xml`?**
`pom.xml` is the main Maven file for your project.
It tells Maven your project’s identity (GAV), libraries (dependencies), and build steps (plugins/settings).
Maven reads this file to **build, test, package, and deploy** your app the same way every time.
Think of it as the project’s **single source of truth** for configuration.

>**Note (for DevOps engineers):**
You must understand `pom.xml` deeply—even if developers usually write and own it. In practice, you own CI/CD, so diagnosing build failures, flaky tests, plugin/version issues, profiles, and repository settings in the POM becomes your responsibility. Knowing the POM lets you spot and fix pipeline problems fast.



Before we explore pom.xml, here’s a tiny XML refresher; tailored to POMs.

**Start with a tiny POM snippet**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!-- Comment like this -->
  <dependency scope="test">
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
  </dependency>
</project>
```

**Now read it like this**

* **Root element:** everything is wrapped by the **`project` element** → opening tag `<project …>` … closing tag `</project>`.
* **Element = opening tag + text + closing tag:**
  `<artifactId>my-app</artifactId>` is one **element** with **text content** (“my-app”). It’s **not** a key–value pair.
* **Child elements:** `groupId`, `artifactId`, `version`, and `dependency` are **children of** `project`.
  Inside `dependency`, its own `groupId`/`artifactId` are **children of** `dependency`.
* **Attributes (key–value on the opening tag):**

  * On `<project …>`: `xmlns`, `xmlns:xsi`, `xsi:schemaLocation` are **attributes** that declare namespaces/schema.
  * On `<dependency scope="test">`: `scope="test"` is an **attribute**.
* **Case-sensitive names:** `<Name>` ≠ `<name>`; POM element names are lowercase.
* **Namespaces:** the `xmlns=…` parts on `<project>` tell tools which **POM schema** to validate against.
* **Well-formed basics:** one root element, properly nested/closed tags, quotes around attribute values.
* **Comments:** `<!-- like this -->` (Maven ignores them).

---

## The sample `pom.xml` explained (section by section)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" ...
  <modelVersion>4.0.0</modelVersion>
```

* Standard XML header and POM schema. `modelVersion` is almost always **4.0.0**.

---

```xml
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
```

### GAV = Coordinates (for **every** Maven project)

* **Coordinate format (naming):** `groupId:artifactId:version`
  *This is the artifact’s full name that tools and repositories use.*

* **`groupId` (who owns it):** Reverse-DNS namespace (e.g., `com.cwvj`).

* **`artifactId` (what it is):** Project/module name within the group (e.g., `payments-service`).

* **`version` (which one):** `1.0.0` (release) or `1.1.0-SNAPSHOT` (moving prerelease).
> **“GAV is used to uniquely identify a project or dependency, whether it’s in your local `~/.m2` cache, a private repo, or a public remote like Maven Central.”**

> **GAV coordinates are the project’s “address.”** They uniquely identify **who owns it** (`groupId`), **what it is** (`artifactId`), and **which version** (`version`) so tools and repositories can find the exact artifact every time.

> **Note:** `<packaging>` is declared as a direct child of `<project>` (typically right after **GAV**). If omitted, Maven defaults to **`jar`**. Common alternatives: **`war`**, **`ear`**, **`pom`**, **`ejb`** (and others, depending on plugins).



**Examples**
Syntax: `groupId:artifactId:version`
* **Release (GAV):** `com.cwvj:payments-service:1.0.0`
  **XML:**

  ```xml
  <groupId>com.cwvj</groupId>
  <artifactId>payments-service</artifactId>
  <version>1.0.0</version>
  ```
* **Snapshot (GAV):** `com.cwvj:payments-service:1.1.0-SNAPSHOT`
  **XML:**

  ```xml
  <groupId>com.cwvj</groupId>
  <artifactId>payments-service</artifactId>
  <version>1.1.0-SNAPSHOT</version>
  ```

**Helpful nuances**

* **Uniqueness:** GAV must uniquely identify an artifact across repos and projects.
* **Repo path mapping:**
  `com.cwvj:payments-service:1.0.0` →
  `~/.m2/repository/com/cwvj/payments-service/1.0.0/payments-service-1.0.0.jar`
* **Not part of GAV:** packaging (`jar/war`) and classifiers (`-sources`, `-javadoc`).
* **Snapshots:** refresh according to repo `updatePolicy` (e.g., `daily`, `always`).

> **Note — GAV & dependencies:**
> Any dependency your project needs—whether another internal project or an external library—is identified by its **Maven coordinates (GAV: groupId:artifactId:version)**.
> These coordinates are **mandatory** to uniquely locate an artifact in your **local cache (`~/.m2`)**, **private/company repositories**, or **public repos (e.g., Maven Central)**.

---

### SNAPSHOT vs RELEASE

* **RELEASE (e.g., `1.0.0`) — immutable.**
  Once published, the bits don’t change. After it’s cached locally/CI, Maven won’t re-fetch unless you force it (`-U`), purge cache, or use a different repo URL.

* **SNAPSHOT (e.g., `1.0.1-SNAPSHOT`) — mutable.**
  Each deploy creates a **timestamped** build (`1.0.1-YYYYMMDD.HHMMSS-#`). Maven resolves the “latest” timestamp **when it decides to refresh**.

* **Refresh rules (`updatePolicy`)** — defined per **remote repo**:

  * `always`: check every build
  * `daily` *(default)*: check at most once per day
  * `interval:N`: check every N minutes
  * `never`: never check automatically

* **Where this policy lives.**
  In the remote repo settings you declare (POM or `settings.xml`):
  `<snapshots><updatePolicy>…</updatePolicy></snapshots>`. Different repos can set different policies.

* **What teammates observe.**
  They see newer SNAPSHOTs **only when** their resolver refreshes (per policy), or if their cache is clean/expired, or they pass **`-U`**.

* **Force an update when needed.**
  `mvn -U <goals>` re-checks SNAPSHOTs (and plugin updates) regardless of the current cache/policy.


---

```xml
<name>my-app</name>
<url>http://www.example.com</url> <!-- Note: This doesn't have to be your company website; point it to any canonical project page you use (e.g., GitHub/GitLab repo, internal docs/wiki, project portal, reporting dashboard). -->
```

* **What these are:** human-readable **project metadata**.
* **`<name>`:** Display name shown in reports/logs; often ends up in the JAR manifest (`Implementation-Title`).

  > *Tip:* Many teams keep `<name>` the same as `artifactId` for consistency, but a human-friendly label is fine.
* **`<url>`:** Project **homepage** (repo/docs/site); used by reporting/site plugins to link back.

  > **Note:** It need not be your public website—use whatever URL best represents the project (GitHub/GitLab, internal wiki, SonarQube project page, etc.).
* **Why it helps:** clearer generated docs/SBOMs/repo UIs; easy click-through for teammates and scanners.
* **Good practice:** set a meaningful `<name>` and point `<url>` to the real project page.

**Example**

```xml
<name>payments-service</name>  <!-- or "Payments Service" if you prefer a friendly label -->
<url>https://github.com/cwvj/payments-service</url>
```
---

```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.release>17</maven.compiler.release>

  <!-- Custom, reusable knobs -->
  <spring.boot.version>3.3.4</spring.boot.version>
  <junit.version>5.11.0</junit.version>
</properties>
```

### Properties

* **Maven properties ≠ OS env vars:** they’re **build-time placeholders** you reference as **`${property.name}`** in the POM.
* **Single source of truth:** change once → all references update (great for **versions/flags**).
* **Compiler level:** `maven.compiler.release` sets language **and** stdlib target (prefer over `source/target`).
* **Getting into your app:** not automatic—use **resource filtering** or pass as **JVM/system properties** when running.

**How they’re used**

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>${spring.boot.version}</version>   <!-- from <properties> -->
  </dependency>

  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${junit.version}</version>         <!-- from <properties> -->
    <scope>test</scope>
  </dependency>
</dependencies>
```

**Handy tricks**

* **Override at build time:** `mvn -Dspring.boot.version=3.3.5 package` (CLI `-D` has highest precedence).
* **Read real env vars:** `${env.HOME}` (only when you truly need OS env).
* **Precedence (simple view):** CLI `-D` > active profile/POM/parent defaults.

> **Note — GAV & dependencies:**
> Any dependency your project needs—whether another internal project or an external library—is identified by its **Maven coordinates (GAV: groupId:artifactId:version)**.
> These coordinates are **mandatory** to uniquely locate an artifact in your **local cache (`~/.m2`)**, **private/company repositories**, or **public repos (e.g., Maven Central)**.

---

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.junit</groupId>
      <artifactId>junit-bom</artifactId>
      <version>5.11.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

### Dependency Management (BOMs = **version rulebook**)

* **What it is:** a **version rulebook only**. It **does not download** or **add anything** to your build by itself.
* **How it works:** if you later declare a dependency **without a `<version>`**, Maven **fills in the version** from this section (often via a **BOM**—Bill of Materials).
* **What it isn’t:** it **doesn’t put libraries on the classpath** and **doesn’t install** anything. It only decides **which version** would be used **if** that dependency is actually declared.

**Concrete example**

Rulebook says: “Use JUnit 5.11.x modules.”

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.junit</groupId>
      <artifactId>junit-bom</artifactId>
      <version>5.11.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

Later, you **use** specific JUnit modules **without versions**:

```xml
<dependencies>
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

* Because of the BOM, Maven resolves both to **version 5.11.0** (or whatever the BOM specifies).
* If you **omit** the `junit-jupiter-*` entries from `<dependencies>`, **nothing** is added—BOM alone does **not** add them.


---
```xml
<dependencies>
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

### Dependencies (**what actually lands on classpaths**)

* **What it is:** libraries your project **uses**; Maven adds them (with **transitives**) to the right **classpath(s)**.
* **How it works:** each entry can set **`<scope>`** (compile/test/runtime) and influences **packaging**.
* **Why no `<version>` here?** Versions are supplied by **`<dependencyManagement>`** (e.g., an imported **BOM**) which can itself read from **`<properties>`**.
* **What it isn’t:** this section **declares classpath content**; version policy lives in **`<dependencyManagement>`** (not on the classpath by itself).

**Elsewhere in the POM (so the above works without `<version>`):**

```xml
<properties>
  <junit.version>5.11.0</junit.version>
</properties>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.junit</groupId>
      <artifactId>junit-bom</artifactId>
      <version>${junit.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

---

### Scope (**where a dependency is visible**)

* **What it is:** a switch that defines **which classpaths** the dependency appears on; bundling depends on **project packaging/plugins**.
* **How it works:** choose the **narrowest scope** that fits to reduce size and attack surface.

| Scope                 | Compile CP | Test CP | Runtime CP | Packaged in Artifact* | Use when…                                             |
| --------------------- | ---------- | ------- | ---------- | --------------------- | ----------------------------------------------------- |
| `compile` *(default)* | Yes        | Yes     | Yes        | **No** (standard JAR) | Needed to **compile** and **run** the app             |
| `test`                | No         | Yes     | No         | No                    | Only tests need it (JUnit, Mockito)                   |
| `provided`            | Yes        | Yes     | No         | No                    | Runtime/container **provides** it (e.g., Servlet API) |
| `runtime`             | No         | Yes     | Yes        | **No** (standard JAR) | Needed **only to run** (e.g., JDBC driver)            |
| `system`              | Yes        | Yes     | No         | No                    | From a local file via `<systemPath>` (avoid)          |
| `import`**            | —          | —       | —          | —                     | Only in `<dependencyManagement>` to import a **BOM**  |

> **Production note — scopes & management**
>
> * **`<dependencies>` wins:** a scope set here **overrides** any managed scope.
> * **No scope in `<dependencies>`?** It **inherits** the scope from **`<dependencyManagement>`** (if present); otherwise defaults to **`compile`**.
> * **`<dependencyManagement>` adds nothing to classpaths:** it only **manages** versions/scopes/exclusions for deps when you actually declare them.


> **Compile CP:** what `javac` sees for **main** code.
> **Test CP:** what the test compiler/runner sees for **tests**.
> **Runtime CP:** what the **JVM** sees when the app **runs**.

* **Packaging nuance:**

* For **JAR** projects, dependencies aren’t bundled inside your JAR **by default**. They’re resolved at runtime or declared transitively in your published POM. Use shade/assembly only if you truly need a **fat/uber JAR**.
* For **WAR/EAR** projects, applicable scopes (e.g., compile/runtime) are placed under the app’s lib directories.

** `import` isn’t a classpath scope; it’s valid **only** under `<dependencyManagement>` to import a **BOM** (version rules), not code.

> **Security note:** Use the **narrowest scope** possible (`test`/`provided`/`runtime` over `compile`) to minimize what ships. **Unneeded artifacts** bloat your package and **increase attack surface** (more code, more CVEs, larger SBOM).


---

```xml
<build>
  <pluginManagement>
    <plugins>
      <plugin><artifactId>maven-clean-plugin</artifactId><version>3.4.0</version></plugin>
      <plugin><artifactId>maven-resources-plugin</artifactId><version>3.3.1</version></plugin>
      <plugin><artifactId>maven-compiler-plugin</artifactId><version>3.13.0</version></plugin>
      <plugin><artifactId>maven-surefire-plugin</artifactId><version>3.3.0</version></plugin>
      <plugin><artifactId>maven-jar-plugin</artifactId><version>3.4.2</version></plugin>
      <plugin><artifactId>maven-install-plugin</artifactId><version>3.1.2</version></plugin>
      <plugin><artifactId>maven-deploy-plugin</artifactId><version>3.1.2</version></plugin>
      <plugin><artifactId>maven-site-plugin</artifactId><version>3.12.1</version></plugin>
      <plugin><artifactId>maven-project-info-reports-plugin</artifactId><version>3.6.1</version></plugin>
    </plugins>
  </pluginManagement>
</build>
```

### Build / `pluginManagement` (**pin plugin versions**)

* **What it is:** a section to **lock plugin versions/config** so every machine/agent uses the **same plugin versions**.
  **Without pinning, Maven may choose different default plugin versions**, leading to **inconsistent behavior** across developers/CI.
* **What it isn’t:** it **does not execute** plugins. It only **declares** versions/config to be used **when** those plugins run.
* **Where execution is defined:**

  * **Maven defaults** bind standard goals to phases based on **packaging** (e.g., for `jar`: `compiler:compile`, `surefire:test`, `jar:jar`, `install:install`, `deploy:deploy`).
  * **Your POM** can add/override bindings under **`<build><plugins>`** with `<executions>` (e.g., add Failsafe at `verify`, Shade at `package`).

> **Note:** When you **don’t pin plugin versions**, Maven picks **default plugin versions tied to the Maven (mvn) version** on that machine/CI agent (via the Super POM/lifecycle mappings). This can **differ across environments** or after a Maven upgrade. **Pin plugin versions** in `<pluginManagement>` for consistent builds.


**Default bindings (JAR packaging) you’re pinning versions for:**

* `compile` → `maven-compiler-plugin:compile`
* `test` → `maven-surefire-plugin:test`
* `package` → `maven-jar-plugin:jar`
* `install` → `maven-install-plugin:install`
* `deploy` → `maven-deploy-plugin:deploy`

> **Note:** Putting a plugin under `pluginManagement` makes its **version** available. To actually **run** extra goals, declare the plugin under **`<build><plugins>`** with `<executions>`.

---




# The Maven Build: Lifecycles, Phases, Plugins, and Goals

A **Maven project** is your application (often a single microservice) described by a `pom.xml`. When you build it, Maven follows a **lifecycle** made of ordered **phases**; at each phase, **plugin goals** perform the actual work (compile, test, package, publish). The sections below explain each concept in a practical, example-first way.

---

## 1) Core concepts (clear and concise)

* **Lifecycle** → the build pipeline (a named route).
* **Phase** → an ordered checkpoint on that route (e.g., `compile`, `test`, `package`).
* **Plugin** → a tool Maven uses (Compiler, JAR, Surefire, Deploy, etc.).
* **Goal** → a specific action of a plugin (e.g., `compiler:compile`, `jar:jar`).

> Phases and lifecycles are **orchestration only**; **plugins/goals do the work**.

---

## 2) The three built-in lifecycles

You run a lifecycle by invoking one of its phases. Invoking a phase runs **that phase and all earlier phases in the same lifecycle**.


### A) **default** — build & publish software (most used)

| Phase          | What it ensures/does (typical)                                                                 |
| -------------- | ---------------------------------------------------------------------------------------------- |
| `validate`     | Project structure and required metadata are present; fail fast on missing config.              |
| `compile`      | Compile main sources to `.class`; honor Java level and compiler options.                       |
| `test-compile` | Compile test sources against main output and test dependencies.                                |
| `test`         | Run unit tests in-JVM; produce reports and fail build on test errors.                          |
| `package`      | Assemble distributable (JAR/WAR/etc.); include resources and produced classes.                 |
| `verify`       | Post-package checks: quality gates, integration verification, extra validations.               |
| `install`      | Publish built artifact to **local repo** (`~/.m2/repository`) for reuse by other local builds. |
| `deploy`       | Push release/SNAPSHOT to **remote repo** (Nexus/Artifactory) for team/CI consumption.          |

Check all phases of the **default lifecycle** in the official Maven docs:
[https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)


### B) **clean** — remove outputs from previous builds

| Phase        | What it does                              |
| ------------ | ----------------------------------------- |
| `pre-clean`  | Optional hooks you add                    |
| `clean`      | Delete previous outputs (e.g., `target/`) |
| `post-clean` | Optional hooks you add                    |

### C) **site** — generate project documentation

| Phase         | What it does                         |
| ------------- | ------------------------------------ |
| `pre-site`    | Prepare for site generation          |
| `site`        | Generate project site (reports/docs) |
| `post-site`   | Post-processing                      |
| `site-deploy` | Publish the generated site           |

---

## 3) Plugins and goals (the actual work)

**Phases do nothing by themselves.** The real work happens when **plugin goals** are **bound** to phases.

### What are plugins and goals?

* **Plugin** = a tool Maven can use (compiler, test runner, packager, deployer).
* **Goal** = a specific action of that plugin (e.g., the compiler plugin’s **`compile`** goal).
* **Format to run a goal directly:** `pluginPrefix:goal` (e.g., `compiler:compile`).


### Common plugins/goals (each goal shows its phase)

| Plugin (artifact)        | Prefix      | Goal (you can invoke directly) | Default phase                   | What it does                     |
| ------------------------ | ----------- | ------------------------------ | ------------------------------- | -------------------------------- |
| `maven-compiler-plugin`  | `compiler`  | `compiler:compile`             | `compile`                       | Compile **main** sources.        |
| `maven-compiler-plugin`  | `compiler`  | `compiler:testCompile`         | `test-compile`                  | Compile **test** sources.        |
| `maven-surefire-plugin`  | `surefire`  | `surefire:test`                | `test`                          | Run **unit tests**.              |
| `maven-jar-plugin`       | `jar`       | `jar:jar`                      | `package`                       | Create the **JAR**.              |
| `maven-jar-plugin`       | `jar`       | `jar:test-jar`                 | — (not bound)                   | Build a JAR of **test classes**. |
| `maven-install-plugin`   | `install`   | `install:install`              | `install`                       | Install to **local repo**.       |
| `maven-deploy-plugin`    | `deploy`    | `deploy:deploy`                | `deploy`                        | Deploy to **remote repo**.       |
| `maven-deploy-plugin`    | `deploy`    | `deploy:deploy-file`           | — (not bound)                   | Deploy a **standalone** file.    |
| `maven-failsafe-plugin`  | `failsafe`  | `failsafe:integration-test`    | `integration-test`              | Run **integration tests**.       |
| `maven-failsafe-plugin`  | `failsafe`  | `failsafe:verify`              | `verify`                        | Fail build if ITs failed.        |
| `maven-resources-plugin` | `resources` | `resources:resources`          | `process-resources`             | Copy/filter **main** resources.  |
| `maven-resources-plugin` | `resources` | `resources:testResources`      | `process-test-resources`        | Copy/filter **test** resources.  |
| `maven-clean-plugin`     | `clean`     | `clean:clean`                  | `clean`                         | Delete `target/`.                |
| `maven-site-plugin`      | `site`      | `site:site`                    | `site`                          | Generate project site.           |
| `maven-site-plugin`      | `site`      | `site:deploy`                  | `site-deploy`                   | Publish project site.            |
| `maven-site-plugin`      | `site`      | `site:run`                     | — (not bound)                   | Preview site locally.            |
| `maven-shade-plugin`     | `shade`     | `shade:shade`                  | — (bind typically to `package`) | Build **fat/uber JAR**.          |
| `maven-shade-plugin`     | `shade`     | `shade:relocate`               | — (not bound)                   | Relocate packages in shaded JAR. |

**Notes**

* **“Default phase”** = the lifecycle phase that triggers the goal automatically.
* **“— (not bound)”** = only runs if you call it (e.g., `mvn jar:test-jar`) or bind it via `<executions>`.
* Running `mvn compile` triggers only goals bound to **`compile`** (e.g., `compiler:compile`), not `testCompile`.


* `shade:shade` isn’t bound by default—you typically bind it to `package` via `<executions>`.

> Notes
> • These phase bindings are the **defaults for `jar` packaging**; `war`/others have different defaults.
> • You can always override/add bindings under `<build><plugins><plugin><executions>…</executions></plugin>`.
> • Discover goals & bindings: `mvn help:describe -Dplugin=maven-compiler-plugin -Ddetail=true` and `mvn help:effective-pom`.
---

### Running a **phase** vs running a **goal directly**

* **`mvn compile` (phase):**
  Runs the **default lifecycle up to `compile`** → `validate` → `compile`.
  Anything bound to earlier phases (e.g., **generate sources**, **process resources**) also runs **before** the compiler.

* **`mvn compiler:compile` (goal directly):**
  Runs **only that plugin goal**. It **does not** automatically run earlier phases.
  If your build relies on outputs from earlier steps (generated code/resources), this may **break** or produce **different** results.

**Rule of thumb**

* Use **phases** (`mvn compile`, `mvn test`, `mvn package`) for normal, reproducible builds.
* Use **direct goals** only for quick experiments or debugging when you’re sure you don’t need the rest of the pipeline.

---

### Minimal example: adding your own binding

If you want Failsafe to run **integration tests** at the right time (after the app is packaged or started), bind its goals to the correct phases:

```xml
<build>
  <plugins>
    <plugin>
      <artifactId>maven-failsafe-plugin</artifactId>
      <version>3.3.0</version>
      <executions>
        <execution>
          <goals>
            <goal>integration-test</goal> <!-- runs during the 'integration-test' phase -->
            <goal>verify</goal>           <!-- runs during the 'verify' phase -->
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

Now `mvn verify` will:

1. Build and package your app,
2. Run integration tests (`failsafe:integration-test`),
3. Fail the build if any ITs failed (`failsafe:verify`).

---
# Step 2: Run commands and observe

### 2.1 Validate and compile

```bash
mvn validate     # checks POM, project structure
mvn compile      # runs validate + resources + compile
```

* **What happens at `compile`:** Maven runs all earlier default-lifecycle phases up to `compile`, then invokes `maven-compiler-plugin:compile`.
* **Output:** `target/classes/` contains compiled `.class` files.

```bash
ls
# pom.xml  src  target
```

### 2.2 Run the app from compiled classes

```bash
java -cp target/classes com.mycompany.app.App
# Hello World!
```

* **`-cp` (classpath):** tells the JVM where to find compiled classes.
* **`com.mycompany.app.App`:** fully-qualified main class (package + class).
* The quickstart project **does not** set a `Main-Class` in the JAR manifest, so you must name the main class explicitly.

### 2.3 Package a JAR and run from the JAR

```bash
mvn package
java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
# Hello World!
```

* **What changed?** `package` created a JAR in `target/`.
* **Why the same output?** You’re still pointing the JVM to the same entry class; only the classpath location changed (classes folder vs JAR).
* **Executable JAR option (later):** add a manifest with `Main-Class` (via `maven-jar-plugin`) or build a **fat/uber JAR** (shade/assembly) so you can run `java -jar app.jar` without `-cp`.

### 2.4 Install to your local repository

```bash
mvn install
# copies the artifact into your local repo: ~/.m2/repository/...

ls -a ~/.m2/repository/com/mycompany/app/my-app/
# .  ..  1.0-SNAPSHOT  maven-metadata-local.xml
```

* **`install`** = put the built artifact into the **local** repository so other local projects can depend on it (by GAV).

### 2.5 Deploy to a remote repository (will currently fail)

```bash
mvn deploy
```

* This fails now because **no remote repository is configured**.
* **Deploy** uploads the artifact to a remote repo defined in your POM’s `<distributionManagement>` with matching credentials in `~/.m2/settings.xml`. (Think: pushing a Docker image to a registry.)

> Minimal deploy wiring (for later):
>
> ```xml
> <distributionManagement>
>   <repository>          <!-- releases -->
>     <id>releases</id>
>     <url>https://nexus.example.com/repository/maven-releases</url>
>   </repository>
>   <snapshotRepository>  <!-- snapshots -->
>     <id>snapshots</id>
>     <url>https://nexus.example.com/repository/maven-snapshots</url>
>   </snapshotRepository>
> </distributionManagement>
> ```
>
> And in `~/.m2/settings.xml`:
>
> ```xml
> <servers>
>   <server><id>releases</id><username>ci</username><password>***</password></server>
>   <server><id>snapshots</id><username>ci</username><password>***</password></server>
> </servers>
> ```

### 2.6 Clean build outputs

```bash
mvn clean
# deletes target/ so you can build fresh
```

### 2.7 Combine phases (mixed lifecycles run left→right)

```bash
mvn clean install   # clean lifecycle → clean, then default lifecycle → ... → install
mvn clean site      # clean first, then generate site docs
```

* **Docs location:** `target/site/` (try `tree target/site`).

### 2.8 Day-to-day DevOps cheat-sheet

```bash
mvn -B clean package                 # non-interactive CI build
mvn -DskipTests package              # build fast (unit tests skipped)
mvn -Dmaven.test.skip=true package   # skip compiling tests too
mvn verify                           # run full checks before install/deploy
mvn dependency:tree                  # inspect dependency graph/conflicts
mvn help:effective-pom               # see merged POM (incl. Super POM)
mvn help:effective-settings          # see merged settings (mirrors, servers)
mvn -o package                       # offline build (use ~/.m2 only)
```

> ⚠️ **Common correction:** `mvn clean install` **does not deploy**.
> **Deploy** only happens with `mvn deploy` and a configured `<distributionManagement>`.

---


# Repositories & roles (clean breakdown)

## What is a repository (and why)?  *(see Figure 1)*

A **Maven repository** is a storage service that holds:

* **Artifacts** (JAR/WAR/EAR), **POMs**, **plugin binaries**, plus **metadata & checksums**.
* It serves two jobs: **resolve** (download what your build needs) and **publish** (upload what you build).
* Repositories enable **caching** (faster/offline), **governance** (license/security policies), and **reproducible** builds across laptops and CI.

## Local repository (cache)

* **Path:** `~/.m2/repository` (override via `~/.m2/settings.xml` → `<localRepository>`).
* **Role:** First place Maven looks. If an artifact is already cached, no network call is made. Enables **offline** builds (`mvn -o ...`).

## Remote repositories (three common types)  *(right side of Figure 1 & 2)*

1. **Company-managed (private)**
   Examples: **Sonatype Nexus**, **JFrog Artifactory**, or a cloud artifact service.
   Acts as a **proxy/cache** for public repos and a **host** for internal **releases** and **snapshots**. Enforces org policies, improves speed, and gives deterministic builds.

2. **Maven Central (public)**
   The default public repo wired in via the **Super POM**. Even with zero config, Maven can reach Central (unless you redirect with a mirror).

3. **Third-party vendor repos** (public or private)
   Hosted by vendors/projects for commercial SDKs or specialized libs. You add them explicitly in **`<repositories>`** / **`<pluginRepositories>`**, or aggregate them **behind your corporate group repo** in Nexus/Artifactory.

## Where you configure things  *(left column of both figures)*

* **Project `pom.xml`**:

  * **`<repositories>` / `<pluginRepositories>`** → where to **download** from.
  * **`<distributionManagement>`** → where to **upload** (`mvn deploy`) releases/snapshots.
* **User `~/.m2/settings.xml`**: **mirrors**, **servers** (credentials), **proxies**, local repo path.
* **Global `$MAVEN_HOME/conf/settings.xml`**: org-wide defaults (rarer).
* **Rule of thumb:** keep **secrets & mirror rules** in `~/.m2/settings.xml`; keep **deploy targets** (URLs/ids) in the **POM**; avoid hard-coding credentials in the POM.

---

## Resolution order (who gets checked, in what order)

Maven walks this path for **downloads** (blue arrows in the diagrams):

1. **Local cache (`~/.m2/repository`)**
   Your machine’s artifact cache. If the exact GAV is already here, Maven uses it immediately—no network calls, supports offline builds.

2. **Mirror (if configured)**
   A redirect defined in `~/.m2/settings.xml` (usually a Nexus/Artifactory **group** URL). With `mirrorOf="*"`, *all* requests go to this single front door, which serves from cache or fetches from its upstreams.

3. **Declared remotes (in order)**
   Repositories you list in your **POM** (`<repositories>` / `<pluginRepositories>`). Maven queries them in the sequence you declared—unless a catch-all mirror intercepts first.

4. **Central (Maven Central)**
   The default public repository, wired in via the Super POM. Maven reaches Central only if you haven’t redirected traffic through a mirror (or if your mirror is scoped and doesn’t match Central).

> **Color legend in Figure 2**
> **Blue** = resolve/download path.
> **Orange** = deploy/upload path (to releases/snapshots).


---

## What is a “mirror”?  *(center block in Figure 2)*

A **mirror** is a redirect in `settings.xml` that points multiple repos to **one front door**—usually your **Nexus/Artifactory group URL**.

* With `mirrorOf="*"`, **all** download requests go through that URL.
* The mirror then serves from its **cache** or **fetches upstream** (Central, vendor, internal) using its own configuration.

> **Important (shown in Figure 2):**
> A “match-all” mirror **does not** fall back to Central automatically. If the mirror can’t find an artifact and it isn’t configured to proxy Central/vendor repos, **resolution fails**. Make sure your mirror proxies **every upstream you need**.

---

## Deploy vs Resolve (don’t mix them up)  *(orange arrows in Figure 2)*

* **Resolve (download)**: sources are `pom.xml` **`<repositories>` / `<pluginRepositories>`** (plus any **mirrors** in `settings.xml`).
* **Deploy (upload)**: targets come from the POM’s **`<distributionManagement>`** and credentials from `~/.m2/settings.xml` **`<servers>`** (ids must match).

  * `mvn install` → copies to **local** repo (`~/.m2/...`).
  * `mvn deploy` → uploads to the **remote** releases/snapshots repo.

---

## Production pattern (what Figure 2 shows)

1. **Use a single “group” mirror** (Nexus/Artifactory) via `mirrorOf="*"` so every build hits one front door.
2. Configure that mirror to proxy **Central** + **vetted vendor repos** + **your internal** repos.
3. In the **POM**, set **`<distributionManagement>`** for **releases** and **snapshots** (upload targets).
4. In **`~/.m2/settings.xml`**, store **credentials** ( `<servers>` ) and the **mirror** rule ( `<mirrors>` ).
5. Result: **simple CI config**, **governed traffic**, **reproducible builds**.

---

## Configuration hierarchy (which wins)

Maven merges configuration; the nearer one wins:

1. Global settings (`$MAVEN_HOME/conf/settings.xml`)
2. User settings (`~/.m2/settings.xml`)
3. Project POM (`pom.xml`)
4. Profiles (in either file)

> **Production note:** Prefer **user settings** for secrets and mirrors (easy rotation, safe from Git). Reserve **global** for read-only defaults (e.g., a curated classroom image). Keep **deploy URLs** in the POM; keep **credentials** in `settings.xml`.

---

## Copy-paste examples (exactly as shown in Figure 2)

### A) `~/.m2/settings.xml` — mirror + credentials

```xml
<!-- ~/.m2/settings.xml (mirror + credentials) -->
<settings>
  <mirrors>
    <mirror>
      <id>corp-group</id>
      <mirrorOf>*</mirrorOf> <!-- match ALL repos, including Central -->
      <url>https://nexus.example.com/repository/maven-group</url>
    </mirror>
  </mirrors>

  <!-- Credentials for deploy (ids MUST match POM <distributionManagement>) -->
  <servers>
    <server>
      <id>releases</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
    <server>
      <id>snapshots</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
```

### B) `pom.xml` — deploy targets (releases & snapshots)

```xml
<!-- pom.xml (deploy targets) -->
<distributionManagement>
  <repository>
    <id>releases</id>
    <url>https://nexus.example.com/repository/maven-releases</url>
  </repository>
  <snapshotRepository>
    <id>snapshots</id>
    <url>https://nexus.example.com/repository/maven-snapshots</url>
  </snapshotRepository>
</distributionManagement>
```

### C) (Optional) If you’re **not** using a match-all mirror

```xml
<!-- pom.xml: add a vendor repository explicitly -->
<repositories>
  <repository>
    <id>acme-vendor</id>
    <url>https://repo.acme.com/maven/releases</url>
    <releases><enabled>true</enabled></releases>
    <snapshots><enabled>false</enabled></snapshots>
  </repository>
</repositories>

<pluginRepositories>
  <pluginRepository>
    <id>acme-vendor-plugins</id>
    <url>https://repo.acme.com/maven/plugins</url>
  </pluginRepository>
</pluginRepositories>
```

---

## Quick FAQ (ties back to the diagrams)

* **Does `mvn deploy` ship my app to a VM?**
  No. It **uploads artifacts** to a repository (e.g., Nexus/Artifactory) using the **releases/snapshots** URLs from **`<distributionManagement>`**.

* **Why use a mirror?**
  To centralize policy, cache, and performance. With `mirrorOf="*"`, the **mirror becomes the front door** and your builds never talk directly to the internet.

* **What if the mirror can’t find a dependency?**
  If you use `mirrorOf="*"`, Maven **won’t** fall back to Central by itself. **Fix the mirror configuration** (proxy Central/vendor upstreams) or relax the mirror rule.

* **Where do credentials live?**
  In **`~/.m2/settings.xml`** `<servers>`. Never in the POM. Use **env vars** or your CI secret store to inject values.

---

## Super POM & Parent POM: Maven Defaults and Org-Level Standards

### Super POM (and how real projects use parents)

**Super POM (built-in default POM)**
Every `pom.xml` you write **implicitly inherits** from Maven’s **Super POM** (a default POM bundled inside Maven).
It defines **global defaults**, for example:

* Default **repositories** (Maven Central).
* Default **source/resource dirs** (`src/main/java`, `src/test/java`, `src/main/resources`, …).
* Default **plugin–phase bindings** (e.g., `compile` → `compiler:compile`, `test` → `surefire:test`).
* Basic **reporting** and other sensible defaults.

You never edit the Super POM directly; you only **override/extend** its values in **your** `pom.xml`.

> **Tip:** Run `mvn help:effective-pom` to see your POM **plus** everything inherited from the Super POM (and any parent POM). This is the “truth” Maven actually uses.

---


### Parent POM (what you control in production)

On top of the **Super POM**, teams usually add an explicit **parent POM**:

```xml
<parent>
  <groupId>com.cwvj</groupId>
  <artifactId>cwvj-parent</artifactId>
  <version>1.0.0</version>
</parent>
```

A **parent POM** is just another POM you own that child projects inherit from. In production it is used to:

* Centralize **dependencyManagement** (shared versions/BOMs).
* Pin **pluginManagement** (same plugin versions across all services).
* Set common **repositories**, **properties** (e.g., `java.version`), and **profiles**.
* Enforce org-wide rules (e.g., **Maven Enforcer**: no SNAPSHOTs, dependency convergence, etc.).

Each microservice/module then has a **small child POM** that focuses on **service-specific** bits (GAV, deps, maybe 1–2 extra plugins), while:

* The **parent POM** provides org-level standards, and
* The **Super POM** (built into Maven) still sits at the very top, providing global defaults for *every* project.

---

#### Example: what a child POM inherits from the parent

**Parent POM (`cwvj-parent`)**

```xml
<project>
  <groupId>com.cwvj</groupId>
  <artifactId>cwvj-parent</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>

  <properties>
    <java.version>17</java.version>
    <spring.boot.version>3.3.4</spring.boot.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <!-- Spring Boot BOM for all services -->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${spring.boot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.13.0</version>
          <configuration>
            <release>${java.version}</release>
          </configuration>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

**Child POM (e.g., `payments-service`)**

```xml
<project>
  <parent>
    <groupId>com.cwvj</groupId>
    <artifactId>cwvj-parent</artifactId>
    <version>1.0.0</version>
  </parent>

  <artifactId>payments-service</artifactId>
  <version>1.2.0</version>

  <dependencies>
    <!-- Version comes from parent’s Spring Boot BOM -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Version for junit-jupiter also comes from parent BOM (if added there) -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <!-- Uses compiler plugin + java.version from parent’s pluginManagement/properties -->
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

**What the child actually gets “for free”:**

* **Spring Boot + JUnit versions** from parent’s `dependencyManagement` (via BOM).
* **`java.version`** and **compiler config** from parent’s `<properties>` + `<pluginManagement>`.
* Your child POM stays **small and focused**, while org-wide standards live in the parent (and global defaults in the Super POM).

---

# Demo 2: Build with Maven in Jenkins and run as a Docker container

In Demo 1, you learned what Maven does and how artifacts flow. Now we’ll **automate those same Maven steps in Jenkins** and **containerize the resulting JAR**, then run it locally on the Docker host.

## What you’ll need (quick prerequisites)

* A Jenkins running in Docker **with access to Docker** (Docker CLI + Docker socket mounted, e.g., `-v /var/run/docker.sock:/var/run/docker.sock`).
* Network access from the Jenkins container to reach Maven Central (or your corporate mirror).
* Java and Maven available **inside** the Jenkins container.

> You can use this link to spin up the Jenkins container required for these demos:
[https://github.com/CloudWithVarJosh/Jenkins-Basics-To-Production/tree/main/Day%2005#step-1-recreate-jenkins-with-the-docker-socket-mounted](https://github.com/CloudWithVarJosh/Jenkins-Basics-To-Production/tree/main/Day%2005#step-1-recreate-jenkins-with-the-docker-socket-mounted)


---

## Step 1: Install Maven inside the Jenkins container

```bash
docker exec -it -u root jenkins bash
apt update
apt install -y maven
mvn --version  # verify Maven is installed
exit
```

**Why:** Jenkins runs inside a container; Maven must be present there to execute `mvn` builds.

---

## Step 2: Create a Freestyle job in Jenkins

1. **New Item** → name it `maven-job` → **Freestyle project**.
2. **Build Steps** → **Execute Shell** → paste the script below.

### What this script does (at a glance)

* **Generates** a minimal Java app using the Maven **Archetype** (Hello World).
* **Builds** it with Maven to produce `target/my-app-1.0-SNAPSHOT.jar`.
* **Creates** a minimal **Dockerfile** (Java 17 JRE image) and a **.dockerignore**.
* **Builds** a **local** Docker image and **runs** it, printing “Hello World!”.

---

## Execute Shell — paste this as-is

```bash

# 1) Generate a sample Maven project (Hello World)
mvn archetype:generate \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.5 \
  -DinteractiveMode=false

cd my-app

# 2) Build the JAR (clean workspace, skip tests for speed)
# -B: batch mode (non-interactive), good for CI logs
# output: target/my-app-1.0-SNAPSHOT.jar
mvn -B -DskipTests clean package

# 3) Create a minimal runtime Dockerfile
# Using a small OpenJDK 17 JRE base (Temurin)
cat > Dockerfile <<'EOF'
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/my-app-1.0-SNAPSHOT.jar /app/app.jar
ENTRYPOINT ["java","-cp","/app/app.jar","com.mycompany.app.App"]
EOF

# Keep build context small (don’t send everything to Docker daemon)
cat > .dockerignore <<'EOF'
target/*
!target/my-app-1.0-SNAPSHOT.jar
.git
.gitignore
EOF

# 4) Build the image locally
# Tag is local-only; no push in this demo
docker build -t cwvj-java-hello:local .

# 5) Run the container locally (and delete it after exit)
echo "----- Container output below -----"
docker run --rm --name cwvj-java-hello cwvj-java-hello:local
echo "----------------------------------"
```

---

## Step 3 — Verify the run

* Open the Jenkins **Console Output**.
* You should see your build steps, the Docker build logs, and finally:

```
----- Container output below -----
Hello World!
----------------------------------
```

> Tip: The Jenkins workspace path (e.g., `/var/jenkins_home/workspace/maven-job/my-app`) will contain `pom.xml`, `src/`, `target/`, `Dockerfile`, and `.dockerignore`. Explore it to reinforce how sources → JAR → image was produced.

---

## What you just automated (DevOps view)

* **Archetype →** standardized project structure & starter POM.
* **Maven lifecycle →** `validate → compile → package` to produce a **repeatable** JAR.
* **Containerization →** minimal Java 17 **runtime** image; app launched via `java -cp`.
* **Local deploy →** `docker run` on the **same** Docker host Jenkins can reach.

---

## Common variations (for later)

* **Executable JAR:** add a `Main-Class` manifest (via `maven-jar-plugin`) and use `java -jar`.
* **Fat/uber JAR:** use **maven-shade-plugin** to bundle dependencies inside one JAR.
* **Push to a registry:** add `docker login`, `docker tag`, `docker push` steps (disabled here by design).
* **Use a Maven mirror:** configure `~/.m2/settings.xml` with a corporate **mirror** to speed up and govern dependency resolution.

That’s it. You’ve turned a plain Maven build into a one-click Jenkins job that builds, images, and runs a Java “Hello World” app—end-to-end.

---

# Conclusion

Maven’s power comes from **declarative intent + conventions**: the POM models *what* you want; lifecycles and plugins perform *how* consistently. Once you grasp **coordinates (GAV)**, **scopes**, **dependencyManagement vs dependencies**, and **default bindings**, you can predict any build—locally or in CI. Pair this with a sane repository strategy (**local → mirror → upstreams**) and you get **repeatable, auditable artifacts** that promote cleanly across environments. The Jenkins demo proved the loop: **generate → build → package → containerize → run**. From here, you can add coverage, quality gates, SBOMs, signing, and promotion policies without changing your mental model.

---

# References

1. **Maven – Getting Started**
   [https://maven.apache.org/guides/getting-started/index.html](https://maven.apache.org/guides/getting-started/index.html)
2. **POM Reference**
   [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)
3. **Build Lifecycle, Phases & Goals**
   [https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
4. **Introduction to Repositories**
   [https://maven.apache.org/guides/introduction/introduction-to-repositories.html](https://maven.apache.org/guides/introduction/introduction-to-repositories.html)
5. **Settings Reference (`~/.m2/settings.xml`)**
   [https://maven.apache.org/settings.html](https://maven.apache.org/settings.html)

---

Here’s the same compact pair, now with **credentials** in `settings.xml` (safe via env vars). Copy-paste as is:

```xml
<!-- ~/.m2/settings.xml (mirror + credentials) -->
<mirrors>
    <mirror>
      <id>corp-group</id>
      <mirrorOf>*</mirrorOf>
      <url>https://nexus.example.com/repository/maven-group</url>
    </mirror>
  </mirrors>

  <!-- Credentials for deploy (ids MUST match POM <distributionManagement>) -->
  <servers>
    <server>
      <id>releases</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
    <server>
      <id>snapshots</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
```

```xml
<!-- pom.xml (deploy targets) -->
<distributionManagement>
  <repository>
    <id>releases</id>
    <url>https://nexus.example.com/repository/maven-releases</url>
  </repository>
  <snapshotRepository>
    <id>snapshots</id>
    <url>https://nexus.example.com/repository/maven-snapshots</url>
  </snapshotRepository>
</distributionManagement>
```

> Note: keep secrets only in `settings.xml` (or Jenkins “Config File Provider”), never in the POM or repo.




