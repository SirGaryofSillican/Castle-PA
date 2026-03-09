

---

## The Castellan — Project Handoff

### What it is
A medievalpunk personal assistant app built as a React artifact in Claude.ai. The aesthetic target is Dark Souls × Skyrim × The Witcher 3 — aged bronze/verdigris palette, Elder Futhark runes as UI labels, spinning gear animations, illuminated manuscript gold accents, parchment feel. Fonts are Georgia (serif fallback, works without import) with Cinzel Decorative and UnifrakturMaguntia as upgrade targets.

### Current state
The app is fully functional with persistent storage via `window.storage` (Claude.ai's artifact storage API, keyed `"castellan8"`). It has eleven sections accessible via a scrollable tab bar at the bottom: Quests (tasks), Vows (habits with streaks), Scrolls (notes), Brew (coffee), Feast (cooking), Games, Bard (music), Train (fitness), Coffers (finance), Tomes (reading), Guild (university).

The chat interface calls an AI via a clean adapter pattern. The Anthropic API is currently wired in as a placeholder, but the architecture is designed for easy swapping.

### AI integration
The adapter is in a function called `aiAdapter(systemPrompt, messages)` — this is the only function that needs to change to swap providers. It receives a structured system prompt (built fresh each message from live store data) and a full conversation history array in `{ role, content }` format. It must return a plain string. The AI can optionally emit `<CASTELLAN_ACTION>{...}</CASTELLAN_ACTION>` blocks to mutate store data — supported actions are `ADD_TASK`, `COMPLETE_TASK`, `ADD_HABIT`, `MARK_HABIT`, `LOG_COFFEE`, `LOG_FITNESS`.

### Planned AI: Ollama
To be self-hosted on a MacBook Air. Model TBD. The Ollama swap is the last priority — aesthetics and new sections come first. The local endpoint will be `http://localhost:11434/api/chat`. A stub for this is already commented into the adapter.

### Priority order
1. Aesthetics — continuing to refine the medievalpunk feel
2. New sections — adding content areas beyond the current eleven
3. AI swap — replacing the Anthropic placeholder with Ollama

### Background image
A parchment/rune illustration (raven centrepiece, circuit board runic ring, binary outer ring) has been chosen and is hosted at `https://raw.githubusercontent.com/SirGaryofSillican/Castle-PA/refs/heads/main/Gemini_Generated_Image_k4nit0k4nit0k4ni.png`. The Claude.ai artifact sandbox blocks external image requests so it cannot be loaded in the artifact. The `Background` component already has the `<img>` tag and URL in place — it will work once the project moves to Vite and the image is served as a local asset instead. The vignette/tinting layers are already correctly set up around it.

### Key technical notes
- React components must be proper named function declarations, not object property values — the JSX transpiler in Claude.ai artifacts will throw `returnReact is not defined` otherwise
- `window.storage` is Claude.ai's persistent storage API — not localStorage. In a self-hosted build this would be replaced with Supabase or similar
- The Background component uses `React.createElement` directly rather than JSX because it's rendered outside the main JSX tree — keep it that way
- All store mutations go through the `update(patch)` function which merges the patch and saves to storage in one call
- Conversation history is held in React state and passed to the adapter on every message — the AI has no memory between sessions

### Self-hosting plan
MacBook Air, local hosting. Stack not fully decided yet but Vite/React frontend is the likely direction. Supabase was previously scoped for the database layer but is not confirmed.

---

