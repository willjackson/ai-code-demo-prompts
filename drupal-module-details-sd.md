# Drupal Module Reference: SiteDash

## Metadata

- **Name**: SiteDash
- **Type**: module
- **Core**: ^10 || ^11 (Compatible with Drupal 10 and 11)
- **Description**: Connects to the SiteDash Monitoring and Dashboard service for site management and monitoring.
- **Package**: SiteDash
- **Configure Route**: sitedash.settings
- **Dependencies**: 
  - audit_export:audit_export
  - audit_export:audit_export_core
  - audit_export:audit_export_post

## File Inventory

### Root Directory Files

- **/README.md** — Module documentation explaining installation, configuration, and usage. Details integration with SiteDash.io monitoring service.
- **/composer.json** — Composer package definition. Declares module type as `drupal-module`, specifies Drupal 10/11 compatibility, and lists `drupal/audit_export` as dependency.
- **/sitedash.info.yml** — Module metadata file defining module name, description, dependencies, and configuration route.
- **/sitedash.module** — Main module file containing hook implementations for help, form alteration, and audit export integration.
- **/sitedash.install** — Installation hooks for setup messages and configuration cleanup on uninstall.
- **/sitedash.services.yml** — Service container definitions for SiteDashClient service and logger channel.
- **/sitedash.routing.yml** — Defines administrative route at `/admin/config/services/sitedash` for settings form.
- **/sitedash.permissions.yml** — Defines single permission `configure sitedash settings` with restricted access.
- **/sitedash.links.menu.yml** — Creates admin menu link under Configuration > Services.

### Configuration Files

- **/config/install/sitedash.settings.yml** — Default configuration values (all empty/false initially) for token, pubkey, siteid, envid, base_url, and remote_post_override.
- **/config/schema/sitedash.schema.yml** — Configuration schema defining data types and labels for all config values.

### Source Code Files

- **/src/Service/SiteDashClient.php** — Core service class handling API authentication and communication with SiteDash backend.
- **/src/Form/SiteDashSettingsForm.php** — Administrative form for configuring SiteDash connection, authentication, and remote post override.

## Services

### sitedash.client
- **Service ID**: sitedash.client
- **Class**: Drupal\sitedash\Service\SiteDashClient
- **Arguments**:
  - `@http_client` — Guzzle HTTP client for API requests
  - `@config.factory` — Configuration factory for reading/writing settings
  - `@logger.channel.sitedash` — Dedicated logger channel for SiteDash
  - `@messenger` — Messenger service for displaying user messages
  - `@request_stack` — Request stack for accessing current request
- **Purpose**: Main service handling SiteDash API authentication and communication

### logger.channel.sitedash
- **Service ID**: logger.channel.sitedash
- **Class**: Drupal\Core\Logger\LoggerChannel
- **Factory**: logger.factory:get
- **Arguments**: ['sitedash']
- **Purpose**: Dedicated logging channel for SiteDash-specific log entries

## Hooks

### hook_help()
- **Function**: `sitedash_help()`
- **Purpose**: Provides help text for the module
- **Context**: Displays on help.page.sitedash route
- **Description**: Shows basic information about SiteDash as a monitoring and dashboard service

### hook_form_FORM_ID_alter()
- **Function**: `sitedash_form_audit_export_core_settings_form_alter()`
- **Form ID**: audit_export_core_settings_form
- **Purpose**: Alters the Audit Export settings form when remote_post_override is enabled
- **Functionality**:
  - Disables remote_url field and adds description "Remote posting URL is managed by SiteDash"
  - Disables authentication_type field and adds description "Authentication is managed by SiteDash"
  - Adds warning message informing users that remote posting is managed by SiteDash
- **Condition**: Only applies when `remote_post_override` config is TRUE

### hook_audit_export_post_url_alter()
- **Function**: `sitedash_audit_export_post_url_alter()`
- **Purpose**: Modifies the remote posting URL for Audit Export
- **Functionality**: Changes URL to `{base_url}/report/ingest` when override is enabled
- **Condition**: Only applies when `remote_post_override` config is TRUE

### hook_audit_export_post_request_options_alter()
- **Function**: `sitedash_audit_export_post_request_options_alter()`
- **Purpose**: Modifies HTTP request options for Audit Export remote posts
- **Functionality**:
  - Sets timeout to 30 seconds
  - Adds authentication headers:
    - `pubKey`: Public key from config
    - `siteToken`: Site token from config
    - `siteId`: Site ID from config
- **Condition**: Only applies when `remote_post_override` config is TRUE

### hook_audit_export_post_site_info_alter()
- **Function**: `sitedash_audit_export_post_site_info_alter()`
- **Purpose**: Adds SiteDash-specific information to site info payload
- **Functionality**:
  - Adds `site_id` to site info
  - Adds `environment_id` to site info (if available)
- **Condition**: Only applies when `remote_post_override` config is TRUE

## Routes and Permissions

### Routes

#### sitedash.settings
- **Path**: `/admin/config/services/sitedash`
- **Form**: `\Drupal\sitedash\Form\SiteDashSettingsForm`
- **Title**: SiteDash
- **Requirements**: 
  - Permission: `configure sitedash settings`
  - Admin route: TRUE
- **Purpose**: Administrative interface for configuring SiteDash connection and settings

### Permissions

#### configure sitedash settings
- **Title**: Configure SiteDash settings
- **Description**: Allow users to configure SiteDash Monitoring settings.
- **Restrict Access**: TRUE (indicates this is a sensitive permission)
- **Purpose**: Controls access to the SiteDash configuration form

### Menu Links

#### sitedash.settings
- **Title**: SiteDash
- **Description**: Configure SiteDash Monitoring and Dashboard service settings.
- **Parent**: system.admin_config_services (Configuration > Web services)
- **Route**: sitedash.settings
- **Weight**: 100
- **Purpose**: Adds SiteDash configuration link to the admin menu under Web services

## Configuration

### Configuration Object: sitedash.settings

#### Default Values (config/install/sitedash.settings.yml)
```yaml
token: ''                    # Authentication token for initial API authentication
pubkey: ''                   # Public key received from SiteDash after authentication
siteid: ''                   # Site ID assigned by SiteDash
envid: ''                    # Environment ID assigned by SiteDash
base_url: ''                 # SiteDash API backend URL (without trailing slash)
remote_post_override: false  # Whether SiteDash manages Audit Export settings
```

#### Schema Definition (config/schema/sitedash.schema.yml)
- **Type**: config_object
- **Fields**:
  - `token` (string): SiteDash Token
  - `pubkey` (string): Public Key
  - `siteid` (string): Site ID
  - `envid` (string): Environment ID
  - `base_url` (string): SiteDash Backend URL
  - `remote_post_override` (boolean): Override Remote Post

### Configuration Usage
- **Authentication State**: Determined by presence of `pubkey`
- **Remote Post Control**: When `remote_post_override` is TRUE, SiteDash takes control of Audit Export's remote posting
- **API Credentials**: `pubkey`, `siteid`, and `token` are used as headers in API requests

## Forms

### SiteDashSettingsForm
- **Class**: Drupal\sitedash\Form\SiteDashSettingsForm
- **Extends**: ConfigFormBase
- **Route**: sitedash.settings
- **Editable Config**: sitedash.settings

#### Form Structure

**Pre-Authentication State** (when pubkey is empty):
1. **Authentication Fieldset**
   - `base_url` (textfield, required): SiteDash API backend URL
   - `token` (textfield, required): SiteDash authentication token
   - Authenticate button (primary action)

**Post-Authentication State** (when pubkey exists):
1. **Authentication Fieldset**
   - `base_url` (textfield, required): SiteDash API backend URL
   - Site ID (read-only display)
   - Site Key (masked display, shows last 6 characters)
   - Reset Site Key button (danger action)

2. **Remote Post Settings Fieldset**
   - `remote_post_override` (checkbox): Enable SiteDash management of Audit Export settings

#### Form Handlers

**submitForm()**
- Saves base_url, remote_post_override, and token to configuration
- Standard configuration form submit behavior

**authenticateSubmit()**
- Custom submit handler for authentication
- Saves token and base_url
- Calls SiteDashClient::authenticate()

**resetSubmit()**
- Custom submit handler for reset action
- Calls SiteDashClient::resetAuthentication()
- Clears pubkey from configuration

## Classes and Plugins

### PHP Classes

#### SiteDashClient
- **Class**: Drupal\sitedash\Service\SiteDashClient
- **Namespace**: Drupal\sitedash\Service
- **File**: /src/Service/SiteDashClient.php
- **Uses**: StringTranslationTrait
- **Purpose**: Core service for SiteDash API communication

**Dependencies (Constructor Injection)**:
- `ClientInterface $http_client` — Guzzle HTTP client
- `ConfigFactoryInterface $config_factory` — Configuration management
- `LoggerChannelInterface $logger` — Logging service
- `MessengerInterface $messenger` — User messaging
- `RequestStack $request_stack` — Request information

**Public Methods**:

1. **authenticate(string $token): bool**
   - Authenticates site with SiteDash API
   - Sends POST request to `/site/init` endpoint
   - Request payload includes:
     - `sitedashToken`: Authentication token
     - `siteName`: From system.site config
     - `hostName`: From current request
     - `siteId`: Existing site ID (if any)
     - `envId`: Existing environment ID (if any)
   - On success: Saves publicKey, siteId, and environmentId
   - Returns TRUE on success, FALSE on failure

2. **resetAuthentication(): void**
   - Clears pubkey from configuration
   - Displays success message to user

#### SiteDashSettingsForm
- **Class**: Drupal\sitedash\Form\SiteDashSettingsForm
- **Namespace**: Drupal\sitedash\Form
- **File**: /src/Form/SiteDashSettingsForm.php
- **Extends**: ConfigFormBase
- **Purpose**: Administrative configuration form

**Dependencies (Constructor Injection)**:
- `SiteDashClient $sitedash_client` — SiteDash API client service

**Key Methods**:

1. **getFormId()**: Returns 'sitedash_settings_form'
2. **getEditableConfigNames()**: Returns ['sitedash.settings']
3. **buildForm()**: Constructs dynamic form based on authentication state
4. **submitForm()**: Saves base_url, remote_post_override, and token
5. **authenticateSubmit()**: Handles authentication action
6. **resetSubmit()**: Handles reset action

### Plugins

No plugins with annotations (@Plugin, @Block, etc.) were found in this module.

## Drush Commands

No Drush commands are implemented in this module.

## Frontend Assets

No JavaScript, CSS files, or library definitions (*.libraries.yml) are present in this module.

## Utility Functions

### Installation Hooks (sitedash.install)

#### hook_install()
- **Function**: `sitedash_install()`
- **Purpose**: Displays installation success message
- **Action**: Shows message prompting user to configure SiteDash settings

#### hook_uninstall()
- **Function**: `sitedash_uninstall()`
- **Purpose**: Cleanup on module uninstallation
- **Action**: Deletes the sitedash.settings configuration object

## Relationships and Dependencies

### Module Dependencies
- **audit_export:audit_export** — Main Audit Export module (required)
- **audit_export:audit_export_core** — Core audit functionality (required)
- **audit_export:audit_export_post** — Remote post functionality (required)

### Service Dependencies

#### SiteDashClient Service
- **http_client** (Guzzle) → Makes API calls to SiteDash backend
- **config.factory** → Reads/writes module configuration
- **logger.channel.sitedash** → Logs errors and important events
- **messenger** → Displays user feedback messages
- **request_stack** → Gets current hostname for API requests

#### SiteDashSettingsForm
- **sitedash.client** service → Handles authentication and reset operations

### Component Interactions

1. **Authentication Flow**
   - User enters token in `SiteDashSettingsForm`
   - Form calls `SiteDashClient::authenticate()`
   - Client sends POST to SiteDash API `/site/init`
   - API returns publicKey, siteId, environmentId
   - Client saves credentials to configuration
   - Form displays authenticated state

2. **Audit Export Integration**
   - When `remote_post_override` is enabled:
     - `hook_form_alter` disables Audit Export's remote settings
     - `hook_audit_export_post_url_alter` changes URL to SiteDash endpoint
     - `hook_audit_export_post_request_options_alter` adds auth headers
     - `hook_audit_export_post_site_info_alter` adds site/env IDs

3. **Configuration Management**
   - All settings stored in `sitedash.settings` config object
   - Form uses ConfigFormBase for standard config handling
   - Authentication state determined by presence of `pubkey`
   - Base URL can be changed even after authentication

4. **Permission System**
   - Single permission controls all module access
   - Route requires `configure sitedash settings` permission
   - Menu link inherits permission from route

### Data Flow

1. **Initial Setup**
   - Admin navigates to `/admin/config/services/sitedash`
   - Enters backend URL and token
   - Submits form for authentication

2. **Authentication Process**
   - Token + site info sent to SiteDash API
   - API validates and returns credentials
   - Credentials stored in Drupal config

3. **Audit Export Override**
   - Admin enables remote_post_override
   - Audit Export posts now go to SiteDash
   - SiteDash headers added automatically

### External API Integration

**SiteDash API Endpoints**:
- `POST {base_url}/site/init` — Initial authentication
- `POST {base_url}/report/ingest` — Audit data submission (via Audit Export)

**Request Headers** (when override enabled):
- `pubKey`: Public key from config
- `siteToken`: Original authentication token
- `siteId`: Site identifier from SiteDash

**Request Payloads**:
- Authentication: sitedashToken, siteName, hostName, siteId, envId
- Audit Export: Standard audit data + site_id, environment_id

## Module Summary

The SiteDash module is a Drupal 10/11 integration module that connects Drupal sites with the SiteDash.io monitoring and dashboard service. Its primary functions are:

1. **Authentication Management**: Provides a secure way to authenticate Drupal sites with the SiteDash API using tokens and public key authentication.

2. **Audit Export Integration**: Seamlessly integrates with the Audit Export module to redirect audit data to SiteDash when the remote post override is enabled.

3. **Configuration Interface**: Offers an administrative UI for managing SiteDash connection settings, authentication status, and integration options.

### Key Features

- **Token-based Authentication**: Initial authentication using a SiteDash token that returns a public key for subsequent API calls
- **Audit Export Override**: Optional feature to redirect Audit Export data to SiteDash's ingestion endpoint
- **Secure Credential Storage**: Uses Drupal's configuration system to securely store API credentials
- **User-friendly Admin Interface**: Provides clear feedback on authentication status and configuration options
- **Error Handling**: Includes proper error logging and user messaging for API communication issues

### Technical Architecture

- **Service-oriented**: Uses Drupal's service container for dependency injection
- **Hook-based Integration**: Leverages Drupal hooks to integrate with Audit Export without modifying its code
- **ConfigFormBase Pattern**: Follows Drupal best practices for configuration forms
- **No Frontend Assets**: Pure backend integration with no JavaScript or CSS requirements
- **Minimal Footprint**: Lightweight module with focused functionality

This module serves as a bridge between Drupal's Audit Export functionality and the SiteDash monitoring service, enabling centralized monitoring and management of Drupal sites through the SiteDash platform.
