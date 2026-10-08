# Ansible Elastic Stack Logging

Ansible roles that deploy the Elastic Stack (Elasticsearch, Logstash, Kibana) for log monitoring, built as a lab on log monitoring with Infrastructure as Code.

## What this covers
- Role-based deployment with separate roles per distribution (`Ubuntu`, `Centos`)
- Adding the Elastic GPG key and 7.x apt repository (`apt_key`, `apt_repository`)
- Installing Java and dependencies, then Elasticsearch, Logstash, and Kibana
- Managing the systemd service lifecycle (daemon-reload, enable, start)

## Lab environment
- Control node: Ubuntu workstation running Ansible
- Managed nodes: one Ubuntu server and one CentOS server (VirtualBox, host-only network)

## Repository structure
```
.
├── ansible.cfg
├── inventory
├── site.yml            # pre_tasks + role plays
└── roles/
    ├── Ubuntu/tasks/main.yml
    └── Centos/tasks/main.yml
```

## Usage
```bash
ansible-playbook --ask-become-pass site.yml
```

## Verification (Ubuntu)
| Component | Check | Result |
|-----------|-------|--------|
| Elasticsearch | `curl localhost:9200` | Version 7.17.9, cluster `demo-elk` |
| Logstash | `systemctl status logstash` | active (running) |
| Kibana | `systemctl status kibana` | active (running) |

## Notes and next steps
- Verified on the Ubuntu host only. The CentOS role was not verified in this lab.
- All three components ran on a single host. Splitting Elasticsearch, Logstash, and Kibana across inventory groups would match a production layout.
- The first playbook run failed at the "ensure elasticsearch is running" task. `journalctl -xeu elasticsearch.service` is the place to look for the cause.
- Security (TLS, authentication) and Beats shippers are not configured.
