# APIVerve Domain Health Action

> Monitor domain expiration, WHOIS changes, and domain availability

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Domain_Health-blue?logo=github)](https://github.com/apiverve/action-domain-health)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**[Browse All APIs](https://apiverve.com/marketplace?utm_source=github&utm_medium=action&utm_campaign=domain-health)** | **[Get Free API Key](https://dashboard.apiverve.com/signup?utm_source=github&utm_medium=action&utm_campaign=domain-health)** | **[Documentation](https://docs.apiverve.com?utm_source=github&utm_medium=action&utm_campaign=domain-health)**

---

## What does this action do?

This action provides access to APIVerve's Domain Health APIs directly in your GitHub workflows:

- Alert before domains expire
- Monitor WHOIS record changes
- Check domain availability for new projects
- Verify domain is responding

### Available APIs

| API | Description |
|-----|-------------|
| `domainexpiration` | Domain Expiration tracks domain expiration health and domain age for any web address. It returns days remaining until expiry, an expiration status, and age in days and years, with paid plans adding exact registration timestamps. |
| `whoislookup` | WHOIS Lookup checks domain registration records to return registrar details, creation and expiration dates, domain age, and active nameservers. Pass any domain name to inspect status codes and calculate days since registration. |
| `domainavailability` | Domain Availability checks domain availability in real time to show if a web address is open for registration or already taken. Get creation and expiration dates for taken domains, while paid plans add registrar details. |
| `pinger` | Domain and IP Pinger checks whether any domain name or public IP address is reachable and responding in real time. It returns an alive status and successful round trips, while paid plans add packet loss percentages and latency metrics. |

---

## Quick Start

```yaml
- name: Domain Health
  uses: apiverve/action-domain-health@v1
  with:
    api_key: ${{ secrets.APIVERVE_KEY }}
    api: domainexpiration
    params: '{"domain": "example.com"}'
```

---

## Setup

### 1. Get Your API Key

Sign up for a free account at [dashboard.apiverve.com/signup](https://dashboard.apiverve.com/signup?utm_source=github&utm_medium=action&utm_campaign=domain-health) and create an API key.

### 2. Add Secret to Repository

Go to your repository **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

- Name: `APIVERVE_KEY`
- Value: Your API key from the dashboard

### 3. Use in Workflow

```yaml
- name: Domain Health
  uses: apiverve/action-domain-health@v1
  with:
    api_key: ${{ secrets.APIVERVE_KEY }}
    api: domainexpiration
    params: '{"your": "parameters"}'
```

---

## Pass/fail checks

Set `check` and the action stops being a plain API call: it evaluates the result and fails the job when something is wrong, so problems surface in CI instead of in production.

### Fail before the domain lapses

Warn at 60 days, fail the job at 14 days

```yaml
- name: Fail before the domain lapses
  uses: apiverve/action-domain-health@v1
  with:
    api_key: $
    check: domain-expiry
    domain: example.com
    warn_days: 60
    fail_days: 14
```

---

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `api_key` | Your APIVerve API key (or set `APIVERVE_API_KEY` env var) | Yes* | - |
| `api` | API to use: `domainexpiration`, `whoislookup`, `domainavailability`, `pinger` | No | `domainexpiration` |
| `params` | JSON parameters for the API | No | `{}` |
| `output_file` | Path to save binary output (images, PDFs) | No | - |
| `format` | Response format: `json`, `yaml`, or `xml` | No | `json` |
| `fail_on_error` | Fail workflow if API returns error | No | `true` |
| `check` | Run a pass/fail check instead: `domain-expiry` | No | - |
| `domain` | Domain to check | With `check` | - |
| `warn_days` / `fail_days` | Warn / fail when this few days remain | No | `60` / `14` |
*\*API key is required but can be provided via input OR `APIVERVE_API_KEY` / `APIVERVE_KEY` environment variable.*

## Outputs

| Output | Description |
|--------|-------------|
| `result` | Full API response as JSON |
| `data` | The `data` field from response as JSON |
| `status` | API status (`ok` or `error`) |
| `file` | Path to downloaded file (if `output_file` was used) |
| `days_remaining` | Days until expiry (`ssl-expiry`, `domain-expiry`) |
| `records` | Matching DNS records as JSON (`dns-record`) |
---

## Examples

### Expiration Check

Check when a domain expires

```yaml
- name: Expiration Check
  id: domain-health-0
  uses: apiverve/action-domain-health@v1
  with:
    api_key: ${{ secrets.APIVERVE_KEY }}
    api: domainexpiration
    params: '{"domain": "example.com"}'

- name: Use result
  run: echo "Result: ${{ steps.domain-health-0.outputs.data }}"
```

### WHOIS Lookup

Get WHOIS information for a domain

```yaml
- name: WHOIS Lookup
  id: domain-health-1
  uses: apiverve/action-domain-health@v1
  with:
    api_key: ${{ secrets.APIVERVE_KEY }}
    api: whoislookup
    params: '{"domain": "example.com"}'

- name: Use result
  run: echo "Result: ${{ steps.domain-health-1.outputs.data }}"
```


---

## Full Workflow Example

```yaml
name: Domain Health Workflow

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  domain-health:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Domain Health
        id: result
        uses: apiverve/action-domain-health@v1
        with:
          api_key: ${{ secrets.APIVERVE_KEY }}
          api: domainexpiration
          params: '{"domain": "example.com"}'

      - name: Show result
        run: |
          echo "Status: ${{ steps.result.outputs.status }}"
          echo "Data: ${{ steps.result.outputs.data }}"
```

---

## Related Actions

Looking for more APIVerve actions?

- [apiverve/action](https://github.com/apiverve/action) - Generic action for all 350+ APIs
- [apiverve/action-release-assets](https://github.com/apiverve/action-release-assets) - Generate QR codes, barcodes, and badges for your GitHub releases
- [apiverve/action-visual-testing](https://github.com/apiverve/action-visual-testing) - Capture screenshots and generate PDFs for visual regression testing and documentation
- [apiverve/action-dns-monitor](https://github.com/apiverve/action-dns-monitor) - Verify DNS configuration, check propagation, and validate DNSSEC after deployments

**[Browse all APIVerve Actions →](https://github.com/marketplace?query=apiverve)**

---

## Pricing

- **Free tier** - Get started with generous free limits
- **Pro plans** - Higher rate limits and priority support for production use

Check out [pricing details](https://apiverve.com/pricing?utm_source=github&utm_medium=action&utm_campaign=domain-health).

---

## Resources

- **API Documentation**: [docs.apiverve.com](https://docs.apiverve.com?utm_source=github&utm_medium=action&utm_campaign=domain-health)
- **API Marketplace**: [apiverve.com/marketplace](https://apiverve.com/marketplace?utm_source=github&utm_medium=action&utm_campaign=domain-health)
- **Issues & Support**: [GitHub Issues](https://github.com/apiverve/action-domain-health/issues)
- **Email**: support@apiverve.com

---

## License

MIT - see [LICENSE](LICENSE)

---

Built by [APIVerve](https://apiverve.com?utm_source=github&utm_medium=action&utm_campaign=domain-health) - 350+ APIs for developers
