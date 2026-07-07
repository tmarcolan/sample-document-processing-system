# Next Steps

## Issues resolved
- Transformed DocumentProcessor.Web.csproj to net8.0

The transformation appears to have completed successfully. There are no build errors present in the solution. The following steps outline how to validate, test, and deploy the migrated project.

## 1. Restore Dependencies

Run the following command from the solution root to ensure all NuGet packages are restored correctly:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages that may need to be updated.

## 2. Build the Solution

Perform a full build to confirm the absence of errors and review any warnings:

```bash
dotnet build --configuration Release
```

Address any warnings that could indicate runtime issues, such as nullable reference warnings or obsolete API usage.

## 3. Review Target Framework

Open `DocumentProcessor.Web.csproj` and confirm the `<TargetFramework>` element targets the intended .NET version (e.g., `net8.0`). Ensure all dependent projects target a compatible framework version.

## 4. Run Unit Tests

If the solution contains test projects, execute them to verify that existing functionality behaves as expected after migration:

```bash
dotnet test --configuration Release
```

Review any failing tests and determine whether they indicate regressions introduced during the migration or pre-existing issues.

## 5. Validate Runtime Behavior

Run the application locally and exercise its core functionality:

```bash
dotnet run --project src/DocumentProcessor.Web/DocumentProcessor.Web.csproj --configuration Release
```

Pay particular attention to:
- Any features that relied on Windows-specific APIs (e.g., `System.Drawing`, COM interop, registry access), as these may fail silently at runtime even without build errors.
- File path handling, since path separator differences between Windows and Linux/macOS can cause runtime failures.
- Configuration loading, particularly if `web.config` or `app.config` files were previously used, as .NET uses `appsettings.json` by default.

## 6. Check for Platform-Specific Code

Search the codebase for any remaining usage of Windows-specific APIs or packages that may not function correctly on non-Windows platforms:

- `Microsoft.Win32`
- `System.Drawing` (consider replacing with `System.Drawing.Common` or an alternative such as `SkiaSharp`)
- P/Invoke calls targeting Windows DLLs
- `RegistryKey` usage

## 7. Review Middleware and HTTP Pipeline

If `DocumentProcessor.Web` is an ASP.NET Core project, verify that the middleware pipeline in `Program.cs` or `Startup.cs` is configured correctly. Confirm that any previously used HTTP modules or HTTP handlers from ASP.NET (classic) have been replaced with equivalent ASP.NET Core middleware.

## 8. Validate Configuration and Secrets

Ensure that environment-specific configuration values (connection strings, API keys, etc.) are correctly defined in `appsettings.json` or environment variables, and that they are being read correctly at runtime.

## 9. Publish the Application

Once runtime behavior has been validated, publish the application to confirm the output is complete:

```bash
dotnet publish src/DocumentProcessor.Web/DocumentProcessor.Web.csproj --configuration Release --output ./publish
```

Review the contents of the `./publish` directory to confirm all required files are present before deploying to the target environment.