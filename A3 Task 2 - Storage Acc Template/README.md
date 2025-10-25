## Storage Account ARM Template — Breakdown

This README documents the structure and parameters of the ARM template in this folder (`template.json`) and the sample parameters file (`parameters.json`). Use this as a reference when deploying the Storage Account resources for the Olympic2021 project.

### Goal

Provision a Storage Account with related service configurations (Blob service and File service) and typical security/retention settings.

### Template structure (high level)

- `$schema` — template schema URI.
- `contentVersion` — template version.
- `parameters` — values supplied at deployment time (see list below).
- `variables` — none used in this template (empty object).
- `resources` — array of Azure resources to create or update. This template contains:
  - `Microsoft.Storage/storageAccounts` — the storage account resource.
  - `Microsoft.Storage/storageAccounts/blobServices` — config for blob soft-delete and container soft-delete.
  - `Microsoft.Storage/storageAccounts/fileservices` — config for file share soft-delete.
- `outputs` — none defined.

### Resources and purpose

1. Microsoft.Storage/storageAccounts (apiVersion: 2025-01-01)

   - Name: `parameters('storageAccountName')`.
   - Configures SKU (`parameters('accountType')`), `kind`, tags, and a range of properties:
     - TLS version, HTTPS-only enforcement, public access, shared key access, OAuth default, access tier.
     - Public network access and network ACLs (bypass/defaultAction/ipRules/ipv6Rules).
     - Dual stack IPv6 endpoint preference, DNS endpoint type.
     - Data plane features: hierarchical namespace (HNS), SFTP, large file shares.
     - Encryption settings: `keySource`, service-level encryption enabled flags, and infrastructure encryption.

2. Microsoft.Storage/storageAccounts/blobServices

   - Named `[concat(storageAccountName, '/default')]` and depends on the storage account.
   - Configures `deleteRetentionPolicy` (blob soft-delete) and `containerDeleteRetentionPolicy` (container soft-delete) with enabled flags and retention days.

3. Microsoft.Storage/storageAccounts/fileservices
   - Named `[concat(storageAccountName, '/default')]` and depends on both the storage account and the blob services resource.
   - Configures `shareDeleteRetentionPolicy` (file share soft-delete) and retention days.

### Parameters

Below are the parameters declared in `template.json` with their types and the sample values provided in `parameters.json`. Short descriptions are provided where obvious from the name.

- `location` (String)

  - Sample value: `newzealandnorth`
  - Purpose: Azure location/region to deploy the resources.

- `storageAccountName` (String)

  - Sample value: `maryannecloudstorage`
  - Purpose: Storage account name (must follow Azure storage naming rules: 3-24 lowercase letters and numbers).

- `accountType` (String)

  - Sample value: `Standard_ZRS`
  - Purpose: SKU for the storage account (e.g., Standard_LRS, Standard_ZRS, Premium_LRS).

- `kind` (String)

  - Sample value: `StorageV2`
  - Purpose: Storage account kind (Storage, StorageV2, BlobStorage, etc.).

- `minimumTlsVersion` (String)

  - Sample: `TLS1_2`
  - Purpose: Minimum TLS version allowed.

- `supportsHttpsTrafficOnly` (Bool)

  - Sample: `true`
  - Purpose: Enforce HTTPS-only to the account.

- `allowBlobPublicAccess` (Bool)

  - Sample: `false`
  - Purpose: Whether blob containers can be public.

- `allowSharedKeyAccess` (Bool)

  - Sample: `true`
  - Purpose: Allow shared key authentication.

- `defaultOAuth` (Bool)

  - Sample: `false`
  - Purpose: Default to OAuth authentication for the account (if applicable).

- `accessTier` (String)

  - Sample: `Hot`
  - Purpose: Default access tier for blobs (Hot/Cool/Archive where applicable).

- `publicNetworkAccess` (String)

  - Sample: `Enabled`
  - Purpose: Controls whether public network access to the storage account is allowed.

- `allowCrossTenantReplication` (Bool)

  - Sample: `false`
  - Purpose: Whether cross-tenant replication is permitted.

- `networkAclsBypass` (String)

  - Sample: `AzureServices`
  - Purpose: Which services bypass network rules (e.g., AzureServices).

- `networkAclsDefaultAction` (String)

  - Sample: `Allow`
  - Purpose: Default action for network ACLs (Allow or Deny).

- `networkAclsIpRules` (Array)

  - Sample: `[]` (empty array)
  - Purpose: List of IP rules (strings) for allowed IPv4 ranges.

- `networkAclsIpv6Rules` (Array)

  - Sample: `[]` (empty array)
  - Purpose: List of IPv6 rules for allowed traffic.

- `publishIpv6Endpoint` (Bool)

  - Sample: `false`
  - Purpose: Whether to publish an IPv6 endpoint for the account.

- `dnsEndpointType` (String)

  - Sample: `Standard`
  - Purpose: DNS endpoint type preference.

- `isHnsEnabled` (Bool)

  - Sample: `true`
  - Purpose: Enable hierarchical namespace (required for ADLS Gen2 features).

- `isSftpEnabled` (Bool)

  - Sample: `false`
  - Purpose: Enable SFTP support on the storage account.

- `largeFileSharesState` (String)

  - Sample: `Enabled`
  - Purpose: Enable large file shares for Azure Files.

- `keySource` (String)

  - Sample: `Microsoft.Storage`
  - Purpose: Key source for encryption (Microsoft.Storage or Microsoft.Keyvault for customer-managed keys).

- `encryptionEnabled` (Bool)

  - Sample: `true`
  - Purpose: Enable service encryption for blob/file/table/queue services.

- `keyTypeForTableAndQueueEncryption` (String)

  - Sample: `Account`
  - Purpose: Key type used for table/queue encryption (Account or Service-managed options).

- `infrastructureEncryptionEnabled` (Bool)

  - Sample: `false`
  - Purpose: Whether double-encryption (infrastructure encryption) is required.

- `isBlobSoftDeleteEnabled` (Bool)

  - Sample: `true`
  - Purpose: Enable soft-delete for blobs.

- `blobSoftDeleteRetentionDays` (Int)

  - Sample: `7`
  - Purpose: Retention period (days) for blob soft-delete.

- `isContainerSoftDeleteEnabled` (Bool)

  - Sample: `true`
  - Purpose: Enable container soft-delete.

- `containerSoftDeleteRetentionDays` (Int)

  - Sample: `7`
  - Purpose: Retention period for container soft-delete.

- `isShareSoftDeleteEnabled` (Bool)

  - Sample: `true`
  - Purpose: Enable soft-delete for file shares.

- `shareSoftDeleteRetentionDays` (Int)
  - Sample: `7`
  - Purpose: Retention days for share soft-delete.
