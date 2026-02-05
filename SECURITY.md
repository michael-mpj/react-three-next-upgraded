# Security Policy

## Reporting a Vulnerability

**Please do not publicly disclose security vulnerabilities.** We take security seriously and will address issues promptly and responsibly.

### Reporting Process

1. **Email**: Send details to [michaeljoseph.mpj@gmail.com](mailto:michaeljoseph.mpj@gmail.com)
   - Include a description of the vulnerability
   - Steps to reproduce the issue
   - Potential impact
   - Any possible fixes you've identified

2. **GitHub Security Advisory** (if you have a GitHub account):
   - Use [GitHub's private vulnerability reporting](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)
   - Search for our repository: `michael-mpj/react-three-next-upgraded`

### Response Timeline

We will acknowledge your report within **48 hours** and:
- Verify the vulnerability
- Assess severity and impact
- Develop and test a fix
- Release a security patch

For critical vulnerabilities, we aim to release patches within **7 days** of verification.

## Supported Versions

| Version | Supported          | Release Date | EOL Date   |
| ------- | ------------------ | ------------ | ---------- |
| 1.x     | ✅ Yes             | 2024-Q4      | 2026-Q4    |
| 0.x     | ⚠️ Limited Support | 2024-Q3      | 2025-Q1    |

Security updates are provided for the latest version only. We recommend always updating to the latest stable release.

## Security Best Practices

### For Users

1. **Keep Dependencies Updated**
   ```bash
   npm audit
   npm update
   ```

2. **Enable GitHub Dependabot**
   - We use Dependabot to automatically check for vulnerable dependencies
   - Enable notifications for your fork

3. **Secure Your Environment**
   - Use `.env.local` for sensitive data (never commit to git)
   - Review `.env.example` for required variables
   - Use environment-specific secrets in CI/CD

4. **Regular Audits**
   ```bash
   npm audit --audit-level=moderate
   ```

### For Contributors

1. **Code Review**
   - All code undergoes peer review before merging
   - Security checks run automatically via GitHub Actions

2. **Dependency Management**
   - Only use trusted, well-maintained packages
   - Report suspicious packages
   - Regularly audit transitive dependencies

3. **Secure Coding**
   - Validate user input
   - Use security headers
   - Sanitize outputs
   - Follow OWASP guidelines

4. **Secret Management**
   - Never commit secrets, API keys, or credentials
   - Use GitHub Secrets for CI/CD
   - Rotate credentials regularly

## Automated Security Checks

Our CI/CD pipeline includes:

- **npm audit**: Checks for known vulnerabilities in dependencies
- **CodeQL Analysis**: Static analysis for code security issues
- **GitHub Dependabot**: Automatic dependency update PRs
- **Branch Protection**: Requires PR reviews and passing checks

### Running Security Checks Locally

```bash
# Check for vulnerable dependencies
npm audit

# Run security scan with npm
npm audit fix --force

# View detailed vulnerability info
npm audit --json
```

## Security Headers & Configuration

This project follows security best practices:

- **Content Security Policy**: Configured in Next.js headers
- **X-Frame-Options**: Prevents clickjacking
- **X-Content-Type-Options**: Prevents MIME type sniffing
- **Referrer-Policy**: Controls referrer information

## Dependency Security

### Regular Updates

Dependencies are updated on a weekly schedule via Dependabot:
- Minor and patch updates: Auto-merged if tests pass
- Major updates: Require manual review and testing

### Audit Level

We use `moderate` audit level, which flags:
- Critical vulnerabilities: ❌ Always block
- High vulnerabilities: ❌ Always block
- Moderate vulnerabilities: ⚠️ Reviewed before merge
- Low vulnerabilities: ℹ️ Logged but don't block

## Secrets & Environment Variables

### Sensitive Data

Never commit:
- API keys or tokens
- Database credentials
- Private encryption keys
- Personal information

### Using `.env.local`

```bash
# Copy the example file
cp .env.example .env.local

# Add your actual secrets
# .env.local is in .gitignore and won't be committed
```

### GitHub Actions Secrets

For CI/CD workflows:
1. Go to Settings → Secrets and variables → Actions
2. Add repository secrets (e.g., `DEPLOY_TOKEN`, `API_KEY`)
3. Reference in workflows: `${{ secrets.SECRET_NAME }}`

## Third-Party Security

### Vercel Deployment

If using Vercel:
- Secrets are encrypted and isolated per deployment
- Never expose secrets in build output
- Use Vercel's environment variables feature

### External Services

Be cautious when integrating:
- Verify SSL certificates
- Use HTTPS for all external APIs
- Validate API responses
- Implement rate limiting

## Disclosure

Once a fix is released, we will:

1. **Credit** the reporter (unless they prefer anonymity)
2. **Publish** a security advisory on GitHub
3. **Announce** the fix in release notes
4. **Provide** migration guidance if needed

## References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE/SANS Top 25](https://cwe.mitre.org/top25/archive/2023/2023.html)
- [Node.js Security Best Practices](https://nodejs.org/en/docs/guides/nodejs-security/)
- [Next.js Security](https://nextjs.org/docs/advanced-features/security-headers)

## Questions?

For security questions that aren't vulnerability reports:
- Start a [GitHub Discussion](https://github.com/michael-mpj/react-three-next-upgraded/discussions)
- Check existing [Issues](https://github.com/michael-mpj/react-three-next-upgraded/issues)
- Review this security policy

---

Thank you for helping keep this project secure! 🔒
