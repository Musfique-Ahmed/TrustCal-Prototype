# Build Prompt: TrustCal AI

Use this as the spec/prompt for an engineering build (e.g. Claude Code, Cursor, or a human dev team). It describes the product, the design decisions already made in the approved mockup (`TrustCal AI.dc.html`), and the scope for a working implementation.

## Product

Build **TrustCal AI**, an AI-powered study assistant for MBBS/medical students. Tagline: "Study with AI. Understand the source. Own your learning."

Core principle: this is NOT a generic chatbot. Every AI answer must be traceable, source-backed, and confidence-rated, and the product must actively encourage independent verification and practice over passive answer-copying (Understand → Verify → Practice → Reflect, not Ask → Copy → Leave).

## Tech stack

- React (or Next.js) + TypeScript SPA/app router.
- CSS via variables/design tokens (see Design System below) — no Tailwind required but acceptable if tokens are wired through `theme()` config.
- State: React context or Zustand for theme, active chat, resource selections, customize prefs.
- Backend (not built in the mockup, needed for real product): auth, chat/message persistence, file storage for uploaded resources, a RAG pipeline (embed uploaded resources → retrieve on each query → feed to LLM with citations), and an analytics store for AI-dependency/learning-balance metrics.

## Design system

**Palette** — CSS variables, both themes must feel like the same product (not a simple invert):

Light: `--bg:#F8F7F2; --surface:#FFFFFF; --surface-secondary:#F1EEE4; --primary:#5A3828; --primary-hover:#472C1F; --accent:#2A835F; --accent-soft:rgba(42,131,95,.12); --text-primary:#2B1D14; --text-secondary:#76513E; --border:rgba(90,56,40,.14); --success:#2A835F; --warning:#B8863B; --danger:#B4503B`

Dark: `--bg:#092328; --surface:#12544F; --surface-secondary:#0D3E3A; --primary:#2A835F; --primary-hover:#34996F; --accent:#8BBB92; --accent-soft:rgba(139,187,146,.16); --text-primary:#F6FBF7; --text-secondary:#B9D7BC; --border:rgba(139,187,146,.18); --success:#8BBB92; --warning:#E0B36B; --danger:#E3927E`

**Type**: Plus Jakarta Sans (headings + body), weights 400–800.
**Shape**: 12–20px border radius on cards/buttons/inputs, thin 1px borders, soft shadows only (`0 1px 2px rgba(0,0,0,.06)` / `0 6-10px 20-28px rgba(0,0,0,.08-.35)` per theme) — never hard drop shadows.
**Icons**: simple line icons (Lucide-style), 14–18px, stroke-based, no filled glyphs.
**Density**: spacious, generous padding (14–24px per section), strong hierarchy via weight/size, not color.

## Screens (all validated in the mockup — build to match)

1. **Chat workspace** (default view) — 3-column desktop shell: left sidebar / center chat / right intelligence panel. No dead space in the bottom-right; the right panel extends full height.
2. **Left sidebar**: logo, theme toggle, New Chat CTA, Folders (add folder), searchable Previous Chats list with timestamps and active-chat highlight, bottom-anchored Customize / Profile / Settings.
3. **Chat header**: chat title + Study Mode / Practice Mode segmented toggle (Study active by default).
4. **Study Mode body**: selected-resource chip strip, user/AI message thread, AI answers include: an "Educational use only" trust note, an AI Confidence card (%, progress bar, info tooltip with the exact disclaimer copy), a Sources Used list (each source opens a Source Preview modal: title/type/section/excerpt/"Used in this answer"), and a collapsible "How this answer was generated" 5-step stepper (Question understood → Selected resources → Retrieved evidence → AI-generated explanation → Confidence assessment). Suggested-action chips (Explain simply / Summarize / Give an example / Quiz me / Create flashcards) sit under the thread. Chat input: attach/image/web-search icons + textarea + send.
5. **Practice Mode body**: topic + Easy/Medium/Hard difficulty selector, MCQ card (A–D), Submit Answer, then Correct/Incorrect state with explanation + source refs, plus accuracy/topics-mastered/weak-areas stat row. Support MCQ, short-answer, clinical-reasoning, viva-style, and case-based question types (mockup covers MCQ; extend the same card pattern to the others).
6. **Right intelligence panel** (chat view only): Resource Selection (checkbox list + Add Resources → opens Upload modal with PDF/notes/image/URL/YouTube/textbook options), Your AI Usage Performance (circular dependency % + 7-day line chart with hover tooltips), Learning Balance (progress bars: AI-assisted learning, independent practice, source verification, plus a raw practice-questions count), Your Learning Insights (neutral, non-judgmental bullet insights).
7. **My Resources** page: searchable/filterable/sortable table (name, type, subject, uploaded date, usage count, status), upload and delete actions.
8. **Profile** page: avatar, university/year/study-level/subjects/learning-style card (editable), 5-stat dashboard (streak, questions answered, accuracy, resources uploaded, AI-assisted sessions).
9. **Settings** page: left mini-nav (Account, Appearance, Privacy, AI Preferences, Resource Management, Notifications, Data & Export) with matching right-hand panels; Data & Export includes export-history/export-usage-report/delete actions.
10. **Customize modal**: Theme (Light/Dark/System), AI response style (Concise/Balanced/Detailed), Study level (Pre-clinical/Clinical/Internship), Language (English/Bangla/Banglish), Medical terminology (Standard/Simplified), and Show sources/confidence/insights toggles.
11. **Responsive**: ≥1120px full 3-column; 760–1120px collapses the left sidebar (right panel stays); <760px hides both sidebars behind a 4-item bottom nav (Chat / Resources / Insights / Profile), Insights showing the right panel's sections stacked full-width.

## Interactions to implement for real

- Sending a chat message appends it and streams/returns a real AI answer with live-computed confidence and cited sources (real RAG, not canned text).
- Source click → preview panel pulls the actual excerpt/page from the underlying resource file.
- Resource checkboxes actually scope what the RAG pipeline is allowed to retrieve from for that chat.
- Upload modal performs real uploads (PDF/image/notes parsing + embedding pipeline; YouTube/URL ingestion).
- AI dependency and Learning Balance metrics are computed from real usage logs (messages sent vs. practice attempts vs. source-preview opens), not static numbers.
- Practice Mode grades answers against a real question bank per topic/difficulty and updates mastery/weak-area tracking.
- Theme, response style, study level, language, and terminology preferences persist per user and actually change system-prompt behavior server-side.

## Non-goals / guardrails

- Never present AI confidence as clinical certainty — always pair confidence UI with the disclaimer copy from the mockup.
- Every substantive medical answer must carry visible source attribution; never allow a sourceless answer to render as though it were established fact.
- Keep the "educational use only" note present but visually quiet — it should never dominate the response.
