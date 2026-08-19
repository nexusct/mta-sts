# MTA-STS Policy Host

This repository hosts the MTA-STS (Mail Transfer Agent Strict Transport Security) policy for the `nexusct.com` domain. MTA-STS is a security standard ([RFC 8461](https://tools.ietf.org/html/rfc8461)) that enables mail servers to declare their ability to receive TLS-secured connections and to specify whether sending mail servers should refuse to deliver messages if a secure connection cannot be established.

## What is MTA-STS?

MTA-STS helps protect email in transit by:
- Preventing downgrade attacks where an attacker forces plaintext email delivery
- Ensuring mail servers communicate over encrypted TLS connections
- Providing a mechanism for mail servers to advertise their security capabilities

## Repository Structure

```
.
├── .well-known/
│   └── mta-sts.txt       # MTA-STS policy file
├── CNAME                 # GitHub Pages custom domain configuration
├── .nojekyll             # Disables Jekyll processing on GitHub Pages
└── README.md             # This file
```

## Current Policy

The MTA-STS policy is located at `.well-known/mta-sts.txt` and must be served over HTTPS at:
```
https://mta-sts.nexusct.com/.well-known/mta-sts.txt
```

**Current configuration:**
- **Mode**: `testing` - Reports failures but does not block non-compliant deliveries
- **MX Pattern**: `*.mail.protection.outlook.com` - Microsoft 365 mail servers
- **Max Age**: `604800` seconds (7 days) - Policy cache duration

## Deployment

This repository is deployed using **GitHub Pages** and serves the MTA-STS policy file over HTTPS.

### GitHub Pages Setup

1. The repository is configured to publish from the `main` branch
2. The `CNAME` file configures the custom domain: `mta-sts.nexusct.com`
3. The `.nojekyll` file prevents Jekyll from processing the `.well-known` directory

### Required DNS Configuration

For MTA-STS to work, two DNS records are required:

#### 1. CNAME Record for Policy Host
Point the MTA-STS subdomain to GitHub Pages:
```
mta-sts.nexusct.com  CNAME  nexusct.github.io
```

#### 2. TXT Record for MTA-STS
Add a DNS TXT record at `_mta-sts.nexusct.com` to advertise the policy:
```
_mta-sts.nexusct.com  TXT  "v=STSv1; id=YYYYMMDDHHMMSS"
```

**Important**: Update the `id` value whenever the policy changes. The `id` is typically a timestamp (e.g., `20260819120000`) and signals to mail servers that they should fetch a fresh policy.

### SSL/TLS Certificate

GitHub Pages automatically provides a free SSL/TLS certificate via Let's Encrypt for the custom domain. Ensure HTTPS is enabled in the repository settings under **Settings → Pages → Enforce HTTPS**.

## Making Policy Changes

To update the MTA-STS policy:

1. Edit `.well-known/mta-sts.txt` with your changes
2. Commit and push to the `main` branch
3. **Update the DNS TXT record** `_mta-sts._mta-sts.nexusct.com` with a new `id` value
4. Wait for DNS propagation and GitHub Pages deployment (typically < 5 minutes)

### Policy Modes

- **`testing`** (current): Reports policy violations but does not block delivery
- **`enforce`**: Blocks email delivery if TLS requirements are not met
- **`none`**: Disables the policy

**Recommendation**: Keep the policy in `testing` mode for at least 2-4 weeks while monitoring for issues, then switch to `enforce` mode.

## Verification

Test the MTA-STS policy using:

1. **Web browser**: Visit `https://mta-sts.nexusct.com/.well-known/mta-sts.txt`
2. **Command line**:
   ```bash
   curl https://mta-sts.nexusct.com/.well-known/mta-sts.txt
   ```
3. **MTA-STS validators**:
   - [Hardenize](https://www.hardenize.com/)
   - [MXToolbox MTA-STS Lookup](https://mxtoolbox.com/mta-sts.aspx)

## Security

See [SECURITY.md](SECURITY.md) for information on reporting security vulnerabilities.

## References

- [RFC 8461: SMTP MTA Strict Transport Security (MTA-STS)](https://tools.ietf.org/html/rfc8461)
- [Google's MTA-STS Guide](https://www.google.com/gmail/postmaster-tools/mta-sts/)
- [MTA-STS Specification](https://datatracker.ietf.org/doc/html/rfc8461)