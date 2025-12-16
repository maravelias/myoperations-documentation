# myoperations-documentation
MyOperations project documentation site powered by MkDocs.

## Project Organization
- `mkdocs.yml`: MkDocs configuration (site metadata, navigation, build rules).
- `docs/`: Markdown content rendered into the site homepage and future sections.
- `AGENTS.md`: Responsibilities and scope of the Documentation Automation Agent.
- `README.md`: Contributor-facing overview, run instructions, and change history.

## Dependencies
- Python 3.12+
- `mkdocs>=1.6`
- `mkdocs-material>=9.5`
- `mkdocs-kroki-plugin>=0.9`
Install them with `pip install mkdocs mkdocs-material mkdocs-kroki-plugin`.

## Running the Site Manually
1. Install the dependencies listed above.
2. From the repo root run `mkdocs serve` for live preview at `http://127.0.0.1:8000`.
3. Run `mkdocs build` to generate static assets in the `site/` directory.

## Diagram Workflow (PlantUML via Kroki)
- Create diagrams in Markdown fenced blocks such as:
  ```plantuml
  @startuml
  actor User
  User -> System: Requests action
  System --> User: Result
  @enduml
  ```
- MkDocs Material + the Kroki plugin render the diagram during `mkdocs serve/build`; no local PlantUML install required.
- Supported Kroki diagram syntaxes include PlantUML, Mermaid, BPMN, Excalidraw, and more. Use the matching fence language (e.g., `mermaid`) to render them.

## Change History
| Date       | Change                                                                 |
|------------|------------------------------------------------------------------------|
| 2025-12-16 | Initialized MkDocs project, documented agent role, and updated README. |
| 2025-12-16 | Enabled MkDocs Material theme + Kroki diagrams and documented workflow. |
| 2025-12-16 | Removed requirements.txt and documented dependencies directly in README. |
