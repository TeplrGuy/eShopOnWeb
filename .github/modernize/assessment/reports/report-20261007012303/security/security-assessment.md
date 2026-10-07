# Security Assessment Report

**Generated:** 2026-10-07T01:29:22.599156Z

## Summary

| Metric | Count |
|---|---|
| TotalFindings | 3 |
| CveCount | 0 |
| CweCount | 3 |
| TotalRulesAssessed | 59 |
| RulesPassed | 56 |

## Scope and interpretation

- Static source review, not penetration testing or an executed CodeQL scan. NOT_FOUND means no confirmed reportable exploit within scope, not proof of absence.
- Dependency analysis checked 49 exact declared NuGet versions; transitive-only packages, runtime/SDK vulnerabilities and LibMan/CDN assets were not assessed.
- Two medium-severity CVEs were found in Azure.Identity 1.10.4 and excluded by the high minimum threshold. See cve-assessment-result.json for details.
- CWE-321 and CWE-798 describe the same JWT signing-key vulnerability. Three flagged checklist rules represent two unique underlying vulnerabilities.
- CWE severities follow the skill assessment taxonomy, not CVSS. The reviewer assessed both underlying code vulnerabilities as HIGH impact with 9/10 confidence.
- Sample seeded passwords and denial-of-service-only findings were excluded by the specialist review scope.

### By Severity

| Severity | Count |
|---|---|
| mandatory | 0 |
| optional | 1 |
| potential | 2 |

## CVE Findings (Dependency Vulnerabilities)

No declared-dependency CVEs met the **high** minimum severity threshold. This does not mean all dependencies are vulnerability-free.

## CWE Findings (Code-Level Vulnerabilities)

### CWE-321: Use of Hard-coded Cryptographic Key
- **Category:** Credentials & Secrets
- **Assessment severity:** potential
- **Story Points:** 5
- **Files:** /home/runner/work/eShopOnWeb/eShopOnWeb/src/ApplicationCore/Constants/AuthorizationConstants.cs:11, /home/runner/work/eShopOnWeb/eShopOnWeb/src/Infrastructure/Identity/IdentityTokenClaimService.cs:26, /home/runner/work/eShopOnWeb/eShopOnWeb/src/PublicApi/Program.cs:54, /home/runner/work/eShopOnWeb/eShopOnWeb/src/PublicApi/CatalogItemEndpoints/CreateCatalogItemEndpoint.cs:30

HIGH impact; confidence 9/10. AuthorizationConstants embeds the JWT signing key as a source constant. IdentityTokenClaimService.GetTokenAsync uses it for HMAC-SHA256 signing at lines 26 and 41. PublicApi startup uses the same constant as its bearer-token verification key at lines 54-69, without a development-only guard or configurable replacement. An attacker who knows the source can sign a JWT containing the administrator role and a future expiration, then access administrator catalog mutation endpoints. The key is an active trust anchor, not merely unused example data. Its literal value is intentionally omitted. Verified through static signing, validation, and authorization data flow; no live forged token was submitted.

### CWE-798: Use of Hard-coded Credentials
- **Category:** Credentials & Secrets
- **Assessment severity:** optional
- **Story Points:** 5
- **Files:** /home/runner/work/eShopOnWeb/eShopOnWeb/src/ApplicationCore/Constants/AuthorizationConstants.cs:11, /home/runner/work/eShopOnWeb/eShopOnWeb/src/Infrastructure/Identity/IdentityTokenClaimService.cs:26, /home/runner/work/eShopOnWeb/eShopOnWeb/src/PublicApi/Program.cs:54

HIGH impact; confidence 9/10. The hard-coded JWT signing credential is also an instance of CWE-798. IdentityTokenClaimService signs with the source constant and PublicApi validates bearer tokens with it. Source knowledge therefore permits forging an administrator token without authenticating. This is the same underlying vulnerability as CWE-321, recorded separately because the checklist requires one result for each rule; deduplicate it when counting unique vulnerabilities. Literal signing-key and sample-password values are intentionally omitted.

### CWE-99: Improper Control of Resource Identifiers ('Resource Injection')
- **Category:** Injection Attacks
- **Assessment severity:** potential
- **Story Points:** 3
- **Files:** /home/runner/work/eShopOnWeb/eShopOnWeb/src/Web/Controllers/OrderController.cs:31, /home/runner/work/eShopOnWeb/eShopOnWeb/src/Web/Features/OrderDetails/GetOrderDetailsHandler.cs:21, /home/runner/work/eShopOnWeb/eShopOnWeb/src/ApplicationCore/Specifications/OrderWithItemsByIdSpec.cs:11

HIGH impact; confidence 9/10. OrderController.Detail accepts an attacker-controlled numeric orderId at /Order/Detail/{orderId} and supplies the authenticated username to GetOrderDetails. GetOrderDetailsHandler.Handle ignores that username and retrieves the order with OrderWithItemsByIdSpec, whose only restriction is order.Id == orderId. No owner authorization occurs before handler lines 29-43 return order items, shipping address, and total. Any authenticated customer can select another customer's existing order ID and receive its details. Authentication alone does not restrict the resource to its owner. Verified by static tracing of the controller, request, handler, and specification; no live exploit was executed.

## Recommendations

1. Restrict order retrieval by both order ID and authenticated buyer identity before returning order details.
2. Replace and rotate the embedded JWT signing key using externally managed signing credentials.
3. Upgrade Azure.Identity to a maintained version that resolves both recorded medium-severity advisories.
4. Re-run dependency analysis with resolved transitive versions and perform dynamic authorization tests.
