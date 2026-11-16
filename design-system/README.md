# Design system

The reusable part of the prototype, pulled out so it can be carried into a real codebase or into Figma.

- `tokens.css` — the design tokens as CSS custom properties, two themes. `index.html` links this file; it is the source of truth.
- `tokens.json` — the same tokens in the W3C Design Tokens format, with the CSS name of each token under `$extensions.css`. Feed it to Style Dictionary, Tokens Studio or a Figma variables import.
- `components.md` — component contracts: markup, states, tokens, rules. The CSS in `index.html` is the reference implementation.
- `data-model.md` — what a session records, how it is stored, what the exports contain.

Conventions carried through everything:

- Colour has roles, not names: `own`, `target` and `ai` are the three actors on the plot; `good` and `bad` are feedback only; `accent` is the one action colour.
- States are attributes (`aria-pressed`, `aria-expanded`, `disabled`, `hidden`, `data-*`), not modifier classes, so a component's accessibility and its look cannot drift apart.
- One breakpoint (880px) and one height breakpoint (820px); all motion respects `prefers-reduced-motion`.

If the prototype is ever split into a framework project, the natural layers are: `shared/tokens` (this folder), `shared/ui` (the primitives in `components.md`), `entities/session` (`data-model.md`), `features/decide`, `features/check-data`, `widgets/radar-plot`, `widgets/advisory`, `widgets/dashboard-*`, `pages/start`, `pages/study`, `pages/dashboard`.
