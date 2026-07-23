# Blog Content Formatting Guide

> HTML structure and readability rules for all Formatly blog post content stored in Supabase.

---

## 1. HTML Structure

### 1.1 Document Shell

Blog content is stored as raw HTML in the `blog_posts.content` column. Every post must follow this top-level structure:

```html
<h2>Engaging hook addressing a pain point (one paragraph)</h2>

<p>Follow-up context paragraph. Acknowledge the struggle before presenting the solution.</p>

<h2>First Major Section</h2>
<p>Body content with short paragraphs (2-4 sentences max).</p>

<h3>Optional Subsection</h3>
<p>Subsection content.</p>

<h2>Second Major Section</h2>
...
```

### 1.2 Heading Rules

| Element | Rule |
|---------|------|
| `<h2>` | First element in the content. No `<h1>` — the page title handles that. |
| `<h2>` | Major sections only. Use for each main topic. |
| `<h3>` | Subsections within an `<h2>` block. Max one level deep. |
| Heading IDs | Do NOT add `id` attributes manually. The app injects them automatically via `extractHeadingsAndInjectIds()`. |
| Heading text | Keep under 12 words. Make descriptive, not clever. |
| Heading styling | Bold, font size, spacing, and borders are applied by the code prose system. Do NOT add classes or inline styles to heading tags. |

### 1.3 Styling System

All blog content is rendered inside a **`.blog-content`** scoped container (defined in `globals.css`). This class applies dedicated styles for every HTML element — headings, paragraphs, links, lists, tables, blockquotes, code, and pre — using high-specificity selectors (e.g., `.blog-content h2`, `.blog-content a`). These styles **cannot be overridden** by global CSS.

Links inside the blog content use the `class="text-[#006ee5] font-medium underline hover:text-[#0056b8]"` format (set directly in the HTML stored in Supabase).

### 1.4 Prohibited Elements

Do NOT include in the content:

| Element | Why |
|---------|-----|
| `<h1>` | Conflicts with the page-level title. |
| `<style>` or inline `style=""` | Breaks the `.blog-content` design system. |
| `<script>` or JSON-LD | Handled by the page component. Raw JSON-LD renders as visible text. |
| `<!-- SEO METADATA -->` | The page component handles SEO. This block renders as visible text. |
| `class=""` on non-link elements | `.blog-content` handles styling. Inline classes can conflict. |
| Empty tags (`<p></p>`, `<div></div>`) | Creates unwanted spacing. |

---

## 2. Typography & Readability

### 2.1 Paragraphs

```html
<p>Keep every paragraph between 2 and 4 sentences. Never exceed 5 sentences or roughly 100 words. Short paragraphs improve scanability on mobile and desktop.</p>
```

- One idea per paragraph.
- No paragraph wall of text longer than 5 lines when rendered.

### 2.2 Character Escapes

All posts use HTML entities for smart punctuation:

| Character | Entity | Notes |
|-----------|--------|-------|
| Em dash | `&mdash;` | Use for breaks in thought (spaces around it). |
| En dash | `&ndash;` | Number ranges (e.g., 45&ndash;67). |
| Left/right quotes | `&ldquo;` `&rdquo;` | All quotation marks in prose. |
| Left/right single | `&lsquo;` `&rsquo;` | All apostrophes and single quotes. |
| Ampersand | `&amp;` | In body text; use `&amp;` in HTML. |
| Ellipsis | `&hellip;` | Trailing off (three dots). |
| Degree | `&deg;` | Measurements (e.g., 0.5&Prime;). |
| Inches | `&Prime;` | Indent depth (e.g., 0.5&Prime;). |

**Do NOT use** straight quotes (`"` `'`), double hyphens (`--`), or literal ampersands (`&`).

### 2.3 Bold & Emphasis

```html
<strong>Key terms and important concepts</strong> — use <strong> for semantic importance.
<em>Titles of works and emphasis</em> — use <em> for italics.
```

- `<strong>` for key terms, rules, warnings
- `<em>` for titles, introduced terms, mild emphasis
- Do NOT use `<b>` or `<i>` (presentational, not semantic)

---

## 3. Links

### 3.1 Link Format

```html
<a href="/blog/related-post-slug">descriptive link text</a>
```

Links MUST include the class attribute for proper styling:

```html
<a href="/blog/related-post" class="text-[#006ee5] font-medium underline hover:text-[#0056b8]">descriptive text</a>
```

### 3.2 Link Rules

| Rule | Details |
|------|---------|
| Text | Descriptive. Never "click here" or "read more." |
| Internal blog | Use relative path: `/blog/slug` |
| Internal pages | Use relative path: `/pricing`, `/features`, `/auth/register` |
| External | Full URL: `https://example.com` + `rel="nofollow"` |
| Official style guides | External `.org` / `.edu` links OK, use `rel="nofollow"` |
| Minimum per post | 2 internal blog links, 1 internal page link |

### 3.3 Registered Internal Links for Cross-Linking

These paths are safe to use in any post:

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

### 3.4 Example Word Asset Links

Some blog posts include a matching educational example document. When a post needs one:

- Create the primary example as a `.docx` file.
- Store the editable source and the public preview together under the blog assets path.
- Link the blog post to the asset bundle using a short label, a one-sentence description, and both file types.

Recommended asset structure:

```text
public/blog-assets/{slug}/{slug}-example.docx
public/blog-assets/{slug}/{slug}-example.pdf
```

Recommended blog post reference pattern:

```html
<p>If you want to see a finished example, open the sample document below and download the editable Word file when you are ready to reuse the structure.</p>
```

If the post references a Word example, the article should make clear that:

- The `.docx` file is the source of truth for formatting behavior.
- The PDF is only a companion preview, not a replacement for the Word file.
- The sample content must teach the format rule, not just mimic it.

Examples of educational sample content:

- APA abstract posts should explain abstract structure through the sample text.
- Chicago posts should use real footnotes or endnotes and a real bibliography.
- Table of contents posts should include a live Word TOC field that is intentionally left unupdated.
- Table examples should preserve visible borders so the formatting is obvious.

When updating or generating these Word examples, use the Anthropic docx skill as the reference workflow:

- [Anthropic `docx` skill](https://github.com/anthropics/skills/tree/main/skills/docx)
- Use it for native `.docx` authoring, Word field fidelity, and render-based validation.

---

## 4. Lists

### 4.1 Bullet Lists

```html
<ul>
  <li><strong>Term:</strong> Description of the item.</li>
  <li><strong>Another term:</strong> Another description.</li>
</ul>
```

- Use for feature sets, comparisons, grouped items.
- Max 7 items per list.
- Lead with `<strong>` term when each item has a label.

### 4.2 Numbered Lists

```html
<ol>
  <li><strong>First step.</strong> Brief instruction for step one.</li>
  <li><strong>Second step.</strong> Brief instruction for step two.</li>
</ol>
```

- Use for sequential steps, ranked items, processes.
- Each item: bold lead-in + brief instruction.
- Max 10 steps.

---

## 5. Tables

### 5.1 Table HTML

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
    <tr>
      <td class="px-4 py-2 font-semibold">Label 2</td>
      <td class="px-4 py-2">Value or description.</td>
    </tr>
  </tbody>
</table>
```

### 5.2 Table Rules

- First column: always `font-semibold` (label column).
- Headers: `text-xs font-semibold uppercase`.
- Use for comparison data, reference values, quick-reference summaries.
- Keep under 10 rows. Wide tables can overflow on mobile.

---

## 6. Callout Boxes

### 6.1 Expert Tip / Note

```html
<div style="background: #f0f7ff; border-left: 4px solid #006ee5; padding: 16px 20px; margin: 24px 0; border-radius: 0 8px 8px 0;">
  <p style="margin: 0;"><strong>Expert Tip:</strong> Text of the tip goes here.</p>
</div>
```

Use sparingly — 1-2 per post max. Never nest inside a table or list.

---

## 7. Section Order

Every post follows this order:

```
1.  <h2> Hook (pain-point question or scenario)
      → 1 paragraph
2.  <h2> Context / Definition
      → 2-3 paragraphs explaining the topic
3.  <h2> Major sections (2-5)
      → Each includes: paragraphs, optional lists or tables
      → Add <h3> subsections only when needed
4.  <h2> CTA Section (Formatly pitch)
      → 1 paragraph acknowledging the pain
      → 1 paragraph presenting Formatly as solution
      → 1 link to /auth/register
```

### 7.1 CTA Link

Every post must end with a CTA link to `/auth/register`. The text should be benefit-driven:

```html
<p>Ready to eliminate formatting headaches? <a href="/auth/register" class="text-[#006ee5] font-medium underline hover:text-[#0056b8]">Upload your document and format it in seconds</a>.</p>
```

---

## 8. What NOT to Include

| Item | Reason |
|------|--------|
| `<style>` tags | Inline CSS breaks the prose system. |
| `style="..."` on elements | Use classes or prose utilities instead. |
| `<script type="application/ld+json">` | Handled by the page component. Raw JSON-LD in content renders as visible text. |
| `{ "@context": "https://schema.org", ... }` | Same — will show as raw text at the top of the article. |
| `<!-- SEO METADATA -->` | Same — this block renders as visible text. |
| Base64 images | Bloats content. Use cover images via Supabase. |
| Emoji | Unless explicitly approved for the post topic. |
| Horizontal rules (`<hr>`) | Not part of the prose design. Use heading breaks instead. |

---

## 9. Readability Checklist

Before adding content to the database, verify:

- [ ] First element is `<h2>` (not `<h1>` or text)
- [ ] No JSON-LD or raw JSON blocks anywhere
- [ ] No `<!-- SEO METADATA -->` comments
- [ ] All links use `class="text-[#006ee5] font-medium underline hover:text-[#0056b8]"`
- [ ] No inline `style=""` attributes except callout boxes
- [ ] All smart quotes use HTML entities (`&ldquo;`, `&rsquo;`, `&mdash;`)
- [ ] No `<script>`, `<style>`, or `<hr>` tags
- [ ] At least 2 internal blog links + 1 internal page link
- [ ] Ends with a Formatly CTA linking to `/auth/register`
- [ ] Paragraphs are 2-4 sentences (under 100 words each)
- [ ] No empty or self-closing non-void elements

---

*Maintainer: Content Team — Update this document when the prose design system changes.*
