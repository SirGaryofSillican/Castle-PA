## The Castellan — Project Handoff

### What it is
A medievalpunk personal assistant app built as a React artifact in Claude.ai. Three distinct modules, each with their own character, aesthetic, colour palette, chat personality, and data sections. The overall tone is Dark Souls × Skyrim × The Witcher 3.

---

### The Three Modules

**The Castellan** — the core PA module. Bronze/teal/aged gold palette. Stern, laconic medieval herald character. Sections: Quests (tasks), Vows (habits with streaks), Scrolls (notes), Guild (university obligations). Background image (parchment/raven/rune illustration) is hosted at `https://raw.githubusercontent.com/SirGaryofSillican/Castle-PA/refs/heads/main/Gemini_Generated_Image_k4nit0k4nit0k4ni.png` but cannot load in the Claude.ai artifact sandbox — wire it up as a local asset when moving to Vite.

**Kaito the Ronin Scholar** — Japanese language learning module. Dark grey/red/gold palette. Calm, poetic, disciplined teacher character. Large kanji watermarks as background texture. Sections: Vocabulary (kanji + reading + meaning), Phrases, Grammar. Vertical ink-wash layout aesthetic.

**Silas the Arcane Curator** — hobbies module. Deep blue/arcane gold palette. Enthusiastic wizard-librarian character. Mana-line grid background texture. Sections: Brew Codex (coffee), Arcane Pursuits (games), Grand Library (books). Grimoire aesthetic with glowing blue accents.

---

### Navigation
- **Home screen** — three SVG character portrait cards. Click to enter a module. Portraits are inline SVG: Castellan in iron plate armour, Kaito as a scarred ronin with katana, Silas as a tall wizard with staff and grimoire.
- **⌂ HOME** button — top-left of every module header, always visible, returns to home screen.
- **⚙ CHAMBERS** button — top-right of every module header, opens a centred modal overlay with that module's data sections. Click outside the modal to dismiss.

---

### AI Integration
Single adapter pattern — `aiAdapter(systemPrompt, messages)` is the only function to touch when swapping providers. It receives a system prompt and message history, returns a plain string.

`callAI(userMessage, history, moduleId, store)` builds the correct character context for whichever module is active and passes it to the adapter. Each module has its own system prompt persona in `buildContext()`.

The AI can emit `<CASTELLAN_ACTION>{...}</CASTELLAN_ACTION>` blocks to mutate store data. `applyActions()` parses and strips them before display. Supported actions per module:

- **Castellan:** `ADD_TASK`, `COMPLETE_TASK`, `MARK_HABIT`, `ADD_HABIT`
- **Silas:** `LOG_COFFEE`, `LOG_GAME`, `LOG_BOOK`
- **Kaito:** no actions (data entered manually via chambers)

Chat history is held in React state per module session. Each module gets a fresh greeting from the AI on load. If the API is unreachable, a fallback message is shown and an error note appears in the header.

### Planned AI: Ollama
Self-hosted on a MacBook Air. Model TBD. Local endpoint will be `http://localhost:11434/api/chat`. A stub for this is commented into the adapter. AI swap is the last priority — aesthetics and new sections come first.

---

### Data & Storage
Storage key: `castellan9` via `window.storage` (Claude.ai's artifact persistence API). Data is structured as:
```
{
  profile: { name, created },
  castellan: { tasks, notes, habits, habitLog, uni },
  kaito: { vocab, phrases, grammar, progress },
  silas: { coffee, gaming, reading }
}
```
`updateModule(moduleId, patch)` merges a patch into a specific module's data and saves. `update(patch)` patches the top-level store (used for profile).

In a self-hosted build `window.storage` would be replaced with Supabase or a local database.

---

### Priority Order
1. Aesthetics — refining each module's visual identity further
2. New sections — expanding data sections within modules
3. AI swap — replacing the Anthropic placeholder with Ollama

---

### Key Technical Notes
- React components must be proper named function declarations — object property values cause `returnReact is not defined` in the Claude.ai JSX transpiler
- The `Background` component and any SVG rendered outside the main tree uses `React.createElement` directly rather than JSX — keep it that way
- External images cannot load in the Claude.ai artifact sandbox regardless of host — defer all image assets to the Vite migration
- Storage has a 3-second safety timeout so a hanging storage call never blocks the UI
- `user-select:none` is set globally — this is a desktop-only app, no mobile/iOS considerations
- Fonts are Georgia as a serif fallback. Cinzel Decorative and UnifrakturMaguntia are upgrade targets for the Castellan module when on Vite

### Self-Hosting Plan
MacBook Air, local hosting. Vite/React frontend. Supabase previously scoped for the database layer but not confirmed.
