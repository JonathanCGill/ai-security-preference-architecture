# Security Policy

## Scope

This security policy applies to the Enterprise AI Security Framework documentation and any example code provided.

## Reporting Vulnerabilities

### In the Framework Documentation

If you identify a security issue with the guidance provided in this framework — for example, a recommendation that could lead to insecure implementations — please:

1. **Open a GitHub issue** describing the concern
2. **Label it** with `security` if you have permissions, or mention "security concern" in the title
3. **Provide details** on why the current guidance is problematic and what the impact could be

We don't require private disclosure for documentation issues, as the framework itself doesn't execute code.

### In Example Code

If you find a security vulnerability in any example code provided:

1. **Do not open a public issue** if the vulnerability could be exploited
2. **Contact the maintainers directly** via GitHub's private vulnerability reporting feature
3. **Provide** a clear description of the vulnerability, steps to reproduce, and potential impact

## Response Timeline

- **Acknowledgment** — Within 48 hours
- **Initial assessment** — Within 7 days
- **Fix or mitigation** — Depends on severity; critical issues prioritised

## Supported Versions

As a documentation framework (not software), we don't have traditional "supported versions." However:

| Version | Support Status |
|---------|---------------|
| Latest (main branch) | ✅ Actively maintained |
| Tagged releases | ✅ Security fixes backported for 12 months |
| Older versions | ❌ No support; upgrade to latest |

## Security Considerations for Implementers

If you're implementing this framework:

1. **This is guidance, not assurance** — You're responsible for validating controls in your environment
2. **Test before deploying** — Don't assume the examples work securely in your context
3. **Keep dependencies updated** — If you use tools mentioned here, keep them patched
4. **Layer your defences** — No single control is sufficient

## Acknowledgments

We appreciate responsible disclosure and will acknowledge security researchers who report valid issues (unless they prefer to remain anonymous).

---

*Enterprise AI Security Framework*
