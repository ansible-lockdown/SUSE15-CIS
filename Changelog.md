# Changes to SUSE 15 CIS

# Based on CIS v2.0.1

# 2026 August - QA pass fixes

- tasks/remount_tmp.yml rewritten
- defaults split into defaults/main/main.yml and defaults/main/audit.yml
- CONTRIBUTING.rst replaced with the canonical CONTRIBUTING.md, README Contributing
- README emoji stripped

Defects found by the 2026-08-27 QA pass, several proven on a real openSUSE Leap 15.6 host.

- 5.3.2.2.1: pam-config -a -cracklib used a single dash, which exits 1 and aborted the
  playbook on any host where cracklib was not already configured
- 7.1.13: find -perm \( -02000 or -04000 \) made find treat the paren as its mode
  argument, so the SUID/SGID review returned nothing and passed vacuously on every host
- 5.4.3.3: single-quoted '#\\1' is not a backreference, so every umask line in
  /etc/profile.d/*.sh was overwritten with the literal text
- 3.2.1: the dccp control wrote "blacklist cramfs"
- 1.7.4: failed_when rejected state "file", so the control failed wherever /etc/motd exists
- 1.2.1.1: stray quotes around the shell body made it exit 127, so the GPG key check
  never ran
- 4.1.1: the module hardcoded nftables while looping three services, so ufw and iptables
  were never masked
- 6.2.1.2: failed_when [0, 257] is unreachable above 255 and rejected the compliant case,
  where a pipefail grep with no match exits 1
- 6.2.1.3: targeted /etc/systemd/journal.conf, and read .rc from a lineinfile result
- 6.3.1.1: a when-condition was sitting in the tags list
- 1.2.1.2/1.2.1.3: the shared repo discovery task had no tags, so any tagged run skipped it
  and both controls failed on an undefined variable
- 5.3.2.2.1 stripped dictcheck unconditionally with no path to write it back; added the
  missing not-pam-config template task and suse15cis_passwd_dictcheck_file
- 7.2.6 referenced discovered_user_username_check; the register is discovered_username_check
- 3.1.x/3.2.x: single-quoted (\\s|$) is a literal backslash-s, so the regexp never matched
  the line it wrote and a duplicate was appended on every run
- remount_tmp set a fact named after a handler, so the required reboot never happened
- 1.4.1: the grub file carrying the password hash was world-readable
- ansible_facts dot notation converted to bracket notation
- Section 1.8 was gated on the Debian package name gdm3, so none of the GNOME Display
  Manager controls ever ran on SLES; the section heading also read DNOME
- 5.3.2.2.x: item != <var> compared a find result dict to a string in six controls, so
  each one stripped its own setting; the 5.3.2.2.x comment block was a position out
  against the benchmark
- Stale section comments corrected and STIG galaxy tags removed from a CIS role

# 2026 August - Alignment with CIS v2.0.1

- Task titles resynced to the v2.0.1 benchmark (13 files): 1.1.1.8, 1.1.2.3.1,
  1.1.2.4.1, 1.1.2.5.1, 1.1.2.6.1, 1.1.2.7.1, 1.6.2, 2.4.1.2-2.4.1.7, 5.1.1,
  5.1.2, 5.1.3, 5.1.18, 5.4.2.4, 6.2.3.1.2, 6.2.3.7, 6.2.4.1
- tasks/section_6/cis_6.3.4.x.yml: rule tags rule_6.3_4_3 and rule_6.3_4_4 used
  underscores instead of dots, so the audit-log discovery task was not selected by
  --tags rule_6.3.4.3 or rule_6.3.4.4 and the loop those controls depend on had no
  files to act on
- Level tags corrected against the v2.0.1 Profile Applicability on 8 controls:
  1.8.8, 1.8.9, 2.2.2, 5.1.11, 5.3.2.1.3, 5.4.1.2, 6.2.2.2, 6.3.3.15

# 2026 July — QA pass: goss, handler, lint fixes

- handlers/main.yml: remount_tmp handler changed to import_tasks (was include_tasks)
- vars/audit.yml: goss binary URL updated goss-org -> krameff
- vars/audit.yml: goss version updated v0.4.8 -> v0.5.0 with new checksums
- vars/audit.yml: ansible_facts dot notation converted to bracket notation
- tasks/section_7/cis_7.1.x.yml: typo permissionss -> permissions (7.1.4 tag)
- tasks/section_7/cis_7.1.x.yml: task key order fixed on 7.1.10 (loop before failed_when/register)
- .github/workflows: actions/checkout updated v6.0.2 -> v7.0.0
- .gitignore: added qa_report.md and AL_QA_Report* patterns
- README.md: goss URL updated goss-org -> krameff, size 12MB -> 16MB

# 2026 July
- added new vars for 1.1.1.9
  - suse15is_remove_kernel_discovery_script: false
  - suse15cis_temp_exec_location: /var
- removed var not used
- linting
- vars moved to subtask in block
- molecule: force `fetch_audit_output: true` and `audit_output_destination` via `set_fact` in the
  default `converge.yml` pre_tasks so the audit JSONs fetch to the controller `_temp_fetched_audits/`.
  `vars/audit.yml` is loaded via `include_vars` (precedence 17) and otherwise clobbers those host_vars;
  removed the now-dead host_vars from `molecule/default/molecule.yml` and updated the QuickStart.
- defaults/main.yml: added `# pragma: allowlist secret` to `suse15cis_passwd_complex_option` and
  `suse15cis_passwd_quality_enforce_root_value` to clear detect-secrets false positives

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
