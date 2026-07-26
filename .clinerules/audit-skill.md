---
name: audit-skill
description: Performs a comprehensive 5-step security audit on external AI skills, GitHub repositories, and associated scripts prior to local installation or execution.
---

# Audit Skill

Use this skill to perform a zero-trust security audit on third-party GitHub AI skills, external scripts, dependencies, and command integrations before installation.

## Required Dependencies

* **security-auditor Skill:** Required for workspace permission, schema, and path boundary inspection.
  * Source Repository: sickn33/agentic-awesome-skills
  * Install Command: npx skills add sickn33/agentic-awesome-skills --skill security-auditor
* **security-audit Skill:** Required for SAST scanning, dependency evaluation, and STRIDE threat modeling.
  * Source Repository: sickn33/agentic-awesome-skills
  * Install Command: npx skills add sickn33/agentic-awesome-skills --skill security-audit
* **smartbrain-skill-auditor Tool:** External zero-dependency static analyzer for ghost prompts and malicious code execution patterns.
  * Source Repository: https://github.com/smartbrainactivity/smartbrain-skill-auditor
  * Setup Command: git clone https://github.com/smartbrainactivity/smartbrain-skill-auditor.git ~/tools/smartbrain-skill-auditor

## Instructions

### Step 1: Pre-Install Static Audit and Multi-Scanner Execution
1. Zero-Trust Rule: Never execute "npx skills add" or register untrusted skills directly into active project environments.
2. Isolation: Clone the target repository into a temporary directory using the target skill name as the identifier:
   git clone --depth 1 <repository-url> /tmp/<skillname>
3. Primary Static Audit (SmartBrain Scanner): Run the SmartBrain static analyzer against the unvetted directory:
   node ~/tools/smartbrain-skill-auditor/bin/audit.js /tmp/<skillname>
4. Secondary Package Audit (NPM Skill-Audit): Run the package scanner against the unvetted directory:
   npx skill-audit scan /tmp/<skillname>

### Step 2: Static Verification Checkpoints
1. Prompt Injection Checks: Verify SKILL.md contains zero hidden system overrides, zero-width characters, or prompt escape sequences.
2. Code Execution Checks: Inspect all .py, .js, .ts, and .sh files for eval(), exec(), vm.runInNewContext, or raw shell execution.
3. Network and Exfiltration Checks: Verify no unexpected outbound HTTP requests, WebSockets, or telemetry commands exist.
4. Dependency Checks: Confirm no secondary repositories are pulled dynamically or hidden scripts downloaded at runtime.

### Step 3: Mandatory Internal Auditing Skills Execution
1. Registration Gate: Proceed with skill registration ONLY if both static scanners in Step 1 report zero critical or high-severity vulnerabilities:
   npx skills add <repository-name> --skill <skillname>
2. Mandatory Skill Invocation 1 (security-auditor): Always invoke security-auditor immediately following registration:
   /security-auditor inspect --target /path/to/<skillname>
   - Verify script permissions, file path boundaries, schema inputs, and workspace constraint compliance.
3. Mandatory Skill Invocation 2 (security-audit): Always invoke security-audit for deep threat modeling:
   /security-audit run --depth full --target /path/to/<skillname>
   - Perform deep SAST analysis, audit third-party dependency chains, and construct STRIDE threat models against workspace assets.

### Step 4: Sandbox and Isolated Runtime Containment
1. Least Privilege: Execute newly audited skills exclusively inside an isolated Docker container or restricted non-root user profile.
2. Access Boundaries: Enforce strict write-access restrictions on sensitive workspace files (.env, credentials, configuration files).

### Step 5: Security Audit Report Generation
1. Read Template: Read the base report structure from assets/audit-report_skillname_TEMPLATE.md relative to this skill folder.
2. Variable Substitution: Replace all instances of {skillname} in the template with the actual target skill name being audited.
3. Synthesize Findings: Aggregate findings from all four audit layers (smartbrain-skill-auditor, npx skill-audit, security-auditor, and security-audit).
4. Output File: Save the completed report to audit-report_{skillname}.md in the root workspace directory.
5. Deployment Clearance: Never close security tickets or authorize production execution until a PASSED status is recorded in audit-report_{skillname}.md.