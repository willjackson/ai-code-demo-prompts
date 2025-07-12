# Prompt for AI — Phase 1: Drupal Module Discovery

You are being asked to review a custom Drupal module and produce a complete, standalone documentation file named `drupal-module-details-sd.md`.

This document will serve as the **authoritative reference** for building an equivalent plugin in WordPress (called `sitedash`) at a later stage. You will not build the WordPress plugin yet. Instead, your task is to fully understand and map the Drupal module’s functionality, architecture, and how its components work together.

---

## Files Available to You

You are provided with two instruction files:

1. `drupal-module-instructions.md`  
   👉 This is your **primary directive**. Follow it precisely to guide your investigation. It will tell you how to inspect each file and what information to extract and record.

2. `general-instructions.md`  
   👉 This gives you **context** on the end goal. It describes how the Drupal module will eventually be re-implemented as a WordPress plugin called `sitedash`. This is for awareness only and should help guide the relevance of the details you extract, but you are **not building anything yet**.

---

## Your Task: Discover and Document the Drupal Module

Your goal is to perform a deep analysis of the Drupal module located in the `drupal_module/` directory. You must:

1. **Examine every file and subdirectory**
    - Determine the purpose of each file.
    - Identify how each piece of the module contributes to the overall functionality.

2. **Record how the module is integrated into Drupal**
    - What services, hooks, routes, permissions, forms, config, and classes are involved?
    - How do the different parts of the module interact with one another?
    - What dependencies or patterns does the module rely on?

3. **Use a consistent, structured format**
    - Follow the formatting guidance and section breakdown provided in `drupal-module-instructions.md`.
    - Use Markdown code blocks, lists, and subheadings where appropriate.
    - Be verbose and precise in your descriptions. Future steps will rely on this document exclusively.

4. **Avoid assumptions or summaries**
    - Every detail should be derived directly from the module’s source code.
    - Do not paraphrase or skip files; process each file and its purpose explicitly.

---

## Output: `drupal-module-details-sd.md`

This is the document you will write.

It should:

- Include a section for every structural category outlined in `drupal-module-instructions.md`.
- Provide enough detail that another developer (or AI) could rebuild the module in another system without needing to look at the original code.
- Clearly describe relationships between parts (e.g., how a controller calls a service, or how a route connects to a form).
- Be self-contained, well-organized, and cleanly formatted in Markdown.

---

## What Comes Next

You are **not** building or planning the WordPress plugin yet.

Once `drupal-module-details-sd.md` is complete, it will be used — alongside `general-instructions.md` — to create a detailed build plan for the `sitedash` WordPress plugin. That will be part of a separate effort.

---

## Summary Checklist

✅ Review all files in `drupal_module/`  
✅ Follow `drupal-module-instructions.md` step-by-step  
✅ Reference `general-instructions.md` only for broader awareness  
✅ Produce a clean, modular `drupal-module-details-sd.md` with deep detail  
✅ Do not perform any implementation or transformation yet

---

Begin your discovery process now.
