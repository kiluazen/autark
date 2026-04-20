# kushalsm.com DNS migration — 2026-04-20

## AgentMail records (needed on kushalsm.com)

| Type | Name | Value | Priority |
|------|------|-------|----------|
| TXT | agentmail._domainkey | `v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyKcR7ZwCpK9onyMmkcguHSGqybzs74kHVS5Hc5QrXlhoYJ2nDWyMW7cYr3C8+IhJPyeRIJ851x0aZ2/+ojpOX5NOiukHuCSKZvPC3CEf7/fllwfSgmsRsaPfjJCF+f1hvrdiKXg86x22fvoNot39OpMxDoYG1x5/Cl4qWYavqvKY3lEmp72h2hMabhtBFe3yLz2jLWnBkGC9RI3uWnr+nYe6UQT9yT2955EK0qebkxHgSGkAG6vmAE9UgP5d1SbQTp9pDnudf0wbUP3KyN2KCTd7plioebbzshqAHqyuHTpJX/YzOtasa5P6QyNPEcXZRgVsD4D3DwjTBjk3lQJaUQIDAQAB` | — |
| MX | @ | inbound-smtp.us-east-1.amazonaws.com | 10 |
| MX | mail | feedback-smtp.us-east-1.amazonses.com | 10 |
| TXT | mail | `v=spf1 include:amazonses.com -all` | — |
| TXT | _dmarc | `v=DMARC1; p=reject; rua=mailto:dmarc@kushalsm.com` | — |

AgentMail organization_id: `b90d0b0d-16a5-495a-859d-fc00d502122b`
AgentMail domain status: NOT_STARTED → will go VERIFIED once records propagate.

## Current Netlify DNS (kushalsm.com zone)

Nameservers currently at GoDaddy → pointing to Netlify NS1:
- dns1.p02.nsone.net
- dns2.p02.nsone.net
- dns3.p02.nsone.net
- dns4.p02.nsone.net

Records:
| Name | Type | Value |
|------|------|-------|
| kushalsm.com | A | 23.227.38.65 (stale Shopify, ignore) |
| www.kushalsm.com | CNAME | shops.myshopify.com (stale, ignore) |
| autark.kushalsm.com | NETLIFY | autark-kushalsm.netlify.app |
| kushalsm.com | NETLIFY | kushalsm.netlify.app |
| tteg.kushalsm.com | NETLIFY | bright-biscuit-38440e.netlify.app |
| www.kushalsm.com | NETLIFY | kushalsm.netlify.app |
| kushalsm.com | TXT | google-site-verification=g31-ba6ZLFxVhzb9mg_9pFBDJ0wpEP4p4ogCaQER5QE |

## Mirror on Cloudflare (initial state, keep Netlify hosting)

- apex A (flattened CNAME) or CNAME → kushalsm.netlify.app (proxied OK)
- www CNAME → kushalsm.netlify.app
- tteg CNAME → bright-biscuit-38440e.netlify.app
- autark CNAME → autark pages.dev (Cloudflare Pages, not Netlify)
- TXT google-site-verification retained
- All 5 AgentMail records

## Cloudflare nameservers (assigned)

- clarissa.ns.cloudflare.com
- venkat.ns.cloudflare.com

Registrar: GoDaddy. Replace Netlify NS with these.

## Plan

1. Netlify → capture current DNS records for kushalsm.com + autark + tteg subdomains
2. Cloudflare → add kushalsm.com zone, mirror records, add AgentMail records above
3. GoDaddy → swap nameservers to Cloudflare's
4. Cloudflare Pages autark project → bind custom domain autark.kushalsm.com
5. Update tteg/scripts/agentmail_*.py DEFAULT_INBOX_ID → kushal@kushalsm.com
6. Wait for AgentMail verification (~5–30 min after NS propagation)
