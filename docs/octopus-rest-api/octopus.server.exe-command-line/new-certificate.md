---
title: New certificate
description: Creates a new certificate that Octopus Server can use to authenticate itself with its Tentacles
position: 140
---

Use the new certificate command to create a new certificate that Octopus Server can use to authenticate itself with its Tentacles.

**New certificate options**

```text
Usage: octopus.server new-certificate [<options>]

Where [<options>] is any of:

      --instance=VALUE       Name of the instance to use
      --replace              Replaces the existing certificate that Octopus
                               Server uses to authenticate itself with its
                               Tentacles
      --export-pfx=VALUE     Exports the new certificate to the specified
                               file; for use with the import-certificate command
      --pfx-password=VALUE   The password to use for the exported pfx file
      --skipDatabaseCompatibilityCheck
                             Skips the database compatibility check
      --skipDatabaseSchemaUpgradeCheck
                             Skips the database schema upgrade checks. Use
                               with caution

Or one of the common options:

      --help                 Show detailed help for this command
```

