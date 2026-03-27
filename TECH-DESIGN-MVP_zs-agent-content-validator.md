# TECH-DESIGN-MVP — `zs-agent-content-validator`

> **Document:** Technical Design (MVP) | **Version:** 1.0.0-mvp
> **Repository:** [https://github.com/zarishsphere/zs-agent-content-validator](https://github.com/zarishsphere/zs-agent-content-validator)
> **Layer:** Layer 9 | **Catalog #:** 194
> **Language:** Go 1.26.1 | **License:** Apache 2.0

---

## Technical Summary

**Clinical content validator — JSON form validation, FHIR mapping check, concept lookup.**

This document defines the **technical architecture, implementation design, complete repository tree, and acceptance criteria** for the MVP of `zs-agent-content-validator`.

---

## GitHub App Authentication Pattern

```go
// internal/github/auth.go
func NewInstallationClient(appID int64, privateKeyPEM []byte, installationID int64) (*github.Client, error) {
    itr, err := ghinstallation.New(
        http.DefaultTransport, appID, installationID, privateKeyPEM,
    )
    if err != nil {
        return nil, fmt.Errorf("create installation transport: %w", err)
    }
    return github.NewClient(&http.Client{Transport: itr}), nil
}
```

## Cloudflare Workers Deployment

```toml
# deploy/wrangler.toml
name = "zs-agent-content-validator"
main = "build/worker.js"
compatibility_date = "2026-01-01"

[triggers]
crons = ["0 9 * * 1"]   # Monday 9am (for weekly reports)

[[kv_namespaces]]
binding = "STATE"
id = "xxx"
```

## Repository Tree

```
zs-agent-content-validator/
├── README.md
├── LICENSE
├── go.mod
├── go.sum
├── .gitignore
├── .github/CODEOWNERS
├── .github/workflows/ci.yml
├── cmd/validator/main.go
├── internal/
│   ├── schema/form_validator.go
│   ├── fhir/mapping_checker.go
│   └── terminology/code_lookup.go
└── config/config.yaml
```

---


## Owners & Governance

| Role | GitHub Handle | Responsibility |
|------|--------------|----------------|
| Platform Lead | `@arwa-zarish` | Final approval, RFC votes |
| Technical Lead | `@code-and-brain` | Architecture, Go/TS review |
| DevOps Lead | `@DevOps-Ariful-Islam` | CI/CD, infra, deployment |
| Health Programs | `@BGD-Health-Program` | Clinical content, country programs |

**PR Policy:** All changes via Pull Request. Minimum 1 owner review. CI must pass. No direct commits to `main`.


---

## MVP Acceptance Checklist

- [ ] All MVP files exist in repository with real content (not placeholders)
- [ ] CI pipeline passes on `main` branch
- [ ] No secrets, credentials, or PHI committed
- [ ] README.md reflects current state with setup instructions
- [ ] CODEOWNERS file present
- [ ] All MVP functional requirements verified manually or via automated tests
- [ ] Linked to `CATALOGS.md` and `TODO.md` in `zs-docs-platform`

---

*This document is the authoritative MVP specification for `zs-agent-content-validator`.*
*Changes require a Pull Request with at least 1 owner approval.*
