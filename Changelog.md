# Changes to SUSE 15 CIS

# Based on CIS v2.0.1

# 2026 July
- added new vars for 1.1.1.9
  - suse15is_remove_kernel_discovery_script: false
  - suse15cis_temp_exec_location: /var
- removed var not used
- linting
- vars moved to subtask in block

# 2026 June — Molecule container testing

- Rewrote `vars/is_container.yml` with valid rule toggles from `defaults/main.yml` (195 container exclusions; removed stale pre-2.0.1 toggle names)
- Container vars: `create_benchmark_facts: false`, `suse15cis_gui: false`, `suse15cis_firewall_package: "none"`
- `molecule/default/converge.yml` simplified — only `system_is_container: true` and role include; all control skips in `is_container.yml`
- `molecule/default/Dockerfile.j2` — added CLI utilities (`gawk`, `grep`, `findutils`, `procps`, `util-linux`, `shadow`, `cron`, etc.)
- Added `tasks/verify.yml` for Molecule verify playbook
- `molecule test -s default` passes (converge, idempotence, verify) on `opensuse/leap:15.6`
- workflow tidy up
- removed unused goss variables
- 2.1.x service and pkgs logic vars now in vars/main.yml enabled a more agnostic OS approach going forward

# 2026 May QA Pass
- Added set -o pipefail to 45 ansible.builtin.shell tasks missing it (both block and inline format);
- Added args: executable: "{{ suse15cis_shell_executable }}"
- Synced LE_audit_setup.yml, post_remediation_audit.yml, pre_remediation_audit.yml from Common (preserving git-core package name)
- Synced GitHub workflows; removed export_badges_public.yml; added add_repo_issue_to_gh_project.yml
- Added update_audit_template: false and default_shell: /bin/bash to vars/main.yml
- Added missing defaults: suse15cis_passwd_complex_option, suse15cis_passwd_quality_enforce_root_value
- Fixed copy-paste errors for tags
- Consistent var naming
- Fixed prelim.yml: added failed_when: false to awk exit-count tasks and login.defs grep tasks
- Added missing prelim_journald_conf_files find task (referenced by 6.2.2.x but not defined)
- Standards around symbolic mode entries
- Removed duplicate Changelog.md
- Added listen to handlers
- Added handlers/main.yml: new Reload sysctl handler running sysctl --system
- Removed rules not required
- Task titles aligned
- Style issues
- Removed upcoming deprecation warning for ansible core modules - Use `ansible_facts["fact_name"]` (no `ansible_` prefix) instead.
- Many Improvements for logic and conditionals
- Moved many prelim tasks next to the control
- Issues with PAM not holding use_authtok in pw-history changed control 5.3.2.3.3 to make more reliable and idempotent

# 2026 Feb Updates
- prelim tags updates
- update vars with company_title: 'MindPoint Group - A Tyto Athene Company'
- Update GH workflow: rollback schedule on export_badges_priv
- QA Fixes for 1.5.x and 1.3.x

# 2026 Jan Update
- ansible-lint config updated (removed deprecated parseable option)
- molecule files updated (renamed variable to current_role_name to avoid read-only variable warning)
- spelling corrections across multiple files (CONTRIBUTING.rst, vars/is_container.yml, tasks/section_6/cis_6.2.3.1.x.yml, defaults/main.yml, templates/ansible_vars_goss.yml.j2)

# 2025 Oct update
audit processes updated and max-concurrent option added
auditd template update
benchmark_version used audit template
workflow updates
Readme updated

thanks to @MaWahli
#11
#12
#13
6.2.1.2 - improved testing logic and alerting if issues found

# 2025 June
pre-commit updates
audit alignment
workflow linting
7.2.7 and 7.2.9 updates and improvements
typos updated

# Initial 2.0.1
