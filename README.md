mrhost Ansible Automation

This repository automates full lifecycle management of web hosting clients using DirectAdmin, Cloudflare, and Ansible.

Built to safely manage 200+ domains, mailboxes, and DNS records — with smart validations, API-driven provisioning, and GitHub Actions-based CI for automatic deployment.

🚀 Features
DirectAdmin Automation

User creation (only if missing)

Mailbox creation and validation

DKIM enablement

Safety checks for duplicates

Cloudflare Integration

Domain (zone) creation via API

A, MX, TXT, and custom DNS record management

Skips or updates only what's necessary

Built-in protection from duplicate or bad records

Smart Safety System

Checks for existing users, domains, mailboxes, and DNS before creation

Prevents duplicate zones or records

Optional deletion logic for DNS, mail, or domains

CI/CD with GitHub Actions

Auto-runs on changes to clients/*.yml

Secure vault integration for secrets

Scalable automation across any number of domains

Idempotent by design

All playbooks can be re-run safely

Makes no changes unless required

📦 Structure

ansible/
├── clients/                # Per-client domain + mail + DNS config (YAML)
├── group_vars/             # Shared variables (vault-encrypted)
├── inventory/              # Hosts file (localhost usage)
├── playbooks/              # Main playbook (full_onboarding.yml)
├── roles/
│   ├── cloudflare/         # DNS and zone logic
│   └── directadmin/        # User/mail/DKIM logic
└── .github/workflows/      # GitHub Actions auto-provisioning pipeline
🛠️ Setup & Usage
1. Clone the repo

git clone git@github.com:ToTheChrist/mrhost-ansible.git
cd ansible
2. Install requirements

pip install ansible
ansible-galaxy collection install community.general
3. Add your first client
Create a file like clients/client01.yml:


username: client01
domain: client01domain.com
email: info@client01domain.com
create_email_accounts: true
email_accounts:
  - user: info

cloudflare_dns_records:
  - type: A
    name: www
    value: 123.45.67.89
    ttl: 3600
    proxied: true
  - type: MX
    name: "@"
    value: mail.client01.com
    ttl: 3600
    priority: 10
  - type: TXT
    name: "@"
    value: "v=spf1 include:_spf.example.com ~all"
    ttl: 3600
4. Encrypt your secrets

ansible-vault encrypt group_vars/all.yml
Add your:

cloudflare_api_token

directadmin_api_key

shared_ip, etc.

5. Run it manually

ansible-playbook -i inventory/hosts playbooks/full_onboarding.yml --ask-vault-pass
🤖 CI/CD: Auto-provision on commit
Your GitHub Actions workflow is already set up:


.github/workflows/provision-client.yml
Whenever you git push a new client file (like clients/client02.yml), GitHub will:

Run your Ansible playbook

Provision the domain

Add DNS + mailboxes safely

Secrets like Vault password are securely passed via GitHub Secrets (ANSIBLE_VAULT_PASS).

🧼 Safe Deletion (Optional)
In your clientXX.yml, set:

delete_emails: true
email_accounts_to_delete:
  - user: info

cloudflare_dns_records:
  - type: A
    name: www
    value: 123.45.67.89
    ttl: 3600
    state: absent
Or:


delete_domain: true
Ansible will detect and remove them carefully.

🛡 Branch Protection Recommended
Make sure to protect your main branch:

✅ Block force pushes

✅ Require status checks

✅ Require PRs and approval

✅ Lock deletion

Set this up under: GitHub → Settings → Rulesets

🧠 Tips
To edit Vault secrets:

ansible-vault edit group_vars/all.yml
To test a specific domain:


ansible-playbook -i inventory/hosts playbooks/fullonboard.yml --ask-vault-pass -vvv

