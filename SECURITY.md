# Security Policy

## Reporting Security Vulnerabilities

If you discover a security vulnerability in this repository, please report it responsibly:

### How to Report

**Do NOT open a public GitHub issue for security vulnerabilities.**

Instead, please report security issues to:
- **Email**: Create a security advisory via GitHub's private vulnerability reporting feature
- **GitHub Security Advisories**: Go to the [Security tab](../../security/advisories) and click "Report a vulnerability"

### What to Include

When reporting a vulnerability, please include:
- A description of the vulnerability
- Steps to reproduce the issue
- Potential impact
- Any suggested fixes (optional)

We will acknowledge receipt of your report within 48 hours and provide a more detailed response within 5 business days.

## Security Considerations for MTA-STS

### Policy Configuration

This repository hosts an MTA-STS policy file that affects email security for the `nexusct.com` domain.

**Critical considerations:**

1. **Policy Mode**
   - Currently set to `testing` mode
   - Only switch to `enforce` mode after thorough testing
   - Incorrect configuration in `enforce` mode can block legitimate email

2. **MX Records**
   - The MX pattern `*.mail.protection.outlook.com` must match your actual mail server configuration
   - Mismatched MX patterns can cause email delivery failures

3. **Max Age**
   - Set to 7 days (604800 seconds)
   - Longer durations provide better security but reduce flexibility for changes
   - Do not set below 1 day (86400 seconds) in production

### Repository Security Best Practices

#### 1. No Secrets in Git

**Never commit:**
- API keys or tokens
- Passwords or credentials
- Private keys or certificates
- Email addresses (except for public contact)
- Internal hostnames or IP addresses

If you accidentally commit a secret:
1. **Immediately rotate/revoke** the exposed credential
2. Contact the repository maintainers
3. Consider the secret permanently compromised (git history is public)

#### 2. Branch Protection

The `main` branch should have:
- Required pull request reviews
- Status checks before merging
- No force pushes allowed
- No deletion allowed

#### 3. HTTPS Only

- This repository **must** be served over HTTPS
- GitHub Pages provides automatic SSL/TLS certificates
- Never disable HTTPS enforcement

#### 4. DNS Security

- Use DNSSEC if your DNS provider supports it
- Regularly audit DNS records for unauthorized changes
- Restrict access to DNS management console

#### 5. Policy File Integrity

The `.well-known/mta-sts.txt` file:
- Must be valid UTF-8 text
- Should not contain comments or extra whitespace
- Must follow the exact format specified in RFC 8461

### Monitoring and Incident Response

**Regular checks:**
- Verify the policy is accessible at `https://mta-sts.nexusct.com/.well-known/mta-sts.txt`
- Monitor certificate expiration (GitHub Pages handles renewal)
- Review GitHub Pages deployment status
- Check DNS TXT record is current

**If the policy becomes inaccessible:**
1. Email servers will continue using cached policies (up to `max_age`)
2. After cache expiration, email delivery may fail or become unencrypted
3. Restore service within the `max_age` window to avoid disruption

## Supported Versions

This repository uses a simple static file hosting model. Security updates apply to:
- The current `main` branch
- Active feature branches

Historical versions in git history are not patched.

## Security Update Process

When a security issue is identified:
1. A fix is developed privately
2. The fix is reviewed and tested
3. The fix is merged to `main`
4. DNS TXT record is updated with a new policy ID
5. A security advisory is published (if appropriate)

## Additional Resources

- [RFC 8461: MTA-STS Specification](https://tools.ietf.org/html/rfc8461)
- [GitHub Pages Security Best Practices](https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https)
- [OWASP Email Security Guidelines](https://cheatsheetseries.owasp.org/cheatsheets/Email_Security_Cheat_Sheet.html)

## Disclaimer

This MTA-STS policy host is provided as-is. The repository maintainers are not responsible for email delivery failures resulting from misconfiguration or policy changes made by authorized users.
