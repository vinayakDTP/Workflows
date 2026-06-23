---
name: ai-seo
description: Audit and optimize web projects for Google's generative AI search features (AI Overviews, AI Mode). Use when building websites, reviewing frontend code, writing content, or when someone asks about SEO, AI search optimization, AEO, GEO, or ranking in AI Overviews. Enforces Google's official guidelines as guardrails during development.
---

# /ai-seo — AI Search Optimization Guardrails

Enforce Google's official AI optimization guidelines when building, reviewing, or auditing web projects. Based on [Google's Optimization Guide for Generative AI Features](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide) (updated June 2026).

## Core principle

**AEO and GEO are still SEO.** Google's generative AI features (AI Overviews, AI Mode) are rooted in core Search ranking and quality systems. There is no separate "AI algorithm" to game. The same index and quality signals that earn rankings are what earn citations in AI answers.

---

## When to apply this skill

- Building a new website or landing page
- Reviewing frontend HTML/CSS/JS for SEO issues
- Writing or editing page content
- Adding structured data / schema markup
- Configuring `robots.txt` or crawling rules
- Setting up e-commerce product feeds or local business profiles
- Preparing a site for agentic AI experiences
- Auditing an existing site for AI search readiness

---

## Guardrails: What to ALWAYS do

### 1. Content quality (highest impact)

Creating content that people find unique, compelling, and useful will influence your website's presence in generative AI search more than any other factor.

- **Write for humans first.** Content must be well-written, easy to follow, and organized with paragraphs, sections, and headings that provide clear structure.
- **Demonstrate E-E-A-T.** Every page should signal Experience, Expertise, Authoritativeness, and Trustworthiness:
  - Include author credentials and bios where relevant.
  - Cite authoritative sources (government sites, peer-reviewed studies, primary data).
  - Show first-hand experience with the subject matter.
  - Link to primary data sources, not content aggregators.
- **Be specific, not generic.** Commodity content (vague, opinion-only, undifferentiated) is increasingly invisible in AI search. Provide unique data, original analysis, or practical details no one else has.
- **Open with a direct answer.** Lead pages with a concise 40-70 word summary answering the core query, name the primary entity, and offer one actionable step. Follow with scannable H2/H3 sections.
- **Keep content fresh.** AI features preferentially cite current content. Regularly update existing pages with new data, dates, and context.

### 2. Images and video

AI search features surface relevant images and video alongside text — more visibility opportunities beyond web links.

- Support textual content with high-quality, relevant images and videos.
- Follow Google's image SEO best practices: descriptive filenames, alt text, proper sizing, served in modern formats (WebP/AVIF).
- Follow Google's video SEO documentation: provide transcripts, use VideoObject schema, host on the page with the content.
- Use visuals that address specific reader needs: diagrams to simplify, tables to compare, screenshots to validate claims.
- Never use images as decoration only — each visual should earn its place by clarifying the content.

### 3. Technical foundation (crawling and indexing)

Your content must be crawlable and indexable before any AI optimization matters.

- **Ensure all important pages are crawlable** — not blocked by `robots.txt`, not behind authentication walls.
- **Allow Googlebot** in `robots.txt`. Do not accidentally block it.
- **Confirm HTTP 200 status codes** on all important pages.
- **Minimize client-side rendering** that hides primary text content. Google needs to see your content without executing complex JS.
- **Set clear canonical URLs** for duplicate content.
- **Maintain XML sitemaps** for all important pages.
- **Pass Core Web Vitals:** LCP, INP, CLS. Google aggregates these into a composite performance score — failing one hurts more than before (March 2026 Core Update).
- **Mobile-first responsive design** is required, not optional.
- **Fast page speed** — latency kills both user experience and crawl efficiency.
- For large/frequently updated sites, optimize your crawl budget.

### 4. Semantic HTML

- Use semantic HTML elements (`<article>`, `<section>`, `<nav>`, `<header>`, `<footer>`, `<main>`, `<aside>`, `<figure>`, `<figcaption>`, `<time>`, `<address>`) where they naturally fit.
- Focus on human readability — don't over-engineer for "AI-perfect" markup. Google says: "Focus on human readability and don't worry about perfect code."
- Semantic HTML helps screen readers, browser agents, and AI agents parse your content. One investment, three audiences.
- Use heading hierarchy correctly: one `<h1>` per page, logical `<h2>`/`<h3>` nesting.
- Use `<ul>`/`<ol>` for lists, `<table>` for tabular data, `<blockquote>` for quotes.

### 5. Structured data

- **There is no special "AI schema."** No hidden markup unlocks AI Overview inclusion.
- Continue implementing standard schema.org types for rich results: `Article`, `FAQPage`, `HowTo`, `Product`, `BreadcrumbList`, `Organization`, `Person`, `VideoObject`, `LocalBusiness`.
- Use JSON-LD format (Google's preferred format).
- Structured data must match visible page content — never add markup for content that isn't on the page.
- Validate with Google's Rich Results Test.
- Use `Organization` and `Person` schema with `sameAs` pointing to canonical profiles (Wikidata, Crunchbase, LinkedIn).
- Structured data is helpful for search understanding and rich results, but content quality beneath the schema is the determining factor for AI citations.

### 6. Local business optimization

- **Google Business Profile** hygiene directly affects AI visibility.
- Complete every relevant field: categories matching primary service, detailed service names/descriptions, hours (including holiday/special), accurate address and phone (consistent with website), photos (exterior, interior, team, product).
- Encourage honest reviews from real customers.
- Keep NAP (Name, Address, Phone) consistent across the site and all profiles.

### 7. Shopping and e-commerce

- Use **Merchant Center feeds** with accurate product information: titles, descriptions, pricing, availability, images, categories.
- Feed quality directly affects how products surface across Google experiences including AI responses.
- Consider **Business Agent** — Google's conversational experience letting customers chat with your brand on Search.
- Implement `Product` schema with complete attributes: price, availability, reviews, images.
- Ensure transaction flows work cleanly and pricing is machine-parseable.

### 8. AI agent readiness (forward-looking)

AI agents are autonomous systems performing tasks on behalf of users (booking reservations, comparing products). Browser agents access websites by:
- Analyzing visual renderings (screenshots)
- Inspecting the DOM structure
- Interpreting the accessibility tree

To prepare:
- Ensure clean, well-structured DOM.
- Maintain strong accessibility practices (ARIA labels, logical tab order, screen-reader-friendly markup).
- Keep interactive elements (forms, buttons, links) clearly labeled and machine-parseable.
- Follow web.dev's agent-friendly website best practices.
- Watch for **Universal Commerce Protocol (UCP)** adoption — an emerging protocol co-developed by Google and Shopify enabling search agents to complete transactions.
- For non-Google AI crawlers, be deliberate about `robots.txt` rules for `GPTBot`, `OAI-SearchBot`, `PerplexityBot`, `ClaudeBot`, `anthropic-ai`.

---

## Guardrails: What to NEVER do (myths debunked by Google)

### 1. Do NOT create `llms.txt` files for Google Search

Google Search does not use `llms.txt` files. Creating them won't help or hurt your Google rankings. If you maintain them for other services (ChatGPT, Perplexity, Claude), that's fine — but don't expect them to affect Google.

### 2. Do NOT "chunk" content for AI

There is no requirement to break content into tiny pieces. Google's systems understand nuance across multiple topics on a page and extract the relevant piece. Write naturally organized content, not artificially fragmented snippets.

### 3. Do NOT rewrite content in an "AI-friendly style"

Do not stuff content with canonical formulas, explicit redundancies, forced synonyms, or robotic phrasing to "help AI understand." Google's AI systems understand synonyms and semantic meaning. Writing in a stilted "AI-optimized" voice hurts human readability for no benefit.

### 4. Do NOT pursue "AEO/GEO hacks"

Ignore tactics marketed as special AI optimization tricks:
- Inauthentic brand mentions or citation seeding
- Paying for "AI placement"
- Creating special AI-targeted pages separate from your main content
- Keyword-stuffing disguised as "entity optimization"
- Any tactic that treats AI search as a separate system from regular search

### 5. Do NOT create separate machine-readable files for Google

You don't need to create new machine-readable files, AI text files, special markup, or Markdown versions of your pages to appear in Google's AI features. Google already reads your HTML.

### 6. Do NOT block Googlebot expecting to "opt out" of AI features only

Blocking Googlebot removes you from ALL of Google Search, not just AI features. The `Google-Extended` user agent controls training usage, not search appearance.

---

## Audit checklist

When reviewing a web project, check these in order:

```
TECHNICAL FOUNDATION
[ ] robots.txt allows Googlebot on all important pages
[ ] No accidental noindex on key pages
[ ] XML sitemap exists and is up to date
[ ] All important pages return HTTP 200
[ ] Canonical URLs are set correctly
[ ] Core Web Vitals pass (LCP < 2.5s, INP < 200ms, CLS < 0.1)
[ ] Mobile-responsive design
[ ] Primary content is not hidden behind client-side JS
[ ] HTTPS enabled

CONTENT QUALITY
[ ] Pages have unique, non-commodity content
[ ] Content opens with a direct answer / summary (40-70 words)
[ ] Clear heading hierarchy (H1 > H2 > H3)
[ ] Author credentials / E-E-A-T signals present
[ ] Content is current (dates, data, references up to date)
[ ] No generic SaaS/marketing filler language
[ ] Authoritative sources cited with links to primary data

MEDIA
[ ] Images have descriptive alt text
[ ] Images use modern formats (WebP/AVIF) with proper sizing
[ ] Videos have transcripts and VideoObject schema
[ ] Visuals serve a purpose (clarify, compare, validate)

STRUCTURED DATA
[ ] JSON-LD schema matches visible page content
[ ] Validated in Rich Results Test
[ ] Appropriate types used (Article, Product, FAQ, etc.)
[ ] Organization/Person schema with sameAs links
[ ] No "ghost" markup for content not on the page

LOCAL (if applicable)
[ ] Google Business Profile complete and accurate
[ ] NAP consistent across site and profiles
[ ] Service categories and descriptions filled
[ ] Hours and photos current
[ ] Reviews present and responded to

E-COMMERCE (if applicable)
[ ] Merchant Center feed active with complete product data
[ ] Product schema with price, availability, reviews
[ ] Transaction flows work cleanly
[ ] Pricing is machine-parseable

AGENT READINESS
[ ] Clean, well-structured DOM
[ ] ARIA labels on interactive elements
[ ] Logical tab order
[ ] Forms and buttons clearly labeled
[ ] robots.txt reviewed for non-Google AI crawlers
```

---

## How to invoke

```
/ai-seo                    — Full audit of current project
/ai-seo review             — Review current changes for AI SEO issues
/ai-seo content <file>     — Audit a specific page's content quality
/ai-seo technical          — Technical crawling/indexing audit only
/ai-seo schema             — Validate structured data implementation
/ai-seo checklist          — Print the full audit checklist
```

---

## Sources

This skill is based on official Google documentation:

- [Google's Guide to Optimizing for Generative AI Features](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide) (June 2026)
- [AI Features and Your Website](https://developers.google.com/search/docs/appearance/ai-features)
- [Top Ways to Succeed in AI Search](https://developers.google.com/search/blog/2025/05/succeeding-in-ai-search) (May 2025)
- [New Resource for Optimizing for Generative AI](https://developers.google.com/search/blog/2026/05/a-new-resource-for-optimizing) (May 2026)
- [Google Search Documentation Updates](https://developers.google.com/search/updates)
