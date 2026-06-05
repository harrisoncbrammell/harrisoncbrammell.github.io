# Agent Guidelines for al-folio

A simple, clean, and responsive Jekyll theme for academics.

## Quick Links by Role

- **Are you a coding agent?** → Read [`.github/copilot-instructions.md`](.github/copilot-instructions.md) first (tech stack, build, CI/CD, common pitfalls & solutions)
- **Customizing the site?** → See [`.github/agents/customize.agent.md`](.github/agents/customize.agent.md)
- **Writing documentation?** → See [`.github/agents/docs.agent.md`](.github/agents/docs.agent.md)
- **Need setup/deployment help?** → [INSTALL.md](INSTALL.md)
- **Troubleshooting & FAQ?** → [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- **Customization & theming?** → [CUSTOMIZE.md](CUSTOMIZE.md)
- **Quick 5-min start?** → [QUICKSTART.md](QUICKSTART.md)

## Essential Commands

### Local Development (Docker)

The recommended approach is using Docker.

```bash
# Initial setup & start dev server
docker compose pull && docker compose up
# Site runs at http://localhost:8080

# Rebuild after changing dependencies or Dockerfile
docker compose up --build

# Stop containers and free port 8080
docker compose down
```

### Pre-Commit Checklist

Before every commit, you **must** run these steps:

1.  **Format Code:**
    ```bash
    # (First time only)
    npm install --save-dev prettier @shopify/prettier-plugin-liquid
    # Format all files
    npx prettier . --write
    ```
2.  **Build Locally & Verify:**

    ```bash
    # Rebuild the site
    docker compose up --build

    # Verify by visiting http://localhost:8080.
    # Check navigation, pages, images, and dark mode.
    ```

## Critical Configuration

When modifying `_config.yml`, these **must be updated together**:

- **Personal site:** `url: https://username.github.io` + `baseurl:` (empty)
- **Project site:** `url: https://username.github.io` + `baseurl: /repo-name/`
- **YAML errors:** Quote strings with special characters: `title: "My: Cool Site"`

## Development Workflow

- **Git & Commits:** For commit message format and Git practices, see [.github/GIT_WORKFLOW.md](.github/GIT_WORKFLOW.md).
- **Code-Specific Instructions:** Consult the relevant instruction file for your code type.

| File Type                                     | Instruction File                                                                                |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Markdown content (`_posts/`, `_pages/`, etc.) | [markdown-content.instructions.md](.github/instructions/markdown-content.instructions.md)       |
| YAML config (`_config.yml`, `_data/`)         | [yaml-configuration.instructions.md](.github/instructions/yaml-configuration.instructions.md)   |
| BibTeX (`_bibliography/`)                     | [bibtex-bibliography.instructions.md](.github/instructions/bibtex-bibliography.instructions.md) |
| Liquid templates (`_includes/`, `_layouts/`)  | [liquid-templates.instructions.md](.github/instructions/liquid-templates.instructions.md)       |
| JavaScript (`_scripts/`)                      | [javascript-scripts.instructions.md](.github/instructions/javascript-scripts.instructions.md)   |

## Projects Section (`_projects/`)

Each project is a Markdown file in `_projects/`. Files are sorted by `importance` (ascending) within each category.

### Front Matter Reference

| Field                  | Required | Description                                                                 |
| ---------------------- | -------- | --------------------------------------------------------------------------- |
| `layout: page`         | yes      | Always `page`                                                               |
| `title`                | yes      | Displayed as the card title and page heading                                |
| `description`          | yes      | Short blurb shown on the card                                               |
| `importance`           | yes      | Integer — lower number = higher position in the grid                        |
| `category`             | yes      | Must match a value in `display_categories` in `_pages/projects.md` (currently `work` or `fun`) |
| `img`                  | no       | Path to thumbnail image (e.g. `assets/img/foo.jpg`); omit for no thumbnail  |
| `redirect`             | no       | External URL — clicking the card goes here instead of the project page      |
| `github`               | no       | GitHub repo URL — renders a GitHub icon with link on the card               |
| `github_stars`         | no       | GitHub repo identifier (e.g. `username/repo`) to display live star count    |
| `giscus_comments: true`| no       | Enables Giscus comment section at the bottom of the project page            |
| `related_publications: true` | no | Pulls related bibliography entries onto the project page               |

### Categories

Categories are defined in `_pages/projects.md` under `display_categories`. Currently: `work`, `fun`. To add a new category, add it to that list and use it as the `category` value in project files.

### Naming Convention

Files are named `<N>_project.md` where `N` is a number. The number in the filename is just for ordering in the filesystem — actual display order is controlled by `importance`.

### Image Best Practices

- Store project images in `assets/img/`
- Use `loading="eager"` for above-the-fold images, lazy loading is enabled site-wide otherwise
- Wrap images in Bootstrap grid rows: `<div class="row">` → `<div class="col-sm mt-3 mt-md-0">`
- Include `{% include figure.liquid ... class="img-fluid rounded z-depth-1" %}` for consistent styling
- Follow a `<div class="caption">` block with caption text after each row

## Writing Voice

Harrison's writing style for project posts and site copy:

- **First-person, conversational, technically honest** — write like a competent person talking through what they built and what they learned, not a resume.
- **Light humor, sprinkled in** — not constant jokes, but a dry or self-deprecating line here and there. The "many ways I procrastinate" description for the projects page is a good benchmark for tone.
- **Acknowledge mistakes and hindsight** — he values candor ("in hindsight I would have...") over polished self-promotion.
- **Don't over-explain** — assume technical literacy; he's a CS person who sometimes ventures into adjacent domains.

When writing any site copy or project posts, keep this voice in mind. Avoid template-sounding filler text.

## Common Issues

For troubleshooting, see:

- [Common Pitfalls & Workarounds](.github/copilot-instructions.md#common-pitfalls--workarounds) in copilot-instructions.md
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for detailed solutions
- [GitHub Issues](https://github.com/alshedivat/al-folio/issues) to search for your specific problem.
