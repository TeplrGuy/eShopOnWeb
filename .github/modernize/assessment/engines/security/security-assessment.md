# Security Assessment Report

**Generated:** 2026-10-07T14:18:30.485679Z

## Summary

| Metric | Count |
|--------|-------|
| Total Findings | 17 |
| CVE Vulnerabilities | 10 |
| CWE Vulnerabilities | 7 |
| Total Rules Assessed | 59 |
| Rules Passed | 52 |

### By Severity

| Severity | Count |
|----------|-------|
| mandatory | 7 |
| optional | 4 |
| potential | 6 |

## CVE Findings (Dependency Vulnerabilities)

### CVE-2023-29337: NuGet Client Remote Code Execution Vulnerability
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2023-29337](https://github.com/advisories/GHSA-6qmf-mmc7-6c2p): NuGet Client Remote Code Execution Vulnerability

Severity: HIGH

Affected dependencies:
  - NuGet.Common@6.3.1 (transitive dependency; no direct declaration location) — range >= 6.2.0, < 6.2.4; first patched 6.2.4; range >= 6.3.0, < 6.3.3; first patched 6.3.3; range >= 6.4.0, < 6.4.2; first patched 6.4.2; range = 6.5.0; first patched 6.5.1; range = 6.6.0; first patched 6.6.1; range >= 6.0.0, < 6.0.5; first patched 6.0.5; range >= 4.6.0, < 5.11.5; first patched 5.11.5
  - NuGet.Protocol@6.3.1 (transitive dependency; no direct declaration location) — range >= 6.2.0, < 6.2.4; first patched 6.2.4; range >= 6.3.0, < 6.3.3; first patched 6.3.3; range >= 6.4.0, < 6.4.2; first patched 6.4.2; range = 6.5.0; first patched 6.5.1; range = 6.6.0; first patched 6.6.1; range >= 6.0.0, < 6.0.5; first patched 6.0.5; range >= 4.7.0, < 5.11.5; first patched 5.11.5

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-0057: NuGet Client Security Feature Bypass Vulnerability 
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2024-0057](https://github.com/advisories/GHSA-68w7-72jg-6qpp): NuGet Client Security Feature Bypass Vulnerability 

Severity: CRITICAL

Affected dependencies:
  - NuGet.Packaging@6.3.1 (transitive dependency; no direct declaration location) — range >= 4.6.0, < 5.11.6; first patched 5.11.6; range >= 6.0.0, < 6.0.6; first patched 6.0.6; range >= 6.3.0, < 6.3.4; first patched 6.3.4; range >= 6.4.0, < 6.4.3; first patched 6.4.3; range >= 6.6.0, < 6.6.2; first patched 6.6.2; range = 6.7.0; first patched 6.7.1; range = 6.8.0; first patched 6.8.1

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-27086: MSAL.NET applications targeting Xamarin Android and .NET Android (MAUI) susceptible to local denial of service
- **Severity:** potential
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2024-27086](https://github.com/advisories/GHSA-x674-v45j-fwxw): MSAL.NET applications targeting Xamarin Android and .NET Android (MAUI) susceptible to local denial of service

Severity: LOW

Affected dependencies:
  - Microsoft.Identity.Client@4.56.0 (transitive dependency; no direct declaration location) — range >= 4.48.0, < 4.59.1; first patched 4.59.1; range >= 4.60.0, < 4.60.3; first patched 4.60.3

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-29992: Azure Identity Library for .NET Information Disclosure Vulnerability
- **Severity:** optional
- **Story Points:** 1
- **Files:** src/Web/Web.csproj

[CVE-2024-29992](https://github.com/advisories/GHSA-wvxc-855f-jvrv): Azure Identity Library for .NET Information Disclosure Vulnerability

Severity: MEDIUM

Affected dependencies:
  - Azure.Identity@1.10.3 (transitive dependency; no direct declaration location) — range < 1.11.0; first patched 1.11.0
  - Azure.Identity@1.10.4 (src/Web/Web.csproj) — range < 1.11.0; first patched 1.11.0

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-30105: Microsoft Security Advisory CVE-2024-30105 | .NET Denial of Service Vulnerability
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** src/ApplicationCore/ApplicationCore.csproj

[CVE-2024-30105](https://github.com/advisories/GHSA-hh2w-p6rv-4g7w): Microsoft Security Advisory CVE-2024-30105 | .NET Denial of Service Vulnerability

Severity: HIGH

Affected dependencies:
  - System.Text.Json@8.0.0 (transitive dependency; no direct declaration location) — range >= 7.0.0, < 8.0.4; first patched 8.0.4
  - System.Text.Json@8.0.3 (src/ApplicationCore/ApplicationCore.csproj) — range >= 7.0.0, < 8.0.4; first patched 8.0.4

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-35255: Azure Identity Libraries and Microsoft Authentication Library Elevation of Privilege Vulnerability
- **Severity:** optional
- **Story Points:** 1
- **Files:** src/Web/Web.csproj

[CVE-2024-35255](https://github.com/advisories/GHSA-m5vv-6r4h-3vj9): Azure Identity Libraries and Microsoft Authentication Library Elevation of Privilege Vulnerability

Severity: MEDIUM

Affected dependencies:
  - Azure.Identity@1.10.3 (transitive dependency; no direct declaration location) — range < 1.11.4; first patched 1.11.4
  - Azure.Identity@1.10.4 (src/Web/Web.csproj) — range < 1.11.4; first patched 1.11.4
  - Microsoft.Identity.Client@4.56.0 (transitive dependency; no direct declaration location) — range >= 4.49.1, < 4.60.4; first patched 4.60.4; range >= 4.61.0, < 4.61.3; first patched 4.61.3

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-38095: Microsoft Security Advisory CVE-2024-38095 | .NET Denial of Service Vulnerability
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2024-38095](https://github.com/advisories/GHSA-447r-wph3-92pm): Microsoft Security Advisory CVE-2024-38095 | .NET Denial of Service Vulnerability

Severity: HIGH

Affected dependencies:
  - System.Formats.Asn1@5.0.0 (transitive dependency; no direct declaration location) — range >= 5.0.0-preview.7.20364.11, < 6.0.1; first patched 6.0.1; range >= 7.0.0-preview.1.22076.8, < 8.0.1; first patched 8.0.1

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-43483: Microsoft Security Advisory CVE-2024-43483 | .NET Denial of Service Vulnerability
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2024-43483](https://github.com/advisories/GHSA-qj66-m88j-hmgj): Microsoft Security Advisory CVE-2024-43483 | .NET Denial of Service Vulnerability

Severity: HIGH

Affected dependencies:
  - Microsoft.Extensions.Caching.Memory@8.0.0 (transitive dependency; no direct declaration location) — range >= 8.0.0-preview.1.23110.8, <= 8.0.0; first patched 8.0.1; range >= 9.0.0-preview.1.24080.9, <= 9.0.0-rc.1.24431.7; first patched 9.0.0-rc.2.24473.5; range >= 6.0.0-preview.1.21102.12, <= 6.0.1; first patched 6.0.2

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2024-43485: Microsoft Security Advisory CVE-2024-43485 | .NET Denial of Service Vulnerability
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** src/ApplicationCore/ApplicationCore.csproj

[CVE-2024-43485](https://github.com/advisories/GHSA-8g4q-xg66-9fp4): Microsoft Security Advisory CVE-2024-43485 | .NET Denial of Service Vulnerability

Severity: HIGH

Affected dependencies:
  - System.Text.Json@8.0.0 (transitive dependency; no direct declaration location) — range >= 8.0.0, <= 8.0.4; first patched 8.0.5; range >= 6.0.0, <= 6.0.9; first patched 6.0.10
  - System.Text.Json@8.0.3 (src/ApplicationCore/ApplicationCore.csproj) — range >= 8.0.0, <= 8.0.4; first patched 8.0.5; range >= 6.0.0, <= 6.0.9; first patched 6.0.10

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

### CVE-2026-32933: AutoMapper Vulnerable to Denial of Service (DoS) via Uncontrolled Recursion
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** Transitive dependency; declaration location unavailable.

[CVE-2026-32933](https://github.com/advisories/GHSA-rvv3-g6hj-g44x): AutoMapper Vulnerable to Denial of Service (DoS) via Uncontrolled Recursion

Severity: HIGH

Affected dependencies:
  - AutoMapper@12.0.1 (transitive dependency; no direct declaration location) — range >= 16.0.0, < 16.1.1; first patched 16.1.1; range < 15.1.1; first patched 15.1.1

Recommended fix: upgrade affected packages to a version outside the vulnerable range; see the advisory for patched versions.

## CWE Findings (Code-Level Vulnerabilities)

### CWE-772: Missing Release of Resource after Effective Lifetime
- **Category:** Code Quality
- **Severity:** potential
- **Story Points:** 3
- **Files:** src/BlazorAdmin/Services/HttpService.cs

The product does not release a resource after its effective lifetime has ended, i.e., after the resource is no longer needed.

HttpService.HttpGet, HttpDelete, HttpPost, and HttpPut create HttpResponseMessage instances at lines 28, 40, 54, and 74. The responses and their content are consumed or returned from the methods without disposal, so associated response resources remain held until eventual cleanup.

### CWE-789: Memory Allocation with Excessive Size Value
- **Category:** Code Quality
- **Severity:** potential
- **Story Points:** 5
- **Files:** src/BlazorShared/Models/CatalogItem.cs

The product allocates memory based on an untrusted, large size value, but it does not ensure that the size is within expected limits, allowing arbitrary amounts of memory to be allocated.

CatalogItem.DataToBase64 copies the complete caller-supplied IFileListEntry.Data stream into a MemoryStream at lines 63-70 without enforcing a maximum length, then creates an additional array and base64 string at lines 70-71. A large supplied file can therefore drive unbounded memory growth.

### CWE-567: Unsynchronized Access to Shared Data in a Multithreaded Context
- **Category:** Concurrency & Synchronization
- **Severity:** potential
- **Story Points:** 5
- **Files:** src/BlazorAdmin/Services/ToastService.cs, src/BlazorAdmin/Helpers/ToastComponent.cs

The product does not properly synchronize shared data, such as static variables across threads, which can lead to undefined behavior and unpredictable data changes.

ToastService.ShowToast invokes OnShow on the caller context, while the System.Timers.Timer Elapsed callback invokes OnHide from a timer thread (ToastService.cs:41-48). ToastComponent.ShowToast and HideToast mutate the same component state and call StateHasChanged directly (ToastComponent.cs:45-54), without synchronization or dispatching the timer callback through the component synchronization context.

### CWE-820: Missing Synchronization
- **Category:** Concurrency & Synchronization
- **Severity:** potential
- **Story Points:** 8
- **Files:** src/BlazorAdmin/Services/ToastService.cs, src/BlazorAdmin/Helpers/ToastComponent.cs

The product utilizes a shared resource in a concurrent manner but does not attempt to synchronize access to the resource.

The timer callback registered in ToastService.SetCountdown runs independently of Blazor UI events and raises OnHide. ToastComponent.HideToast changes IsVisible and calls StateHasChanged without marshaling execution to the Blazor synchronization context (ToastComponent.cs:51-54), allowing concurrent unsynchronized component state access.

### CWE-259: Use of Hard-coded Password
- **Category:** Credentials & Secrets
- **Severity:** optional
- **Story Points:** 5
- **Files:** src/ApplicationCore/Constants/AuthorizationConstants.cs

The product contains a hard-coded password, which it uses for its own inbound authentication or for outbound communication to external components.

AuthorizationConstants.DEFAULT_PASSWORD is a hard-coded password and is passed to UserManager.CreateAsync for both seeded accounts in src/Infrastructure/Identity/AppIdentityDbContextSeed.cs:21,25. The fixed value is not reproduced here.

### CWE-321: Use of Hard-coded Cryptographic Key
- **Category:** Credentials & Secrets
- **Severity:** potential
- **Story Points:** 5
- **Files:** src/ApplicationCore/Constants/AuthorizationConstants.cs, src/Web/key-768c1632-cf7b-41a9-bb7a-bff228ae8fba.xml

The product uses a hard-coded, unchangeable cryptographic key.

AuthorizationConstants.JWT_SECRET_KEY is a fixed cryptographic signing key used by the API and IdentityTokenClaimService; the tracked ASP.NET Data Protection XML file also embeds unencrypted key material at lines 10-12. Key values are omitted.

### CWE-798: Use of Hard-coded Credentials
- **Category:** Credentials & Secrets
- **Severity:** optional
- **Story Points:** 5
- **Files:** src/ApplicationCore/Constants/AuthorizationConstants.cs, src/Infrastructure/Identity/AppIdentityDbContextSeed.cs, src/Web/key-768c1632-cf7b-41a9-bb7a-bff228ae8fba.xml

The product contains hard-coded credentials, such as a password or cryptographic key.

The repository contains a fixed password used when seeding accounts, a fixed JWT signing key, and unencrypted ASP.NET Data Protection key material. The credential and key values are omitted.
