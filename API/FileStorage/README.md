# API File Storage

File uploads are abstracted behind `IFileStorageService` with provider selection and shared validation/safety rules.

## Verified capabilities

- **Provider selection** — configuration chooses Local, AWS S3, or Azure Blob implementations.
- **Local storage** — writes under a controlled Files root; path traversal is blocked.
- **AWS S3** — stores private object keys (no permanent public URL pattern in the service path).
- **Azure Blob** — supports connection-string or managed-identity style configuration; container access set private.
- **Upload validation** — shared `FileUploadValidator` enforces extension allowlists, blocks dangerous extensions, validates image MIME/extension pairing, checks size limits, and rejects empty payloads.
- **Server-generated filenames** — storage names are GUID-based (`BuildServerFileName`); client filenames are not trusted as storage names.

## Configuration

- Settings / startup: `API/src/Infrastructure/FileStorage/Startup.cs`
- Config file: `API/src/Host/Configurations/fileStorage.json`

## Libraries

- `AWSSDK.S3` (AWS provider)
- `Azure.Storage.Blobs` / `Azure.Identity` (Azure provider)

## Evidence

- `API/src/Infrastructure/FileStorage/Startup.cs`
- `API/src/Infrastructure/FileStorage/LocalFileStorageService.cs`
- `API/src/Infrastructure/FileStorage/AwsFileStorageService.cs`
- `API/src/Infrastructure/FileStorage/AzureBlobFileStorageService.cs`
- `API/src/Infrastructure/FileStorage/FileUploadValidator.cs`
- `API/src/Core/Application/Common/FileStorage/IFileStorageService.cs`
- `API/src/Core/Application/Common/FileStorage/FileUploadRequest.cs`
- `API/src/Host/Configurations/fileStorage.json`
