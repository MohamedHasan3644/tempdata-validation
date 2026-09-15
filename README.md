# TempData Static SSR Validation

This repository contains a Blazor Web App used to validate TempData behavior during static server-side rendering (static SSR). It covers consuming reads, `Peek`, `Keep`, untouched requests, `[SupplyParameterFromTempData]` writeback, and same-request reads.

## Repository

- Repository: <https://github.com/MohamedHasan3644/tempdata-validation>
- Related issue: <https://github.com/dotnet/aspnetcore/issues/69134>
- TempData documentation: <https://learn.microsoft.com/aspnet/core/blazor/state-management/server?view=aspnetcore-11.0#temporary-data-persistence>

## Prerequisites

- Windows with PowerShell.
- .NET SDK `11.0.100-rc.1.26425.128`, as selected by `global.json`.
- Chrome or Microsoft Edge for manual browser validation.
- Permission to trust the ASP.NET Core HTTPS development certificate.

Run all commands below from the repository root, for example:

```powershell
Set-Location D:\tempdata-validation
```

## Verify the environment

Confirm that the pinned SDK is available and record the complete .NET environment when collecting validation evidence:

```powershell
dotnet --version
dotnet --info
```

`dotnet --version` should report `11.0.100-rc.1.26425.128` or the compatible SDK selected according to `global.json`.

## Setup

Trust the local ASP.NET Core HTTPS development certificate:

```powershell
dotnet dev-certs https --trust
```

Accept the Windows confirmation dialog if prompted.

Restore the solution dependencies:

```powershell
dotnet restore .\TempDataValidation.sln
```

## Build

Build the restored solution:

```powershell
dotnet build .\TempDataValidation.sln --no-restore
```

A successful build ends with `Build succeeded.` and no errors.

## Run

Start the sample with its HTTPS launch profile:

```powershell
dotnet run --project .\TempDataValidation\TempDataValidation.csproj --launch-profile https
```

Wait for the terminal to report that the application is listening, then open:

- HTTPS: <https://localhost:7171>
- HTTP: <http://localhost:5012>

Use the HTTPS address for validation. Keep the server terminal open while testing and press `Ctrl+C` to stop it.

## Browser preparation

Use a clean browser session for each independent test flow:

1. Close all existing Incognito or InPrivate windows so that prior TempData cookies aren't reused.
2. Disable browser extensions and any injected performance or Web Vitals tooling. A fresh browser profile with no extensions is preferred.
3. Open a new Incognito or InPrivate window and navigate to `https://localhost:7171`.
4. Open DevTools and select **Network**.
5. Enable **Preserve log**, leave **Disable cache** cleared unless a test says otherwise, and clear the network log before beginning the flow.
6. Open **Console**, clear it, and record or classify any browser error shown during the test.
7. Never publish a complete TempData cookie value. Redact the value while retaining the cookie name and relevant attributes.

Close all private windows before starting the next independent test. Private windows from the same browser process can share cookies while any of them remains open.

## Test execution

Follow the click-by-click instructions and evidence checkpoints in [MANUAL_TEST_STEPS.md](MANUAL_TEST_STEPS.md). The general workflow is:

1. Start with a clean browser session.
2. Open the seed page at `https://localhost:7171/`.
3. Enter a unique value that identifies the testcase, such as `TC05-PEEK-20260915-001`.
4. Select the operation required by the testcase and choose **Store and redirect**.
5. Verify the rendered values, request timestamp, output markers, HTTP method, status code, and relevant response headers.
6. Perform the required reload or follow-up navigation and verify the expected TempData lifecycle.
7. Capture each evidence checkpoint before navigating, reloading, or changing the DevTools panel.
8. Confirm that the browser console and server terminal contain no unclassified application errors.

The sample exposes these validation routes:

| Route | Validation behavior |
|---|---|
| `/` | Write a TempData message and redirect. |
| `/read` | Call `Get` twice in one request and consume the value at request completion. |
| `/read-bound-clear` | Bind with `[SupplyParameterFromTempData]`, copy the value, and explicitly clear the property. |
| `/read-bound-untouched` | Bind with `[SupplyParameterFromTempData]` and leave the property unchanged for writeback. |
| `/peek` | Read with `Peek` without consuming the value. |
| `/keep-once` | Read with `Get` and retain the key with `Keep`. |
| `/untouched` | Complete a request without accessing TempData. |
| `/peek-keep` | Call `Peek` and then `Keep` for the same key. |
| `/same-request` | Write and read the same key twice before one response completes. |

Use unique testcase values: choose a different identifiable message for every independent testcase. Reuse a value only within one flow when continuity or reader parity is the behavior being demonstrated.

## Evidence layout

Validation artifacts are stored under `evidence/`:

```text
evidence/
|-- TestReport.docx
|-- TC-01/
|-- TC-02/
|-- TC-03/
|   |-- Part-A-navigate-away-and-request-new-reader/
|   `-- Part-B-test-the-browser-back-button/
|-- TC-04/
|-- ...
`-- TC-12/
```

Each testcase directory contains screenshots for the significant rendered state, DOM markers, Network request details, and Console state. Artifact names begin with the testcase ID and sequence number, for example `TC-05-01-first-peek.png`.

When adding or replacing evidence:

- Keep all artifacts for one testcase in its matching directory.
- Capture the browser address, visible result, and request timestamp where applicable.
- Include the HTTP method and status when a network transition is part of the assertion.
- Use the same unique test value across the screenshots belonging to one flow.
- Apply cookie redaction: redact cookie values, authorization data, and other sensitive information.
- Reference the exact relative artifact paths from the corresponding row in `TestReport.docx`.