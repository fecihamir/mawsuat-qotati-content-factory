# موسوعة قططي - AI Content Factory

This is a local, file-based Arabic content production system for the Mawsu'at Qotati cat knowledge website. The Coordinator receives one keyword or public URL, delegates the six-stage workflow, and stops at `PENDING_APPROVAL`. V1 produces no automatic publication or deployment.

## Commands

- `npm install --prefix .opencode` - Install the local OpenCode plugin dependency
- `opencode run --command content "لماذا تخرخر القطط؟"` - Start a keyword job
- `/content https://example.com/article` - Start an article-URL job in the OpenCode TUI
- `/content https://www.youtube.com/watch?v=...` - Start a YouTube-URL job in the OpenCode TUI
- `opencode debug config` - Inspect the resolved OpenCode configuration

There is no build, test, or deploy command for the factory itself. The requested end-to-end validation is performed through the `content` command and artifact audit.

## Pipeline

`INPUT -> RESEARCH -> EVIDENCE / FACT CHECK -> BRIEF -> WRITING / EDITING -> SEO / QA -> HUMAN APPROVAL -> STRUCTURED OUTPUT`

The project defines exactly six agents:

1. `coordinator` - Orchestration and gate enforcement
2. `researcher` - Source collection and claim inventory
3. `fact-checker` - Evidence decisions and traceability
4. `content-strategist` - Search-informed article brief
5. `writer-editor` - Arabic draft and editorial revision
6. `seo-qa` - SEO metadata and final quality checks

## Workspace

Each run uses `.opencode/workspace/jobs/<job-id>/`:

| Path | Artifact owner |
|---|---|
| `input/` | Coordinator: validated request and job metadata |
| `research/` | Researcher: source register and research notes |
| `evidence/` | Fact Checker: machine-readable claim decisions and report |
| `brief/` | Content Strategist: article plan |
| `draft/` | Writer/Editor: first draft |
| `edited/` | Writer/Editor: edited article, compatible JSON, and claim map |
| `seo/` | SEO/QA: metadata, claim audit, and findings |
| `approval/` | Coordinator: human review state and checklist |
| `published/` | Created only after explicit human approval; local structured output only |

The base `.opencode/workspace/{research,briefs,drafts,reports,data}` directories are retained for compatibility, but job directories are the authoritative handoff mechanism.

## Editorial Rules

- Core principle: `SOURCE != TRUTH`.
- Write in clear, natural Modern Standard Arabic for a broad Arab audience.
- A source URL is evidence to evaluate, not an authority to repeat.
- Important claims require source IDs, evidence, agreement or conflict, a decision status, a reason, and writer guidance.
- Only `APPROVED` and `QUALIFIED` evidence may reach the brief or article. `REJECTED` and `NEEDS_RESEARCH` claims are excluded.
- Do not invent studies, statistics, quotations, authorities, or corrections.
- Use an original Mawsu'at Qotati voice. Do not imitate a named writer, competitor, journalist, or YouTuber.

## Taxonomy

Allowed top-level categories:

- `breeds` - سلالات القطط
- `food` - تغذية وأطعمة القطط
- `behavior` - سلوك ولغة الجسد
- `care` - رعاية ونظافة القطط
- `kittens` - القطط الصغيرة
- `questions` - أسئلة القطط
- `names` - أسماء القطط

V1 excludes a top-level medical or health category, veterinary positioning or diagnosis, and mating/pregnancy/birth content. Health-adjacent topics must remain cautious educational content without veterinary authority claims.

## Website Compatibility

The only external project permitted for compatibility inspection is `D:\mycatkitty`. It is a Next.js 15 App Router TypeScript project with data-driven content in `src/data/`, types in `src/types/`, and article routing at `src/app/cats/[category]/[slug]/page.tsx`. Its `Article` interface in `src/types/article.ts` uses Arabic title/excerpt fields, category and slug fields, sections made of typed content blocks, FAQs, related links, and optional SEO overrides. The factory's `edited/article.json` follows this shape closely and remains local until human approval.

Never modify, install into, deploy, or change configuration or environment variables in `D:\mycatkitty`. Do not inspect any other external project.

## Approval

The Coordinator must leave every completed run at `approval/status.json` with `state: "PENDING_APPROVAL"`. Review the article, evidence report, sources, claim audit, SEO metadata, and warnings. To approve, explicitly name the job in a later Coordinator message, for example `approve job-20260902-120000`. The Coordinator may then create only the local `published/structured-output.json`. `CHANGES_REQUESTED` routes the work back to an existing stage; `REJECTED` ends the job. No state permits deployment.

## Model

The requested `opencode/deepseek-v4-flash-free` model was not present in the installed OpenCode model catalog. V1 therefore uses the available free fallback `opencode/mimo-v2.5-free`, explicitly configured on all six agents. Provider credentials stay in OpenCode's native credential store; never add them to this repository.

## Tooling and Scope

- OpenCode built-ins (`read`, `write`, `edit`, `glob`, `grep`, `websearch`, and `webfetch`) are preferred.
- V1 creates no custom tools, MCP servers, publishing integrations, databases, or Cloudflare configuration.
- URL research must use public HTTPS resources with no embedded credentials and must avoid local/private/reserved network targets.
- Agents have least-privilege write access to their own job-stage directory. The Coordinator is the only agent that can write job control files.
