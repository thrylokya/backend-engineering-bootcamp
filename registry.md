# Registry

This registry documents important resources, packages, services, and external dependencies used by the backend-engineering-bootcamp repository. Use this file to locate important tools, third-party services, and references relevant to the project and the bootcamp material.

## How to use

- Add a new entry for any dependency, tool, or external resource used by the repository.
- Keep entries concise and include a link when applicable.
- Maintain licenses and contact information where available.

## Template

- Name: 
- Type: (library, service, tool, dataset, external-article, boilerplate, other)
- Description: One-sentence summary of what this is and why it's used.
- Source / Link: URL to the project or resource
- Version: (if applicable)
- License: (if known)
- Maintainer / Contact: (optional)
- Notes: (how it's used in this repo, additional context)

## Example entries

- Name: Node.js
  - Type: runtime
  - Description: JavaScript runtime used for backend examples and exercises.
  - Source / Link: https://nodejs.org/
  - Version: 18.x
  - License: MIT
  - Maintainer / Contact: Node.js Maintainers
  - Notes: Used in several sample projects and exercises. See package.json files in examples/ for exact pinned versions.

- Name: PostgreSQL
  - Type: database
  - Description: Relational database used for persistence in many exercises.
  - Source / Link: https://www.postgresql.org/
  - Version: 15
  - License: PostgreSQL
  - Maintainer / Contact: PostgreSQL Global Development Group
  - Notes: Example docker-compose files are provided under infra/.

- Name: Docker
  - Type: tool
  - Description: Container runtime used for local development and CI examples.
  - Source / Link: https://www.docker.com/
  - Version: 24.x
  - License: Apache-2.0 (components)
  - Notes: See ./dev/README.md for how to run the examples with Docker.

## Additions and maintenance

- When adding a new entry, follow the template above and keep the **Version**, **License**, and **Source / Link** fields populated when possible.
- Update this file whenever a dependency or external resource is added, removed, or upgraded.

---

If you'd like, I can scan the repository for dependencies (package.json, requirements.txt, go.mod, etc.) and populate this registry automatically with detected packages and versions.