# StoreDNS Audit

Browser-based DNS security auditor for ecommerce storefronts, built for Shopify stores.

**Live tool:** https://jhazor-labs.github.io/storedns-audit/

Enter a store's custom domain and get a graded report on the DNS records that control the store's platform connection, email deliverability, and spoofing protection. Everything runs client-side in the visitor's browser. No backend, no data collection, nothing sent to any server operated by this project.

## What it checks

**Storefront connection**
- A record against Shopify's documented IPv4 range (23.227.38.x), including the multiple-A-record misconfiguration Shopify warns about
- AAAA record against Shopify's documented IPv6 address
- www CNAME against Shopify's documented target (shops.myshopify.com)

**Email security and deliverability**
- SPF (RFC 7208): presence, duplicate records (permerror), terminating `all` mechanism strength, 10-DNS-lookup limit
- DMARC (RFC 7489): presence, policy strength (none / quarantine / reject), duplicate records, aggregate reporting (rua), and the strict-alignment settings (adkim=s / aspf=s) that Shopify documents as breaking its email authentication
- DKIM: probe of 20 selectors commonly used by major email providers, with an explicit disclosure that selector absence is not proof DKIM is missing
- MX: mail routing with provider identification

**Infrastructure hardening**
- CAA: presence, and on Shopify-connected domains, verification that the certificate authorities Shopify requires (Google, Let's Encrypt, SSL.com) are permitted to issue
- DNSSEC: validation status, flagged as a caution on Shopify-connected domains per Shopify's own troubleshooting guidance
- Nameserver identification

## How it works

DNS queries are resolved directly in the browser using public DNS-over-HTTPS JSON APIs: Google Public DNS (`dns.google/resolve`) as primary, Cloudflare (`cloudflare-dns.com/dns-query`) as fallback. All Shopify-specific pass/fail criteria are taken from Shopify's published help documentation; email standards criteria follow RFC 7208 (SPF) and RFC 7489 (DMARC).

Scoring starts at 100: each warning subtracts 6, each failure subtracts 15, informational findings subtract nothing. Grades: A (90+), B (80+), C (70+), D (60+), F (below 60).

## Scope and limitations

This is a DNS configuration audit, not a penetration test. It reads only publicly published DNS records and cannot see:

- Shopify admin settings (staff 2FA, permissions, installed apps)
- TLS configuration details
- Anything requiring authenticated access

The report includes a manual checklist covering those items.

## Running locally

The entire application is one file. Clone or download `index.html` and open it in a browser. No build step, no dependencies beyond two Google Fonts loaded from CDN.

## Stack

Vanilla HTML/CSS/JavaScript, single file, zero frameworks, zero build tooling.
