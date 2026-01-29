# Changes to SUSE 15 CIS

# Based on CIS v2.0.1

# 2026 Jan Update
- License year updated
- ansible-lint config updated - removed deprecated parseable option
- molecule files - renamed role_name variable to avoid read-only conflict
- typos corrected across multiple files
- QA linting - yamllint, ansible-lint, codespell all passing

# 2025 Oct update
- audit processes updated and max-concurrent option added
- auditd template update
- benchmark_version used audit template
- workflow updates
- Readme updated
- thanks to @MaWahli
  - #11
  - #12
  - #13
- 6.2.1.2 - improved testing logic and alerting if issues found

# 2025 June
- pre-commit updates
- audit alignment
- workflow linting
- 7.2.7 and 7.2.9 updates and improvements
- typos updated

# Initial 2.0.1
