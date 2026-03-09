## The Castellan — Project Handoff

### What it is
A medievalpunk personal assistant app built as a React artifact in Claude.ai. Three distinct modules, each with their own character, aesthetic, colour palette, chat personality, data sections, and decorative UI. Storage key: `castellan10`.

---

### The Three Modules

**Sir Gary** — core PA module. Bronze/teal/aged gold palette. Gruff, loyal knight-steward character. Decorative crest at top (spinning gears, flickering torch sconces, animated wax seal, rune row, Latin knick knacks). Live stat bar (quests, vows, streak, guild, overdue). Cobweb and scroll watermarks in chat. Sections: Quests, Vows, Scrolls, Guild.

**Kaito** — Japanese learning module. Dark grey/red/gold palette. Calm, poetic ronin scholar character. Left side panel: word of the day (rotates daily), progress counters, glowing kanji buttons, recent vocab list, large ambient kanji watermark. Ink-brush dividers appear every 4 messages in chat. Sections: Vocab (kanji/reading/meaning), Phrases, Grammar.

**Silas** — hobbies module. Deep blue/arcane gold palette. Enthusiastic wizard-librarian character. Grimoire header with mana sigil animations. Left side panel: floating mana particles, "currently" status (active game/brew/book), recent catalogue cards. Sections: Brew Codex (coffee), Arcane Pursuits (games), Grand Library (books).

---

### Navigation
- **Home screen** — three floating SVG portrait cards (Gary in plate armour, Kaito as scarred ronin, Silas as tall wizard). Hover lifts the card. Click to enter.
- **⌂ HOME** — top-left of every module header, always visible.
- **CHAMBERS** — top-right of every module header, opens a centred modal. Click outside to dismiss.

---

### AI Integration
`aiAdapter(systemPrompt, messages)` — the only function to touch when swapping providers. Returns a plain string.

`callAI(userMessage, history, moduleId, store)` — builds the correct character context via `buildContext()` and calls the adapter. Each module has its own persona.

`applyActions(text, mod, store, updateModule)` — parses and strips `<CASTELLAN_ACTION>{...}</CASTELLAN_ACTION>` blocks, mutates store directly. Supported actions:
- **Gary:** `ADD_TASK`, `COMPLETE_TASK`, `MARK_HABIT`, `ADD_HABIT`
- **Silas:** `LOG_COFFEE`, `LOG_GAME`, `LOG_BOOK`
- **Kaito:** none (data entered manually)

Chat history held in React state per session. Each module greets via AI on load. API errors fall back gracefully with an inline error note.

### Planned AI: Ollama
Self-hosted on MacBook Air. Model TBD. Endpoint: `http://localhost:11434/api/chat`. Stub commented in the adapter. AI swap is the last priority.

---

### Data Structure
```
{
  profile: { name, created },
  gary:  { tasks, notes, habits, habitLog, uni },
  kaito: { vocab, phrases, grammar },
  silas: { coffee, gaming, reading }
}
```
`updateModule(moduleId, patch)` — merges patch into a module and saves.
`update(patch)` — patches top-level store (profile only).

---

### Priority Order
1. Aesthetics — further refining each module's visual identity
2. New sections — expanding data sections within modules
3. AI swap — replacing Anthropic placeholder with Ollama

---

### Key Technical Notes
- React components must be proper named function declarations — object property values cause `returnReact is not defined`
- Anything using fragments `<>` or mixing SVG children outside the main tree must use `React.createElement` directly — `GaryAmbience` is an example of this pattern
- `window.storage` is Claude.ai's artifact persistence API — replace with Supabase or local DB on self-hosted build
- 3-second safety timeout on storage load prevents hanging on the "forge awakens" screen
- `user-select:none` globally — desktop only, no mobile considerations
- Background image (parchment/raven/rune) hosted at `https://raw.githubusercontent.com/SirGaryofSillican/Castle-PA/refs/heads/main/Gemini_Generated_Image_k4nit0k4nit0k4ni.png` — wire up as local asset in Vite, the artifact sandbox blocks external images

### Self-Hosting Plan
MacBook Air, local hosting. Vite/React frontend. Supabase previously scoped for database but not confirmed.
