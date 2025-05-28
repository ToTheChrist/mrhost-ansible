# mrhost Ansible Automation

This repository contains Ansible playbooks and roles for automating DirectAdmin user, mailbox, and DKIM provisioning, as well as Cloudflare DNS management.

---

## Features

- **DirectAdmin user management**: Create users, set passwords, and manage domains.
- **Mailbox automation**: Create and manage email accounts (idempotent).
- **DKIM management**: Enable DKIM only if not already enabled.
- **Cloudflare integration**: Manage DNS records via Cloudflare API.
- **Idempotent**: Safe to re-run; only applies changes when needed.

---

## Usage

1. **Clone the repository**

   ```sh
   git clone <your-repo-url>
   cd ansible
   ```

2. **Install requirements**

   - Install Ansible (2.12+ recommended)
   - Install required collections:
     ```sh
     ansible-galaxy collection install community.general
     ```

3. **Configure your inventory and client files**

   - Edit `clients/client01.yml` or add your own client files.
   - Set variables as needed.

4. **Encrypt your secrets**

   - Store secrets in `group_vars/all.yml` and encrypt with Ansible Vault:
     ```sh
     ansible-vault encrypt group_vars/all.yml
     ```

5. **Run the playbook**

   ```sh
   ansible-playbook -i inventory/hosts playbooks/fullonboard.yml --ask-vault-pass
   ```

---

## Security

- **Never commit secrets in plain text.**
- Sensitive files are excluded via `.gitignore`:
  - `playbooks/output/`
  - `group_vars/all.yml`
- Use Ansible Vault for all credentials and API keys.

---

## Structure

```
ansible/
├── clients/                # Per-client variable files
├── group_vars/             # Group variables (vault-encrypted)
├── inventory/              # Inventory files
├── playbooks/              # Playbooks and output
├── roles/
│   ├── cloudflare/
│   └── directadmin/
└── .gitignore
```

---

## Tips

- To view or edit encrypted secrets:
  ```sh
  ansible-vault view group_vars/all.yml
  ansible-vault edit group_vars/all.yml
  ```
- To add new features, follow the idempotent pattern: query state, compare, and only change if needed.

---

## License

MIT License

---

**Questions or contributions welcome!**