# Ant Design Interview Q&A

The following prompt-style questions and answers provide context for Ant Design contributors discussing project direction, architecture, and practices during interviews or onboarding conversations.

## Product Direction

- **Question:** How does Ant Design balance keeping a coherent design language with the need to introduce new components or redesign existing ones based on user feedback?  
- **Answer:** We start with Ant Design’s design language as the primary constraint and feed product feedback through the design team before backlog grooming. For new ideas we prototype in Figma, validate against the token system, and only build when design reviews confirm there is no visual debt. Component redesigns run through regression demos, language parity checks, and changelog alignment so that coherence remains the default while still letting feedback drive improvements.

## Architecture

- **Question:** Can you walk through the rationale behind the current build pipeline (webpack, ES/CJS/UMD outputs) and where tree-shaking concerns typically surface?  
- **Answer:** Webpack remains because it handles our legacy UMD output, custom CSS-in-JS transforms, and doc tooling efficiently. We emit ES modules for modern bundlers, CJS for Node-based build chains, and UMD for legacy CDN usage. Tree-shaking issues usually stem from deep component re-exports; we mitigate them by limiting default exports, leaning on `babel-plugin-import`, and tracking bundle size in CI.

## TypeScript Strategy

- **Question:** What guardrails are in place to maintain strict typing across such a large codebase, and how do you enforce them during code reviews?  
- **Answer:** We compile in strict mode, lint against `any`, and fail CI on type regressions. During review we require interfaces for public props, scoped generics for reusable hooks, and updates to shared typings (like locale) whenever APIs change. Reviewers also cross-check demos and tests to ensure inferred types stay ergonomic for consumers.

## Component Evolution

- **Question:** How do you evaluate backwards compatibility when adjusting a component’s API, especially given the rule to favor `open` over `visible` and the broader naming guidelines?  
- **Answer:** Any API change starts with a compatibility matrix covering React 16–19 and SSR. When we rename a prop we provide deprecation warnings, document dual support, and stage removal through the changelog. Naming rules (`open`, `dataSource`, etc.) are enforced through lint rules and reviewer checklists so consumers can rely on stable conventions.

## Styling System

- **Question:** Why was `@ant-design/cssinjs` chosen over other CSS-in-JS solutions, and what patterns do you use to control runtime cost while supporting tokens, themes, dark mode, and RTL?  
- **Answer:** It gives us token-aware style generation, server-side rendering support, and deterministic class names. To keep runtime lean we batch style registration, memoize token expansions, and precompute theme variants. RTL and dark mode rely on logical properties and token overrides rather than branching styles, which cuts duplication.

## Token Management

- **Question:** How do you coordinate global tokens versus component tokens (`buttonPrimaryColor`, etc.) to keep visual consistency without stifling component-level customization?  
- **Answer:** We define global primitives (colors, spacing, typography) and extend them through component tokens layered in a single source file per component. Any new component token requires design sign-off and doc updates. Global tokens feed defaults while component tokens handle contextual tweaks, and ConfigProvider props expose the right override surface for consumers.

## Testing Expectations

- **Question:** With a 100% coverage target, what kinds of tests are considered must-have before merging a feature?  
- **Answer:** Every component change needs unit/interaction tests with React Testing Library, plus snapshots for stable markup. We also add regression cases for hooks or edge scenarios raised in issues. Coverage thresholds run in CI, so PRs must cover new branches and avoid bypassing assertions.

## Internationalization

- **Question:** When adding a new localized string, what process ensures every locale file is updated and stays aligned with the shared `useLocale` typings?  
- **Answer:** We add typings in `components/locale/index.tsx` first, update every language file in the same commit, and run lint rules that flag missing keys. Reviews double-check English and Chinese docs for matching anchors, and ConfigProvider demos get run locally to confirm merged locales.

## Accessibility

- **Question:** What checks or manual testing does the team run to keep WCAG 2.1 AA compliance, especially for keyboard focus, reduced motion, and high-contrast scenarios?  
- **Answer:** Automated linting plus Storybook axe scans catch most issues. We manually tab through interactive demos, confirm focus outlines with tokens, and test `prefers-reduced-motion` fallbacks. High-contrast checks rely on design tokens, and we document any exceptions that need design review.

## Release Cadence

- **Question:** How do you decide which changes land in `master` versus the `feature` branch, and what criteria trigger updates to both English and Chinese changelog entries?  
- **Answer:** Bug fixes and incremental improvements ship via `master`, while larger features and breaking work target `feature` or `next` depending on scope. Any user-facing change mandates synchronized entries in `CHANGELOG.en-US.md` and `CHANGELOG.zh-CN.md` with the appropriate emoji tag, linked issue/PR, and version annotation before merge.

