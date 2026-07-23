---
name: "formatly-blog-writer"
description: "Write and publish Formatly blog posts following brand voice, SEO rules, templates, SVG cover generation, and Supabase publishing."
---

# Formatly Blog Writer Skill

> Standard Operating Procedure for writing, formatting, and publishing blog posts on FormatlyApp.com.
> Any agent with access to this skill and the Supabase service role key can publish blog posts that match Formatly's brand.

**Repository:** `github.com/Faithmanex/formatly-agent-skills` — this file is `skills/formatly-blog-writer/SKILL.md`.
Canonical references are in `references/`. All agents should `git pull` the repo before each session.

## Required Reading Order

**Three references, each with a specific role — read all before writing:**

| Order | File | What It Covers |
|-------|------|----------------|
| 1st | **`/data/workspace/website/BLOG_CONTENT_FORMATTING.md`** | Canonical HTML rules — headings, typography, links, lists, tables, callouts, prohibited elements. **Single source of truth for the `content` field. Read FIRST.** |
| 2nd | **`/data/workspace/website/blog_planning_strategy.md`** | Content strategy, audience segments, content pillars, topic bank, calendar, SEO plan, social distribution. **Reference for what to write and why.** |
| 3rd | **This skill (`SKILL.md`)** | Workflow reference — templates, tone, cover SVG generation, topic generation, Supabase publishing pipeline. **How to do it.** |

**Conflict resolution:** `BLOG_CONTENT_FORMATTING.md` > `blog_planning_strategy.md` > this skill. The repo guide (`BLOG_CONTENT_FORMATTING.md`) is what ships on the live site.

### ⚡ Output Rule: No Schema/SEO Metadata in Content

The `blog_posts.content` column stores the visible article HTML only. **Do not include** SEO metadata, JSON-LD, Schema.org blocks, `<script>` tags, or HTML comment blocks in the content. The page component handles all of that from the table columns.

---

## 1. Quick Reference

| Field | Rule |
|-------|------|
| **Categories** | `Style Guides`, `Writing Tips`, `Product Guides` |
| **Default Author** | `Formatly Editorial Team` |
| **Default Read Time** | `5 min read` (adjust based on word count: ~200 words/min) |
| **Target word count** | 800–2000 words (pillar articles 2000+) |
| **Total published posts** | 94 (as of July 23, 2026) |
| **Total user profiles** | 138 |
| **Cover image format** | SVG stored in Supabase Storage at `blog/covers/` |
| **Publishing** | INSERT into Supabase `blog_posts` table with `published=true` |

---

## 2. Tone & Voice

### Core Rules
- **Personality**: Confident but not arrogant — authoritative on formatting rules, humble about scope
- **Empathy**: Always acknowledge the pain (lost hours, grading penalties, frustration) before presenting the solution
- **Directness**: No fluff, no jargon, honest about what Formatly does and doesn't do
- **Academic register**: Speaks the language of researchers but remains accessible to undergrads
- **CTA tone**: Benefit-first, never pushy: "Upload your paper and get perfectly formatted APA in 30 seconds"

### Writing Conventions
- **Start every article** with an H2 hook that addresses the reader's pain point directly
  - Example: *"Have you ever faced the dread of grading anxiety over margins, headings, and citations?"*
- **Short paragraphs**: 2–4 sentences, max 5 lines each, one idea per paragraph
- **Conversational but authoritative** — write like an expert who actually uses the tools
- **Use "you"** to address the reader directly
- **Avoid jargon** unless it's common academic terminology
- **Don't use markdown tables** in content — use `<table>` elements

### What Not to Do
- No hype or superlatives ("game-changing", "revolutionary")
- No generic AI slop — be specific about rules and examples
- Don't pretend Formatly does things it doesn't (e.g., proofreading, content generation)
- Don't bash competitors with ad hominem — use factual comparisons

---

## 3. Content Strategy & Audience

### Target Audience Segments

| Segment | Pain Point | Content Angle |
|---------|-----------|---------------|
| Undergraduates (18–22) | First research papers, unfamiliar with citation styles | Beginner guides, step-by-step tutorials, "how to" articles |
| Graduate students (22–30) | Thesis/dissertation formatting, publication requirements | Advanced guides, comparison posts, productivity tips |
| PhD candidates & researchers (25–40) | Journal submission formatting, hours wasted on compliance | Publisher-ready output guides, rule-based automation deep-dives |
| Academic professionals (30–60) | Journal-specific guidelines, departmental standards | Custom styles, AI feedback, institutional use cases |
| Self-published authors (25–55) | KDP/manuscript formatting complexity | Print-ready formatting guides, comparison vs design tools |
| Freelance editors (25–50) | Deliver faster, compliant drafts | Efficiency workflows, batch processing tips |

### Content Pillars & Categories

| Strategic Pillar | Blog Category | Focus |
|-----------------|---------------|-------|
| **Educational** | `Style Guides` (primary), `Writing Tips` (secondary) | Citation style rules, formatting best practices, academic writing help |
| **Product** | `Product Guides` | Feature explainers, tutorials, use cases, comparisons |
| **Thought Leadership** | `Writing Tips` | Academic trends, research productivity, AI in academia |
| **SEO-driven** | Cross-cuts all categories | High-intent keyword targeting |

### Content-to-Conversion Funnel

```
Awareness (SEO, social, community)
  → Topical guides, how-to articles, style references
  → Target: "how to format APA" searchers, Reddit users
    ↓
Consideration (comparison, feature deep-dives)
  → Formatly vs X comparisons, feature walkthroughs, use cases
  → Target: users evaluating tools
    ↓
Conversion (tutorials, case studies, CTAs)
  → "How to format in 30 seconds" tutorials, ROI calculators
  → Target: users ready to try
    ↓
Retention (advanced tips, product updates)
  → Advanced formatting guides, changelogs
  → Target: existing free-tier users → Pro upgrade
```

### Search Intent Mapping

| Intent | % of Keywords | Content Type | CTA Strategy |
|--------|---------------|--------------|--------------|
| Informational (how to, what is) | 60% | Guides, tutorials | Soft: mid-article "try Formatly" |
| Commercial (vs, best, review) | 25% | Comparisons, reviews | Strong: side-by-side pricing |
| Transactional (online tool, free) | 10% | Tool landing pages | Direct: "Upload & Format" |
| Navigational (Formatly login) | 5% | N/A (landing pages) | N/A |

---

## 4. Article Templates

### 4.1 Standard Style Guide / Educational Post

Used for: Style guides, educational content, thought leadership

```
TITLE (≤70 chars, primary keyword first)

EXCERPT (≤160 chars, plain text, includes primary keyword naturally)

STRUCTURE:
<h2> Engaging hook (1 paragraph, addresses pain point directly)
  → "Have you ever faced the dread of [specific pain]?"

<h2> Understanding [Topic] (defines the problem/context)
  → 2–3 paragraphs explaining why this matters

<h2> [Section 1] (first key point)
  → Paragraphs + bullet lists or tables
  → Include 1 expert tip or callout box (opt-in style="" only for callouts)

<h2> [Section 2] (second key point)
  → Same pattern

<h2> [Section 3] (third key point)
  → Same pattern

<h2> [Optional: Comparison table]
  → Table with 3+ columns per table HTML rules

<h2> Simplify [Topic] with Formatly (soft CTA)
  → 1 paragraph acknowledging pain + 1 sentence CTA
  → Internal link to /auth/register

CTA PLACEMENT (per strategy doc):
  - Mid-article (after H2 section 2): soft CTA, contextual
  - End of article: strong CTA, conversion-focused
  - Final paragraph: benefit-driven link to /auth/register
```

### 4.2 Tutorial / How-To

Used for: Step-by-step guides, feature walkthroughs

```
TITLE: How to {Action} in {Timeframe} (≤70 chars)

EXCERPT (≤160 chars, starts with "Learn how to {action}...")

STRUCTURE:
<h2> Engaging hook (pain point + promise of solution)
  → "Spending hours manually [pain]?"

<h2> What You'll Need (prerequisites)
  → Bullet list (e.g., "A .docx manuscript, a Formatly account")

<h2> Step-by-Step Guide
  → Each step = <h3> heading
    <h3>Step 1: [Action]</h3>
    <p>[1–2 paragraphs explaining the step]</p>
  → 3–7 steps total

<h2> Pro Tips / Troubleshooting
  → FAQ-style bullets for common issues

<h2> CTA Section
  "Ready to skip the manual work? Upload your {document type} and get {result} in 30 seconds."
```

### 4.3 Comparison / Listicle

Used for: Formatly vs Competitor, "X vs Y", feature comparison

```
TITLE: Formatly vs {Competitor}: The Complete {Year} Comparison (≤70 chars)

EXCERPT (≤160 chars, includes both brand names + key differentiator)

STRUCTURE:
<h2> Engaging hook
  → "Choosing between {Product A} and {Product B}? Here's what you need to know."

<h2> At a Glance (TL;DR table)
  → 2-column table: Feature | Winner

<h2> {Product A} Overview (2–3 paragraphs)
  → What it is, who it's for, key strengths, key weaknesses

<h2> {Product B} Overview (2–3 paragraphs)
  → Same structure

<h2> Head-to-Head Comparison
  → Subsections (<h3>) for each dimension:
    <h3>Pricing</h3>
    <h3>Speed &amp; Turnaround</h3>
    <h3>Citation Styles Supported</h3>
    <h3>Document Formatting Scope</h3>
    <h3>User Experience</h3>
    <h3>Privacy &amp; Security</h3>

<h2> Comparison Table (full detail)
  → Table with 6+ rows, 3 columns (Feature | Product A | Product B)

<h2> Who Should Choose Which?
  → "Choose Formatly if... Choose {Competitor} if..."

<h2> Final Verdict
  → Bold recommendation paragraph + CTA
```

### 4.4 FAQ Post

Used for: Common questions, People Also Ask targeting

```
TITLE: {Topic} FAQs: Everything You Need to Know (≤70 chars)

STRUCTURE:
<h2> Introduction (1 paragraph hook + context)

<h2> Frequently Asked Questions
  → Each Q&A pair:
    <h3>{Question}?</h3>
    <p>{Answer in 2–3 paragraphs, include internal links}</p>
  → Minimum 8 questions, ideal 10–15

<h2> Still Have Questions? (CTA section)
```

---

## 5. HTML & Formatting Rules

> ⚠️ **This section duplicates key rules from `BLOG_CONTENT_FORMATTING.md`. The repo guide is authoritative — always read it first before writing. These are reminders.**

### Allowed HTML Tags
Only: `h2`, `h3`, `p`, `ul`, `ol`, `li`, `table`, `tr`, `td`, `th`, `strong`, `em`, `a`, `div` (callout boxes only)

### Prohibited Elements
| Element | Why |
|---------|-----|
| `<h1>` | Conflicts with page-level title |
| `<style>` or inline `style=""` (except callout boxes) | Breaks `.blog-content` design system |
| `<script>` / JSON-LD | Handled by page component; raw = visible text |
| `<!-- SEO METADATA -->` | Renders as visible text |
| `class=""` on non-link elements | `.blog-content` handles styling |
| Empty tags (`<p></p>`) | Creates unwanted spacing |
| Base64 images | Bloats content. Use cover SVGs |
| `<hr>` | Not part of prose design |

### Link Format
```html
<a href="/blog/related-post" class="text-[#006ee5] font-medium underline hover:text-[#0056b8]">descriptive text</a>
```

- No "click here" or "read more"
- Internal blog: `/blog/slug`
- Internal pages: `/pricing`, `/features`, `/auth/register`
- External: full URL + `rel="nofollow"`
- Minimum per post: 2 internal blog links + 1 internal page link

### Character Escapes
Use HTML entities — never straight quotes or double hyphens:
`&ldquo;` `&rdquo;` `&lsquo;` `&rsquo;` `&mdash;` `&ndash;` `&amp;` `&hellip;` `&deg;` `&Prime;`

### Callout Boxes (the only place inline styles are allowed)
```html
<div style="background: #f0f7ff; border-left: 4px solid #006ee5; padding: 16px 20px; margin: 24px 0; border-radius: 0 8px 8px 0;">
  <p style="margin: 0;"><strong>Expert Tip:</strong> Text of the tip goes here.</p>
</div>
```

Use max 1–2 per post. Never nest inside a table or list.

### Table HTML
```html
<table class="min-w-full border rounded-lg overflow-hidden my-6">
  <thead class="bg-muted">
    <tr>
      <th class="px-4 py-2 text-left text-xs font-semibold uppercase">Header 1</th>
      <th class="px-4 py-2 text-left text-xs font-semibold uppercase">Header 2</th>
    </tr>
  </thead>
  <tbody class="text-sm">
    <tr>
      <td class="px-4 py-2 font-semibold">Label</td>
      <td class="px-4 py-2">Value or description.</td>
    </tr>
  </tbody>
</table>
```

### Section Order
```
1. <h2> Hook (pain-point question or scenario — 1 paragraph)
2. <h2> Context / Definition (2–3 paragraphs)
3. <h2> Major sections (2–5) with <h3> subsections as needed
4. <h2> CTA Section (pain acknowledgment + Formatly solution + /auth/register link)
```

---

## 6. SEO & Metadata

SEO data (slug, title, excerpt, category, author, dates, cover_image, read_time) is supplied as `blog_posts` table columns when inserting a post — never embedded in the `content` HTML. The page component (`app/blog/[slug]/page.tsx`) builds `<head>` metadata and JSON-LD from those columns at render time.

### Slug Rules
- Kebab-case lowercase, max ~80 chars
- Use primary keyword
- Avoid duplicates (check existing slugs in Supabase first)

### Title Rules
- Max 70 characters
- Primary keyword near the beginning
- Format: `{Primary Keyword}: {Secondary Context} - Formatly Blog`

### Meta Title Templates (from strategy doc)

| Type | Template |
|------|----------|
| Standard | `{Primary Keyword} - Formatly Blog` |
| Style Guide | `{Citation Style} Formatting Guide: {Key Element} - Formatly` |
| Comparison | `Formatly vs {Competitor}: The Complete {Year} Comparison - Formatly` |
| Tutorial | `How to {Action} in {Timeframe} - Formatly` |

### Excerpt Rules
- Max 160 chars, plain text (no HTML)
- Include primary keyword naturally
- Start with a verb or actionable phrase

### Internal Linking Requirements
| Link Type | Min | Target |
|-----------|-----|--------|
| Internal blog links | 2 | Relevant cluster/pillar articles |
| Product/feature pages | 1 | `/auth/register`, `/pricing`, `/features` |
| Outbound to authorities | 1–2 | `.edu`/`.org` (with `rel="nofollow"`) |
| Pillar → Cluster | All cluster articles | Bi-directional |
| Cluster → Pillar | The anchor pillar | Upward link |

### Example Word Assets

Some blog posts can include a downloadable `.docx` example document with a `.pdf` preview. The site ships **DOCX/PDF asset pairs for 7 blog guides**, plus 8 more registered entries that need file generation.

#### Asset Storage
- Source `.docx`: `public/blog-assets/{slug}/{slug}-example.docx`
- Preview `.pdf`: `public/blog-assets/{slug}/{slug}-example.pdf`

Some assets use shorter paths (e.g., `/blog-assets/apa-abstract/apa-abstract-example.docx`). Check the registry for exact paths.

#### Asset Registry
**File:** `lib/blog-example-assets.ts` (TypeScript `Record<string, BlogExampleAsset>`)

The registry has **15 entries total**. **7 have actual files**:

| Slug | Label | DOCX | PDF |
|------|-------|------|-----|
| `ultimate-apa-7th-edition-formatting-guide` | APA Full Paper Example | ✅ | ✅ |
| `mla-9th-edition-formatting-guide` | MLA Full Paper Example | ✅ | ✅ |
| `chicago-manual-of-style-a-practical-guide` | Chicago Notes Example | ✅ | ✅ |
| `harvard-referencing-a-complete-guide` | Harvard Reference List Example | ✅ | ✅ |
| `harvard-referencing-style-guide` | Harvard Style Example | ✅ | ✅ |
| `how-to-update-and-sync-table-of-contents-in-word` | Table of Contents Example | ✅ | ✅ |
| `how-to-format-a-dissertation-in-apa-style` | APA Dissertation Example | ✅ | ✅ |

**8 entries registered but need file generation:**
`how-to-format-apa-abstract-page-7th-edition`, `how-to-format-table-apa-7th-edition`, `how-to-format-an-mla-works-cited-page`, `how-to-format-an-apa-title-page`, `understanding-footnotes-and-endnotes-in-chicago-style`, `how-to-format-reference-list-apa-7th-edition`, `how-to-format-a-literature-review-apa-7th-edition`, `turabian-style-guide`

#### The Annotated DOCX Viewer (`BlogExampleAssetPanel`)

When a post has a registered asset, `BlogExampleAssetPanel` renders an interactive annotated document viewer on the blog post page:
- **Live DOCX rendering** via `docx-preview` npm package (client-side)
- **Toggle between Annotated and Raw DOCX views**
- **Overlay pins** marking key formatting rules (page numbering, title, abstract, citations, references)
- **Sidebar rule inspector** — click a pin to see the formatting rule + compliance example
- **Download button** for the source `.docx` file

**Component:** `components/blog/blog-example-asset-panel.tsx`

**Usage in blog page:**
```tsx
import { blogExampleAssets } from "@/lib/blog-example-assets"
import { BlogExampleAssetPanel } from "@/components/blog/blog-example-asset-panel"

const asset = blogExampleAssets[slug]
return (
  <article>
    <BlogPostContent content={post.content} />
    {asset && <BlogExampleAssetPanel asset={asset} />}
  </article>
)
```

#### Workflow: Writing a Post With an Example Asset

When writing a post that needs a downloadable example document:

1. **Check if a registry entry already exists** — look in `lib/blog-example-assets.ts` for the slug. If one exists but the actual `.docx`/`.pdf` files are missing, you need to **generate the files**, not re-register.
2. **For new entries:** Add to `lib/blog-example-assets.ts` with label, description, pdfUrl, docxUrl
3. **Generate missing files:** Create the `.docx` (source of truth) and `.pdf` (companion preview) in the matching `public/blog-assets/` path
4. **Reference in the post HTML:** Mention the example verbally near the relevant section — the component renders automatically

#### Creating Example Documents
- The `.docx` file is the source of truth for formatting behavior.
- The PDF is a companion preview only.
- Sample content must *teach* the formatting rule, not just mimic it.
- APA abstract samples should explain abstract structure through the example text.
- Chicago samples must use real footnotes/endnotes and a real bibliography.
- TOC samples must include a live Word TOC field left intentionally unupdated.
- Table examples should preserve visible borders so the formatting is obvious.
- For native `.docx` authoring, use the Anthropic docx skill (https://github.com/anthropics/skills/tree/main/skills/docx).

#### How to Generate Missing Asset Files

If DOCX/PDF files are registered but missing:
1. Check that you have the Anthropic docx skill or relevant tooling available
2. Use the existing python generation scripts as a starting point:
   - `scripts/generate_blog_example_templates.py`
   - `scripts/generate_sample_docs.py`
3. Create the `.docx` and `.pdf` in the `public/blog-assets/{slug}/` directory
4. Verify the files render in the `BlogExampleAssetPanel` by checking the post page

#### Purdue OWL Viewer & Scripts
- **Standalone reference:** `purdue_owl_viewer.html` (same pin annotation pattern, dev/resource reference only)
- **DOCX generation scripts:** `scripts/generate_blog_example_templates.py`, `scripts/generate_sample_docs.py`

### Registered Cross-Linking Paths (existing posts)
- `/blog/ultimate-apa-7th-edition-formatting-guide`
- `/blog/mla-9th-edition-formatting-guide`
- `/blog/chicago-manual-of-style-17th-edition-formatting-guide`
- `/blog/how-to-format-in-text-citations-apa-mla-chicago`
- `/blog/how-to-format-reference-list-apa-7th-edition`
- `/blog/how-to-create-hanging-indent-word-apa-mla-chicago`
- `/blog/5-common-apa-formatting-mistakes-and-how-to-fix-them`
- `/auth/register`
- `/pricing`
- `/features`

---

## 7. Cover SVGs

### Naming Convention
`/blog/{slug}-cover.svg`

### SVG Requirements (from PROJECT.md)
- Must have a `viewBox` attribute
- No hardcoded `width` or `height` on the outer `<svg>` tag
- Use Formatly brand colors

### Color Palette
| Element | Color |
|---------|-------|
| Background gradient | `#002a57` (dark navy) → `#001a3a` |
| CTA accent | `#006ee5` (primary blue) |
| Card backgrounds | `#ffffff` with opacity |
| Text on dark | `#ffffff` |
| Secondary text | `#94a3b8` |

### SVG Template
```svg
<svg viewBox="0 0 800 500" fill="none" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="bg" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0%" stop-color="#002a57"/>
      <stop offset="100%" stop-color="#001a3a"/>
    </linearGradient>
  </defs>
  <rect width="800" height="500" fill="url(#bg)"/>
  <!-- Category badge -->
  <rect x="40" y="40" width="100" height="26" rx="6" fill="#006ee5" opacity="0.15"/>
  <text x="90" y="57" text-anchor="middle" fill="#60a5fa" font-family="sans-serif" font-size="11" font-weight="700">{CATEGORY}</text>
  <!-- Headline -->
  <text x="40" y="110" fill="#ffffff" font-family="sans-serif" font-size="28" font-weight="800">{HEADLINE}</text>
  <!-- Checklist items -->
  {CHECKLIST_ITEMS}
  <!-- Formatly branding -->
  <text x="40" y="440" fill="#60a5fa" font-family="sans-serif" font-size="9" font-weight="700" letter-spacing="2">FORMATLYAPP.COM</text>
</svg>
```

### Article Type → Checklist Items
| Type | Items |
|------|-------|
| `standard` (style guide) | Style compliance check, Citation examples, Formatting shortcuts |
| `comparison` | Side-by-side comparison, Feature breakdown, Pricing overview |
| `listicle` / `mistakes` | Common mistakes exposed, Quick-fix solutions, Expert tips |
| `tutorial` | Step-by-step guide, Screenshots included, Pro tips inside |

### Upload to Supabase Storage
```bash
curl -X POST \
  -H "apikey: $SERVICE_ROLE_KEY" \
  -H "Authorization: Bearer $SERVICE_ROLE_KEY" \
  -H "Content-Type: image/svg+xml" \
  -d '@path/to/file.svg' \
  "https://chuxhdvazicuwdqwhtot.supabase.co/storage/v1/object/blog/covers/{filename}"
```

---

## 8. Publishing to Supabase

### Database Table: `blog_posts`

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| `slug` | TEXT (unique) | Yes | Kebab-case, unique |
| `title` | TEXT | Yes | Max 70 chars |
| `excerpt` | TEXT | Yes | Max 160 chars, plain text |
| `content` | TEXT | Yes | Full HTML content. |
| `author` | TEXT | Yes | Default: "Formatly Editorial Team" |
| `category` | TEXT | Yes | One of: "Style Guides", "Writing Tips", "Product Guides" |
| `read_time` | TEXT | Yes | Format: "X min read" |
| `cover_image` | TEXT | No | Path like `/blog/{slug}-cover.svg` |
| `published` | BOOLEAN | Yes | `true` to make visible |
| `created_at` | TIMESTAMPTZ | Auto | ISO 8601 |
| `updated_at` | TIMESTAMPTZ | Auto | ISO 8601 |

> **Note:** The app's `app/blog/[slug]/page.tsx` reads `title`, `excerpt`, `slug`, `category`, `author`, `cover_image`, `read_time`, and date columns to build `<head>` metadata and JSON-LD at render time. These go in table columns, not the `content` field.

### Insert via REST API
```bash
curl -X POST \
  -H "apikey: $SERVICE_ROLE_KEY" \
  -H "Authorization: Bearer $SERVICE_ROLE_KEY" \
  -H "Content-Type: application/json" \
  -H "Prefer: return=minimal" \
  -d '{
    "slug": "article-slug",
    "title": "Article Title",
    "excerpt": "Article excerpt under 160 chars.",
    "content": "<h2>Full HTML content here</h2>",
    "author": "Formatly Editorial Team",
    "category": "Style Guides",
    "read_time": "5 min read",
    "cover_image": "/blog/article-slug-cover.svg",
    "published": true,
    "created_at": "2026-06-30T12:00:00.000Z"
  }' \
  "https://chuxhdvazicuwdqwhtot.supabase.co/rest/v1/blog_posts"
```

### Bulk Insert via SQL
For 50+ posts, use the Supabase Management API SQL endpoint:
```bash
curl -X POST \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "INSERT INTO blog_posts (...) VALUES ... ON CONFLICT (slug) DO NOTHING"}' \
  "https://api.supabase.com/v1/projects/chuxhdvazicuwdqwhtot/database/query"
```

---

## 9. Topic Generation

### Topic Ideation Prompt
When generating new blog topics, use this prompt structure:

> Generate a single high-value, highly searchable blog post topic for Formatly (an AI-powered academic document formatting platform). It must fall naturally into one of these categories: Style Guides, Writing Tips, Product Guides.
>
> Existing topics are: [list existing slugs/titles]. Generate something fresh — never duplicate a slug.
>
> Return JSON: { "topicTitle": "...", "targetKeyword": "...", "category": "Style Guides|Writing Tips|Product Guides", "articleType": "standard|tutorial|comparison|listicle|faq", "slug": "..." }

### Topic Priority Matrix
```
                  HIGH IMPACT
                     |
   P2 (MLA)          |      P1 (APA) [DONE]
   C1 (vs PERRLA)    |      C3 (30s tutorial)
   F1 (APA FAQs)     |      C4 (Rule-based) [DONE]
                     |
 LOW EFFORT ---------|-------- HIGH EFFORT
                     |
   S1 (Back2School)  |      P3 (Chicago)
   F4 (Formatly FAQ) |      C2 (vs Scribbr)
   S2 (Thesis)       |      P4 (Harvard)
                     |
                  LOW IMPACT
```

Priority order: High impact + Low effort first → High impact + High effort → fill rest.

---

## 10. Existing Cover SVGs

Check `public/blog/` directory for existing SVGs before creating new ones. For the 50 batch-generated posts, all need cover SVGs generated (none exist yet).

---

## 11. Supabase Credentials

| Variable | Source |
|----------|--------|
| `SUPABASE_URL` | `https://chuxhdvazicuwdqwhtot.supabase.co` |
| `SERVICE_ROLE_KEY` | From Supabase Management API or `.env.format` |
| `ACCESS_TOKEN` | From `.env.format` (Supabase Management API token) |

---

## 12. Verification Checklist

After publishing a blog post, verify:

- [ ] Page renders at `formatlyapp.com/blog/{slug}`
- [ ] Title, excerpt, and meta tags are correct
- [ ] No raw JSON-LD or Schema.org blocks visible in the rendered content (means they were accidentally included in content field)
- [ ] No stray `<!-- SEO METADATA -->` or similar HTML comments visible in the rendered content
- [ ] All internal links work
- [ ] Cover SVG loads correctly
- [ ] Article appears on `/blog` listing page
- [ ] Read time and author display correctly
- [ ] No HTML rendering errors (broken tags, unescaped characters)
- [ ] Category badge matches the post
- [ ] Ends with a Formatly CTA linking to `/auth/register`
- [ ] At least 2 internal blog links + 1 internal page link