# GitHub Security Guide: Sensitive Data Management

This guide documents best practices for keeping sensitive data out of version control and details the risks associated with accidental credential leakage.

## 1. What Information Must Never Be Pushed
The following types of sensitive information must **never** be committed to Git or pushed to any remote repository:
- **API Keys & Tokens**: Secret keys for services like OpenAI, AWS, Stripe, Google Cloud, or GitHub.
- **Private Cryptographic Keys**: SSH keys (`id_rsa`), SSL certificates, and PGP private keys.
- **Database Credentials**: Connection strings, passwords, usernames, and database host addresses.
- **Environment Variables**: Local configuration files containing system paths, ports, or credentials (e.g., `.env` files).
- **Personally Identifiable Information (PII)**: User emails, phone numbers, addresses, and other personal data.

## 2. Security Investigation & Vulnerability Analysis

### Risks of Committed Secrets
When secrets are committed to Git:
1. **Automated Scraping**: Attackers deploy automated bots that constantly scan public GitHub commits for exposed secrets (like AWS keys). AWS keys are often exploited within seconds of being pushed, resulting in massive unauthorized server bills.
2. **Persistent Git History**: Even if you delete a secret in a subsequent commit, it remains in the Git commit history. Removing it completely requires rewriting history using tools like `git-filter-repo` or BFG Repo-Cleaner.
3. **Data Leaks & Breaches**: Database passwords exposed in repository files can allow attackers to access, steal, or delete production data.

### Public vs. Private Repositories
- **Public Repository**: Accessible to anyone on the internet. Anyone can clone, view, and inspect the code and commit history. Extremely high risk for secret exposure.
- **Private Repository**: Restricted to authorized users/collaborators. While safer than public repos, they are **still insecure** for storing secrets because:
  - Collaborators may have varying levels of security hygiene.
  - The repository could be made public in the future.
  - Third-party integrations or security breaches of collaborator accounts could expose the source code.

## 3. Best Practices for Protection
- **Use Environment Variables**: Load secrets from `.env` files into environment variables at runtime instead of hardcoding them in source code.
- **Exclude Config Files**: Keep `.env` and other local config files out of Git using a `.gitignore` file.
- **Use Template Files**: Commit a template file (e.g., `.env.example`) showing the structure of the variables without containing any actual secrets.
