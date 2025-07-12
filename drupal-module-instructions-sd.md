# Drupal Module Investigation Instructions

## Objective

Perform a comprehensive analysis of the Drupal module contained in the `drupal_module/` directory. The purpose is to understand how the module works as a whole and to document every detail in a reference file named `drupal-module-details.md`.

This file will serve as a master reference for reproducing the module's functionality in another platform, such as WordPress.

---

## General Guidance

- Read and document every file in the module, including YAML, PHP, and configuration files.
- Track how each file interacts with others (e.g., services calling plugins, controllers loading configuration).
- Maintain a modular and structured record in `drupal-module-details.md`.

---

## 1. Identify Module Metadata

Start with the `.info.yml` file:

- Document:
    - Module name
    - Description
    - Core compatibility
    - Declared dependencies
- Note the purpose of the module if described.

### Example Entry
```markdown
## Metadata
- Name: ...
- Core: ...
- Description: ...
- Dependencies: [...]
```

---

## 2. List and Describe All Files

For each file in the module:

- Record:
    - File name
    - File path
    - File type (e.g. PHP class, YAML configuration, hook implementation)
    - Brief description of what the file does
- Include relative location and role in the system

Organize files by folder to understand structure.

---

## 3. Service Definitions

If `*.services.yml` exists:

- List each declared service
    - Service ID
    - Class used
    - Tags (e.g., event_subscriber, plugin.manager)
    - Dependencies (arguments)

---

## 4. Hook Implementations

Search `.module` files and others for hook implementations:

- List each hook
- Describe its purpose and context
- Identify which other components it interacts with (e.g., forms, routes)

---

## 5. Routing and Permissions

If `*.routing.yml` or `*.permissions.yml` files exist:

- Document all routes:
    - Paths
    - Controller class/method
    - Required permissions

- Document all defined permissions:
    - Permission key
    - Title and description

---

## 6. Form and Configuration Handling

- Identify all forms (via `FormBase`, `ConfigFormBase`, or hooks)
- List configuration objects used
- Note how settings are read/written
- Record default values and config schema if available

---

## 7. PHP Classes and Their Roles

- List all classes in `/src` or elsewhere
    - Class name
    - Namespace
    - File location
    - Interfaces or base classes implemented
    - Main public methods and their purpose

---

## 8. Plugins and Annotations

If any files use `@Plugin`, `@Block`, or similar:

- Record:
    - Plugin type
    - Annotation properties
    - Class purpose and behavior

- Explain how Drupal discovers or uses the plugin

---

## 9. Drush Commands (if present)

If custom Drush commands are implemented:

- List each command
    - Command name
    - Description
    - Available options/arguments
    - Expected result or behavior

---

## 10. JavaScript, CSS, and Libraries

If `*.libraries.yml` or frontend assets exist:

- List defined libraries
- File paths for JS/CSS
- When/where they are attached (e.g., via hooks, forms, or routes)

---

## 11. Helper or Utility Code

- Document any global helper functions or utility classes
- Note how and where they are used in the module

---

## 12. Relationships and Dependencies

- Track how components depend on each other
    - Services calling plugins
    - Forms using config
    - Controllers dispatching events
- Document any observed coupling or patterns

---

## 13. Update `drupal-module-details.md`

- For each step above, record your findings in a structured and human-readable format
- Use Markdown formatting with sections, sub-sections, and code blocks where needed
- This document should be self-contained and describe the full architecture and functionality of the module

---

## Example Format

```markdown
# Drupal Module Reference

## Metadata
...

## File Inventory
- /src/Controller/AdminController.php — Defines the admin UI logic
- /templates/settings.html.twig — Outputs settings form

## Services
...

## Hooks
...

## Configuration
...

## Classes and Plugins
...

## Forms
...

## Routes and Permissions
...

## Frontend Assets
...

## Utility Functions
...

## Relationships and Dependencies
...
```

---

## Goal

Once complete, `drupal-module-details.md` should provide a full understanding of how the module is structured, what each piece does, and how they work together. This will act as a foundation for recreating the functionality in another platform.

Do not omit or assume functionality. Read every file. Reference internal APIs only as needed. This is a technical investigation and mapping process.

