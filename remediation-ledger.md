# Remediation Ledger — Java Modernization

Lessons-learned memory for the Java 21 / Spring Boot 4 upgrade. When a NEW build or test
failure is solved, add an entry so later services reuse the fix. Add entries via PULL REQUEST
only — never overwrite this file automatically.

## Reuse note
Generic — safe to reuse across services. Entries accumulate per team, not per service.

## How to add an entry
Copy the template, fill it in, open a PR. Keep the error signature specific enough to match.

---

## Template
### [short title]
- **Error signature:** (the key line(s) of the error message)
- **Where it applies:** (Track A / B; which version step; which kind of dependency)
- **Root cause:** (one sentence)
- **The fix:** (the exact change that resolved it)
- **Captured as formula?:** (yes/no — if the modernization tool saved it as a reusable task)

---

## Entries
<!-- First real entries will be added during the PetClinic pilot run. -->

### (example) Old bundled Gradle fails on newer JDK
- **Error signature:** `Unsupported class file major version 65` during Gradle configure
- **Where it applies:** Setup phase, any IDE that auto-probes Gradle with an old bundled version
- **Root cause:** Gradle 7.5.1 can't parse classes compiled by Java 21.
- **The fix:** Project is Maven-only; disabled IDE Gradle import
  (`"java.import.gradle.enabled": false`). Cosmetic — Maven build was always green.
- **Captured as formula?:** no