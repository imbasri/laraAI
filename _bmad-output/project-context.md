---
project_name: 'larapos'
user_name: 'AWK'
date: '2026-04-01'
sections_completed: ['technology_stack', 'language_rules', 'framework_rules', 'testing_rules', 'quality_rules', 'workflow_rules', 'anti_patterns']
status: 'complete'
existing_patterns_found: 50
---

# Project Context for AI Agents

_This file contains critical rules and patterns that AI agents must follow when implementing code in this project. Focus on unobvious details that agents might otherwise miss._

---

## Technology Stack & Versions

### Core Backend
- **PHP** 8.3+
- **Laravel** 13.x
- **Laravel Fortify** 1.34+ (authentication backend)
- **Laravel Wayfinder** 0.1.14 (type-safe route/form generation)
- **Inertia.js Laravel** 3.0+ (SSR bridge)

### Core Frontend
- **React** 19.2.x
- **TypeScript** 5.7.x
- **Inertia.js React** 3.0+ (SPA bridge)
- **Vite** 8.0.x (bundler + dev server)
- **Tailwind CSS** 4.0.x (utility-first CSS)

### UI Component Libraries
- **shadcn/ui** — new-york style, CSS variables, neutral baseColor
- **Radix UI** 1.1.x — all primitives (dialog, dropdown-menu, select, tooltip, etc.)
- **Headless UI** 2.2.x — React primitives
- **Lucide React** 0.475.x — icon library
- **class-variance-authority** 0.7.x — component variants
- **input-otp** 1.4.x — OTP input component

### Build & Quality Tools
- **ESLint** 9.17+ (flat config)
- **Prettier** 3.4+ (with tailwindcss plugin)
- **TypeScript ESLint** 8.23+
- **Pest** 4.4+ (PHP testing)
- **Laravel Pint** 1.27+ (PHP formatter)
- **React Compiler** 1.0+ (babel plugin)

### Path Aliases
- `@/*` → `./resources/js/*`

## Critical Implementation Rules

### Language-Specific Rules (TypeScript/JavaScript)

**TypeScript Configuration:**
- `strict: true`, `noImplicitAny: true` — type checking ketat
- `isolatedModules: true` — setiap file harus self-contained untuk transpile
- `moduleResolution: bundler` — **tidak perlu ekstensi `.ts`/`.tsx`** di import paths
- `jsx: react-jsx` — tidak perlu `import React`
- `@/*` alias → `./resources/js/*` (bukan root project)

**Import/Export Patterns:**
- **Type imports WAJIB terpisah** — `import type { Foo }` di baris sendiri, tidak boleh digabung dengan value imports
- **Import order:** builtin → external → internal → parent → sibling → index
- **Alphabetical** dalam setiap grup (case-insensitive)
- **Top-level type specifier** — `import type` selalu di atas regular imports

**React-Specific:**
- **React Compiler aktif** — tidak perlu manual `useMemo`/`useCallback` untuk performance (kecuali untuk referential equality)
- **React 19** — gunakan API yang kompatibel dengan React 19
- **Strict mode aktif** — semua komponen harus compatible dengan React strict mode

**Code Style:**
- **Curly braces wajib** di semua control statements (`if`, `for`, `while`, `try`, dll) meskipun 1 baris
- **1TBS brace style** — buka brace di baris yang sama dengan statement
- **Blank line** sebelum dan sesudah control flow statements
- Prettier handle formatting, ESLint handle logic rules — `npm run lint` untuk fix ESLint, `npm run format` untuk Prettier

**Critical Notes:**
- `@typescript-eslint/no-explicit-any` **dimatikan** — `any` eksplisit diperbolehkan, tapi `any` implisit akan error dari TypeScript
- **Jangan edit** file di `components/ui/`, `actions/`, `wayfinder/`, `routes/` — ini auto-generated
- **Laravel Wayfinder** — selalu pakai generated routes, bukan hardcode URL string
- **Inertia Layout** — page di `auth/` → AuthLayout, `settings/` → [AppLayout, SettingsLayout], lainnya → AppLayout

### Framework-Specific Rules

**Laravel Backend:**
- **PHP 8.3+** — gunakan constructor promotion, readonly properties, typed properties
- **Laravel 13** — ikuti konvensi terbaru
- **Laravel Fortify** — authentication backend, jangan bypass
- **Pest** — testing framework (bukan PHPUnit tradisional)
- **Laravel Pint** — PHP formatter, konfigurasi di `pint.json`

**Inertia.js Patterns:**
- Controllers return `Inertia::render('page-name', $props)` — bukan view/JSON response
- Page components di `resources/js/pages/` — namespace menentukan layout otomatis
- Props dari controller otomatis jadi TypeScript types di React page
- Shared data via middleware, bukan prop drilling
- Form handling pakai `useForm()` dari `@inertiajs/react` — bukan state manual
- Navigasi pakai `<Link>` component atau `router.visit()/get()/post()` — bukan `<a>` tags
- Error handling via `useForm().errors` — tidak perlu manual error state

**shadcn/ui + Tailwind v4:**
- Komponen UI di `resources/js/components/ui/` — **auto-generated**, jangan edit manual
- Gunakan `cn()` utility dari `@/lib/utils` untuk conditional class merging
- `cva()` dari `class-variance-authority` untuk component variants
- Tailwind v4: konfigurasi di CSS (`app.css`), bukan `tailwind.config.js`
- CSS variables untuk theming — `--background`, `--foreground`, dll
- Icon library: **Lucide React** — import dari `lucide-react`

**Laravel Wayfinder:**
- Type-safe route helper dari generated files di `resources/js/wayfinder/`
- Form variants aktif (`formVariants: true`)
- **Selalu** pakai Wayfinder routes, bukan string URL hardcoded
- Generated files di-ignore ESLint — jangan edit manual

### Testing Rules

**Pest PHP (Backend):**
- Test files di `tests/Feature/` (integration) dan `tests/Unit/` (unit)
- Syntax: `test('description', function () { ... })` atau `it('does something', function () { ... })`
- **Bukan** PHPUnit class-based syntax
- `RefreshDatabase` trait otomatis untuk Feature tests (via `Pest.php`)
- SQLite in-memory untuk testing (`DB_DATABASE=:memory:` di `phpunit.xml`)
- Factory pattern: `User::factory()->create()`
- HTTP testing: `$this->actingAs($user)->get(route('...'))->assertOk()`
- Inertia assertions: `->assertInertia(fn ($page) => $page->has('data'))`

**Testing Conventions:**
- Nama test deskriptif: `it('can_create_a_new_product', ...)` bukan `test_create()`
- Satu test file per class/feature
- Mock external services, test real business logic
- Bcrypt rounds = 4 di testing (lebih cepat)
- Semua mailer/queue/cache = `array` driver di testing

**Frontend Testing:**
- Belum ada testing framework frontend (belum ada Vitest/Jest/Cypress)
- Jika ditambahkan: test files di `resources/js/__tests__/` atau `*.test.tsx`

### Code Quality & Style Rules

**PHP (Laravel Pint):**
- Preset: `laravel` (di `pint.json`)
- PSR-12 coding standards
- PHP 8.3+ features: constructor promotion, readonly properties, typed properties, attributes
- Attribute-based fillable: `#[Fillable(['name', 'email'])]` bukan `$fillable` array
- Attribute-based hidden: `#[Hidden(['password'])]` bukan `$hidden` array
- Factory type annotation: `/** @use HasFactory<UserFactory> */`

**TypeScript/JavaScript (ESLint + Prettier):**
- Prettier: `singleQuote: true`, `tabWidth: 4`, `printWidth: 80`, `semi: true`
- Prettier plugin: `prettier-plugin-tailwindcss` — auto-sort Tailwind classes
- ESLint ignores: `vendor/`, `node_modules/`, `public/`, `components/ui/*`, `actions/**`, `wayfinder/**`, `routes/**`
- `curly: ['error', 'all']` — wajib curly braces di semua control statements
- `@stylistic/brace-style: ['error', '1tbs']` — buka brace di baris yang sama
- `padding-line-between-statements` — blank line sebelum/sesudah control flow

**CSS (Tailwind v4):**
- Konfigurasi di `resources/css/app.css`, bukan `tailwind.config.js`
- CSS variables untuk theming: `--background`, `--foreground`, `--primary`, dll
- `cn()` utility dari `@/lib/utils` — wajib untuk conditional class merging
- `clsx` + `tailwind-merge` — handle class conflicts otomatis

**Naming Conventions:**
- PHP: PascalCase class, camelCase method, snake_case database columns
- TypeScript: PascalCase component/interface, camelCase function/variable, kebab-case file
- React components: file name = component name (PascalCase)
- Inertia pages: kebab-case route name → PascalCase component

### Development Workflow Rules

**Git & Repository:**
- Branch naming: `feature/description`, `fix/description`, `chore/description`
- Commit messages: imperative mood — "add feature" bukan "added feature"
- Jangan commit `.env` — gunakan `.env.example` sebagai template
- Jangan commit `node_modules/` atau `vendor/`

**Development Commands:**
- `composer run dev` — start semua services (Laravel + Vite + Queue)
- `npm run lint` — ESLint auto-fix
- `npm run format` — Prettier auto-fix
- `npm run types:check` — TypeScript type checking
- `composer run lint` — Laravel Pint auto-fix
- `composer run test` — full test suite

**Wayfinder:**
- Generate type-safe routes: `php artisan wayfinder:generate`
- Jalankan setelah menambah/mengubah route di `routes/web.php`
- Generated files: `resources/js/wayfinder/`, `resources/js/routes/`, `resources/js/actions/`

**Database:**
- Default: SQLite (`database/database.sqlite`)
- Migration naming: `YYYY_MM_DD_HHMMSS_description.php`
- Selalu jalankan `php artisan migrate:fresh` di development jika perlu reset
- Factory + seeder untuk test data

### Critical Don't-Miss Rules

**Anti-Patterns (JANGAN lakukan ini):**
- ❌ **Jangan hardcode URL string** — selalu pakai Wayfinder routes (`products.index()`, bukan `'/products'`)
- ❌ **Jangan edit auto-generated files** — `components/ui/*`, `actions/*`, `wayfinder/*`, `routes/*`
- ❌ **Jangan pakai `<a>` tags** untuk navigasi — pakai `<Link>` dari `@inertiajs/react`
- ❌ **Jangan return JSON dari controller** yang seharusnya Inertia page — pakai `Inertia::render()`
- ❌ **Jangan pakai `tailwind.config.js`** — Tailwind v4 konfigurasi di CSS
- ❌ **Jangan gabung type + value imports** — pisahkan `import type` dari `import`
- ❌ **Jangan pakai `useMemo`/`useCallback`** untuk performance — React Compiler handle otomatis
- ❌ **Jangan bypass Fortify** untuk authentication — gunakan Fortify controllers/middleware
- ❌ **Jangan commit `.env`** — selalu pakai `.env.example` sebagai template

**Edge Cases:**
- Inertia layout switching berdasarkan nama page — pastikan namespace benar (`auth/`, `settings/`)
- `moduleResolution: bundler` — tidak perlu ekstensi `.ts`/`.tsx` di import paths
- `isolatedModules: true` — tidak boleh `const enum` atau cross-file type inference
- `noImplicitAny: true` tapi `no-explicit-any: off` — `any` implisit error, `any` eksplisit OK
- Form submission di Inertia — selalu pakai `useForm()`, bukan state manual + axios/fetch

**Security:**
- `#[Hidden]` attribute di Model untuk sensitive fields (password, two_factor_secret, dll)
- `#[Fillable]` attribute untuk mass assignment protection
- Password cast: `'password' => 'hashed'` di model `$casts`
- Rate limiting di routes: `->middleware('throttle:6,1')` untuk sensitive endpoints
- Fortify 2FA support aktif — jangan disable tanpa alasan

**Performance:**
- React Compiler handle memoization — jangan over-optimize dengan `useMemo`/`useCallback`
- Lazy load komponen besar dengan `React.lazy()` jika perlu
- Inertia shared data via middleware — hindari prop drilling
- Queue worker untuk background jobs (email, notification, dll)

---

## Usage Guidelines

**Untuk AI Agents:**
- Baca file ini sebelum implementasi kode apapun
- Ikuti SEMUA aturan yang terdokumentasi
- Jika ragu, pilih opsi yang lebih restriktif
- Update file ini jika pola baru ditemukan

**Untuk Manusia:**
- Jaga file ini tetap lean dan fokus pada kebutuhan agent
- Update saat technology stack berubah
- Review berkala untuk aturan yang sudah usang
- Hapus aturan yang sudah menjadi obvious seiring waktu

Last Updated: 2026-04-01
