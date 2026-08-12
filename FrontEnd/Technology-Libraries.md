# FrontEnd Technology & Libraries

Library → purpose → capability mappings verified from `FrontEnd/package.json` and actual `src/` usage. Packages that are installed but unused or template-only are called out separately.

## Core platform

| Library | Purpose | Capability |
|---------|---------|------------|
| React / React DOM | UI runtime | SPA components (React 19) |
| TypeScript | Static typing | Strict TS app with `@/*` path alias |
| Vite (+ `@vitejs/plugin-react`) | Dev server & production build | HMR, ESM bundling (`tsc && vite build`) |
| react-router-dom | Client routing | Nested routes, lazy pages, redirects |

## State & async workflows

| Library | Purpose | Capability |
|---------|---------|------------|
| Redux Toolkit + react-redux | Global store | Auth + Layout slices via `configureStore` |
| redux-saga | Side effects | Auth saga (login, logout, 2FA, signup/password flows) |

Note: classic reducers are used; `createSlice` / `createAsyncThunk` are not the primary pattern here.

## API / session

| Library | Purpose | Capability |
|---------|---------|------------|
| Native `fetch` + NSwag Fetch clients | HTTP to the API | Generated `WebApiClient.ts` + `authenticatedFetch` |
| jwt-decode | Access-token expiry | Client-side JWT `exp` checks in session helpers |
| moment | Date/time | NSwag `dateTimeType: MomentJS` + Orbit date formatting |

NSwag generation config: `FrontEnd/src/helpers/api/GenerateWebApiClient.nswag` (Fetch template → `WebApiClient.ts`).

## UI, styling, forms

| Library | Purpose | Capability |
|---------|---------|------------|
| Tailwind CSS (+ FrostUI / forms / typography plugins) | Utility styling | Primary app styling system |
| Sass | SCSS pipeline | Theme/structure/plugin SCSS under `assets/scss` |
| @headlessui/react | Accessible primitives | Modal/offcanvas/popover wrappers |
| react-toastify | Notifications | Global toast portal |
| react-hook-form + yup + resolvers | Forms/validation | Auth and selected Orbit forms |
| ag-grid-community / ag-grid-react | Data grids | Primary Orbit tables via shared DataGrid components |
| dompurify | HTML sanitization | Safe HTML rendering helper |

## Product integrations (verified usage)

| Library | Purpose | Capability |
|---------|---------|------------|
| @microsoft/signalr | Realtime | Notification hub hook/UI |
| @jitsi/react-sdk | Meetings | Start-meeting UI |
| @stripe/stripe-js / @stripe/react-stripe-js | Payments | Subscription checkout flows |
| i18next / react-i18next | Localization | Locale switching and Orbit translations |
| @fullcalendar/* | Calendar | Appointment calendar views |
| @react-oauth/google / react-facebook-login | Social login UI | Auth social login forms |

## Quality tooling

| Library | Purpose | Capability |
|---------|---------|------------|
| Vitest + Testing Library + jsdom | Unit tests | Configured in Vite; small existing test set |
| ESLint / Prettier | Lint/format | `lint` / `format` scripts |

## Installed but unused or template-limited (verified)

| Package | Notes |
|---------|--------|
| axios / axios-mock-adapter | Present in package.json; **no `src/` imports** — HTTP uses fetch/NSwag |
| i18next-http-backend | Not the active load path for app i18n |
| gridjs / gridjs-react | Template table demos; Orbit grids use AG Grid |
| dayjs | Essentially unused for app dates (moment dominates) |
| apexcharts / chart.js / swiper / plyr / jsvectormap / rsuite (most usage) | Primarily Attex template demo pages, not core Orbit admin flows |

## Evidence

- `FrontEnd/package.json`
- `FrontEnd/vite.config.ts`
- `FrontEnd/tsconfig.json`
- `FrontEnd/src/helpers/api/httpClient.ts`
- `FrontEnd/src/helpers/api/apiClients.ts`
- `FrontEnd/src/helpers/api/GenerateWebApiClient.nswag`
- `FrontEnd/src/redux/store.ts`
- `FrontEnd/src/components/DataGrid/`
- `FrontEnd/src/hooks/useSignalR.ts`
