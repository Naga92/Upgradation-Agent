# Copilot Instructions — Java Modernization (Team Standards)

These are repo-level guardrails for any GitHub Copilot session (VS Code or IntelliJ),
including the "GitHub Copilot app modernization – upgrade for Java" tool. Apply them on
every upgrade task in this repository.

## Target architecture (where we are going)
- Java: OpenJDK 21 (LTS).
- Framework: Spring Boot 4.x (built on Spring Framework 7 and Jakarta EE 11).
- Namespace: use `jakarta.*` everywhere. No `javax.*` may remain after the upgrade.
- Build tool: keep the project's existing build tool. This service uses Maven — do NOT
  switch between Maven and Gradle.
- Expect a dependency cascade from the Spring Boot 4 BOM (Bill of Materials = the curated
  set of dependency versions Spring manages): Hibernate 7, Jackson 3, Tomcat 11, Spring
  Security 7; JUnit 4 and Undertow are removed. Plan for these rather than fighting them.

## Staging rules (never skip intermediate states)
Identify which track a service is on before proposing changes.
- Track A (this pilot): Java 17 → 21, Spring Boot 3.x → 4.x. Clear 3.x deprecations, step
  to 3.5, then 4.0; bump Java to 21.
- Track B: Java 8/older → 21, Spring Boot 1.x/2.x → 4.x. Stage it: (1) JDK to 17 first;
  (2) Spring Boot to 3.x first — this is where the javax→jakarta rename happens;
  (3) settle on 3.5 and clear every deprecation; (4) then 4.0; (5) then JDK 17 → 21.
- HARD RULE: never attempt "Spring Boot 2.x → 4.0" or "Java 8 → 21" in one hop. Always
  route through the intermediate versions.

## How changes must be made
- Determinism first: prefer OpenRewrite recipes / the app modernization tool (which runs
  them) over hand-written or free-form AI edits for structural changes. Use AI only for the
  leftover residue and the build/fix loop. Reference recipes (confirm exact IDs and versions
  in the OpenRewrite catalog before relying on them):
  - Spring Boot 3.x → 4.0:  org.openrewrite.java.spring.boot4.UpgradeSpringBoot_4_0
  - Spring Boot → 3.5:      org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_5
  - Java 21:                org.openrewrite.java.migrate.UpgradeToJava21
- No regressions: the build and existing tests must stay green at every step. Tests are the
  safety net. Never delete or weaken a test just to make the build pass.

## Approval policy (human-in-the-loop)
- Surface every side-effecting step for human approval BEFORE doing it: dependency/version
  bumps, edits to pom.xml, file edits, and build runs. Show what will change and why.
- Work in small, reviewable increments with clear commit messages. Do not blanket-apply.

## Security / CVE policy
- Resolve known CVEs (publicly catalogued security vulnerabilities) as part of the upgrade.
- NEVER downgrade a dependency or pin it to a known-vulnerable version to make the build pass.
- Treat the tool's automated CVE remediation as a first pass, not the final word; a human
  reviews it. Pair with Dependabot and the CI vulnerability gate for ongoing protection.

## Hard stop
- If a dependency has no Java-21- or Spring-Boot-4-compatible version, STOP and escalate to a
  human architect. Do not force a broken dependency or work around it with a vulnerable/
  downgraded pin.

## Definition of done (per service)
- Builds green on Java 21 + Spring Boot 4.x; all existing tests pass (0 regressions).
- No `javax.*` imports remain.
- No unresolved high/critical CVEs.
- Changes reviewed and approved by a human; notable fixes captured in the remediation ledger.