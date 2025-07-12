# WordPress Plugin Development Instructions

## Overview

This plugin, called `SiteDash`, is intended to replicate the functionality of a custom Drupal 11 module. It is designed to authenticate with an external SaaS service and integrate with previously installed plugins (notably `audit_export`) via shared hooks or service contracts.

## Functional Requirements

- Authenticate the WordPress site with the external SiteDash SaaS API.
- Store and manage the authentication state in WordPress options.
- Leverage shared functionality, such as remote post hooks, if available.
- Clean and secure handling of API credentials and connection errors.

## Architectural Notes

- This plugin **depends** on capabilities exposed by the `audit_export` plugin — particularly the remote post system.
- If `audit_export` is not installed or activated, SiteDash should either:
    - Disable remote post integrations gracefully, or
    - Notify the administrator of the missing dependency.

## Directory Structure

```plaintext
/wp-content/plugins/sitedash/
├── sitedash.php                 # Plugin bootstrap file
├── includes/
│   ├── AuthManager.php          # Handles API communication and token storage
│   └── RemotePostBridge.php     # Optional integration with audit_export remote post
├── admin/
│   └── settings-page.php        # Admin UI for connecting to SaaS
└── cli/
    └── commands.php             # Optional WP-CLI commands (if applicable)
```

## Admin UI

- Located under Tools → SiteDash
- Allows administrators to:
    - Enter API key or credentials
    - Test connection to SaaS service
    - View current authentication status
- Displays integration state with `audit_export` (if available)

## Remote POST Integration (via `audit_export`)

- If `audit_export` is installed and active:
    - Register SiteDash as a remote post listener or hook subscriber
    - Provide a handler for transmitting data securely to the SaaS endpoint

## WP-CLI Integration (Optional)

If included, WP-CLI commands may include:

```bash
wp sitedash auth status
wp sitedash auth connect --key=<api_key>
wp sitedash remote sync
```

## Best Practices

- Use PSR-4 autoloading
- Escape all output in HTML
- Validate and sanitize all user inputs
- Store tokens securely using WordPress’s settings API
- Gracefully handle API failures and missing dependencies
- Follow WordPress security and coding standards

## References

- WordPress Plugin Developer Handbook: https://developer.wordpress.org/plugins/
- WP-CLI Handbook: https://make.wordpress.org/cli/handbook/
- Drupal module reference: `drupal_module/sitedash/`
- Audit Export reference: `drupal_module/audit_export/` (used for integration)

---

Thanks for reading. This document supports building a minimal, secure SiteDash plugin for WordPress based on the functionality and structure of its Drupal counterpart.
