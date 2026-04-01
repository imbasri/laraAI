# Laravel + React Starter Kit

A modern full-stack application starter kit built with **Laravel 13**, **React 19**, **Inertia.js 3**, **Tailwind CSS 4**, and **shadcn/ui**.

This is a clean, production-ready foundation for building web applications — not a boilerplate with demo features. Start building your app on top of this solid base.

---

## Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| **Backend** | PHP + Laravel | 8.4 / 13.x |
| **Frontend** | React + TypeScript | 19.x / 5.x |
| **Bridge** | Inertia.js | 3.0+ |
| **Styling** | Tailwind CSS + shadcn/ui | 4.x / new-york |
| **Build** | Vite | 6.x |
| **Auth** | Laravel Fortify | 1.x |
| **Testing** | Pest (PHP) | 4.x |
| **AI Agents** | BMad + OpenCode | - |
| **Linting** | ESLint 9 + Prettier | flat config |

---

## Prerequisites

- **PHP 8.4** with extensions: `mbstring`, `xml`, `curl`, `sqlite3` (or your preferred database)
- **Composer** 2.x
- **Node.js 20+** and **npm** (or pnpm/yarn/bun)
- **Git**
- **Laravel Herd** (recommended for local development)

---

## Quick Start

### 1. Install Dependencies

```bash
# Install PHP dependencies
composer install

# Install Node dependencies
npm install
```

### 2. Environment Setup

```bash
# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate

# Create SQLite database (default)
touch database/database.sqlite
# Or use MySQL/PostgreSQL — update DB_* in .env
```

### 3. Run Migrations

```bash
php artisan migrate
```

### 4. Start Development Server

```bash
# Option A: Run everything in one command (recommended)
composer run dev

# Option B: Run services separately
# Terminal 1 — Laravel server
php artisan serve

# Terminal 2 — Vite dev server (HMR)
npm run dev

# Terminal 3 — Queue worker (if needed)
php artisan queue:listen
```

Open **http://localhost:8000** in your browser (or use Laravel Herd at **https://larapos.test**).

---

## AI-Assisted Development

This project is configured for AI-assisted development using **OpenCode** and **BMad** agents.

### OpenCode

OpenCode is the primary AI agent for this project. It provides:
- Context-aware code generation
- Laravel-specific knowledge via Laravel Boost MCP
- Integrated development workflow

```bash
# Start OpenCode session
opencode
```

### BMad Agents

BMad provides specialized AI agents for different development tasks:

| Agent | Purpose |
|-------|---------|
| **Barry (Quick Dev)** | Rapid implementation of features and fixes |
| **Amelia (Dev Story)** | Story-based feature implementation |
| **John (PM)** | Product requirements and planning |
| **Winston (Architect)** | Technical architecture decisions |
| **Sally (UX Designer)** | UI/UX design and specifications |
| **Quinn (QA)** | Test automation and quality assurance |
| **Bob (Scrum Master)** | Sprint planning and task management |
| **Paige (Tech Writer)** | Documentation and technical writing |

```bash
# Invoke a specific agent
/opencode bmad:quick-dev
/opencode bmad:dev-story
```

### MCP Tools

The project includes Laravel Boost MCP server for:
- Database schema inspection
- Read-only queries
- Application info retrieval
- Browser error logging
- URL generation

---

## Project Structure

```
├── app/
│   ├── Actions/          # Laravel Actions (business logic)
│   ├── Concerns/         # PHP Traits
│   ├── Http/
│   │   ├── Controllers/  # Inertia controllers
│   │   └── Middleware/   # Request middleware
│   ├── Models/           # Eloquent models
│   └── Providers/        # Service providers
├── config/               # Laravel configuration
├── database/
│   ├── migrations/       # Database migrations
│   ├── factories/        # Model factories for testing
│   └── seeders/          # Database seeders
├── resources/
│   ├── css/
│   │   └── app.css       # Tailwind CSS + theme variables
│   └── js/
│       ├── actions/      # Auto-generated (Wayfinder forms) — DO NOT EDIT
│       ├── components/   # React components
│       │   └── ui/       # shadcn/ui components — auto-generated, DO NOT EDIT
│       ├── hooks/        # Custom React hooks
│       ├── layouts/      # Inertia layout wrappers
│       ├── lib/          # Utilities (cn(), etc.)
│       ├── pages/        # Inertia page components (your views)
│       │   ├── auth/     # → AuthLayout
│       │   ├── settings/ # → AppLayout + SettingsLayout
│       │   └── ...       # → AppLayout (default)
│       ├── routes/       # Auto-generated (Wayfinder routes) — DO NOT EDIT
│       ├── types/        # TypeScript type definitions
│       └── wayfinder/    # Auto-generated (Wayfinder routes) — DO NOT EDIT
├── routes/
│   ├── web.php           # Main web routes
│   ├── settings.php      # Settings routes
│   └── console.php       # Artisan commands
├── tests/
│   ├── Feature/          # Integration tests (Pest)
│   └── Unit/             # Unit tests (Pest)
├── components.json       # shadcn/ui configuration
├── eslint.config.js      # ESLint flat config
├── pint.json             # Laravel Pint config
├── tsconfig.json         # TypeScript configuration
└── vite.config.ts        # Vite + plugins configuration
```

---

## Building Your Application

### Adding a New Page

1. **Create a route** in `routes/web.php`:

```php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::inertia('products', 'products/index')->name('products.index');
    Route::inertia('products/create', 'products/create')->name('products.create');
});
```

2. **Create the React page** at `resources/js/pages/products/index.tsx`:

```tsx
import { Head, Link } from '@inertiajs/react';
import AppLayout from '@/layouts/app-layout';
import { products } from '@/routes';

export default function ProductIndex() {
    return (
        <>
            <Head title="Products" />
            <div className="p-6">
                <div className="mb-4 flex items-center justify-between">
                    <h1 className="text-2xl font-bold">Products</h1>
                    <Link
                        href={products.create()}
                        className="rounded bg-blue-600 px-4 py-2 text-white"
                    >
                        Add Product
                    </Link>
                </div>
                {/* Your content here */}
            </div>
        </>
    );
}

ProductIndex.layout = {
    breadcrumbs: [{ title: 'Products', href: products.index() }],
};
```

3. **Create a controller** (optional, for complex logic):

```php
// app/Http/Controllers/ProductController.php
namespace App\Http\Controllers;

use App\Models\Product;
use Inertia\Inertia;

class ProductController extends Controller
{
    public function index()
    {
        return Inertia::render('products/index', [
            'products' => Product::all(),
        ]);
    }
}
```

Then use it in your route:

```php
Route::get('products', [ProductController::class, 'index'])->name('products.index');
```

### Adding a New Model + Migration

```bash
# Generate model + migration + factory + seeder
php artisan make:model Product -mfs
```

Edit the migration in `database/migrations/XXXX_create_products_table.php`:

```php
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->text('description')->nullable();
    $table->decimal('price', 10, 2);
    $table->integer('stock')->default(0);
    $table->timestamps();
});
```

Run migrations:

```bash
php artisan migrate
```

### Adding Forms with Wayfinder

Wayfinder auto-generates type-safe form helpers. Just define your Laravel form request:

```bash
php artisan make:request StoreProductRequest
```

In your controller:

```php
public function store(StoreProductRequest $request)
{
    Product::create($request->validated());
    return redirect()->route('products.index');
}
```

Wayfinder will generate TypeScript types for the form. Use it in React:

```tsx
import { useForm } from '@inertiajs/react';
import { products } from '@/routes';

const { data, setData, post, processing, errors } = useForm({
    name: '',
    description: '',
    price: '',
    stock: '0',
});

const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    post(products.store());
};
```

### Adding shadcn/ui Components

```bash
# Add a component from shadcn/ui
npx shadcn@latest add button
npx shadcn@latest add table
npx shadcn@latest add dialog
```

Components are added to `resources/js/components/ui/`.

### Using the Database

```bash
# Run migrations
php artisan migrate

# Seed the database
php artisan db:seed

# Fresh migration (drops all tables)
php artisan migrate:fresh --seed

# Open Tinker (REPL)
php artisan tinker
```

---

## Development Commands

| Command | Description |
|---------|-------------|
| `composer run dev` | Start Laravel + Vite + Queue (all in one) |
| `npm run dev` | Start Vite dev server (HMR) |
| `npm run build` | Build for production |
| `npm run lint` | Run ESLint + auto-fix |
| `npm run lint:check` | Run ESLint (no auto-fix) |
| `npm run format` | Run Prettier + auto-fix |
| `npm run format:check` | Run Prettier (no auto-fix) |
| `npm run types:check` | TypeScript type checking |
| `composer run lint` | Run Laravel Pint (PHP formatter) |
| `composer run test` | Run full test suite |
| `php artisan test` | Run Pest tests |

---

## Testing

### PHP Tests (Pest)

```bash
# Run all tests
php artisan test

# Run specific test file
php artisan test tests/Feature/ExampleTest.php

# Run with coverage (requires Xdebug)
php artisan test --coverage
```

Example test:

```php
// tests/Feature/ProductTest.php
test('can view products page', function () {
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get(route('products.index'));

    $response->assertOk();
    $response->assertInertia(fn ($page) => $page->has('products'));
});
```

---

## Code Style Rules

### TypeScript/JavaScript
- **Type imports separate**: `import type { Foo }` on its own line
- **Import order**: builtin → external → internal → parent → sibling → index (alphabetical)
- **Curly braces required** on all control statements
- **Blank lines** before/after control flow statements
- **No file extensions** in imports (`.ts`/`.tsx` not needed)

### PHP
- **Laravel Pint** for formatting: `composer run lint`
- **PSR-12** coding standards
- **Typed properties** and **constructor promotion** (PHP 8.3+)

### CSS
- **Tailwind CSS v4** — config in `app.css`, not `tailwind.config.js`
- Use `cn()` utility for conditional class merging
- **shadcn/ui** components — do not edit files in `components/ui/` manually

---

## Deployment

### Production Build

```bash
# Install production dependencies
composer install --optimize-autoloader --no-dev

# Build frontend assets
npm run build

# Run migrations
php artisan migrate --force

# Cache configuration
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

### Environment Variables for Production

Set these in your `.env` or hosting platform:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

DB_CONNECTION=mysql
DB_HOST=your-db-host
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_user
DB_PASSWORD=your_password

# Queue (recommended for production)
QUEUE_CONNECTION=database
```

### Recommended Hosting

- **Laravel Forge** + DigitalOcean/Linode/AWS
- **Laravel Vapor** (serverless)
- **Railway** / **Render** / **Fly.io**
- **Traditional VPS** with Nginx + PHP-FPM

---

## Troubleshooting

### Using Laravel Herd

This project uses Laravel Herd for local development. Herd provides:
- Automatic HTTPS configuration
- Built-in PHP server management
- SSL certificate handling

```bash
# List your sites
herd sites

# Start a service (e.g., MySQL, PostgreSQL)
herd services:start mysql

# Check PHP versions
herd php:list
```

Access the application at **https://larapos.test**

### Vite HMR not working
```bash
# Clear Vite cache
rm -rf node_modules/.vite
npm run dev
```

### TypeScript errors
```bash
# Check types
npm run types:check

# Regenerate wayfinder types
php artisan wayfinder:generate
```

### Database issues
```bash
# Fresh migration (WARNING: drops all data)
php artisan migrate:fresh

# Check migration status
php artisan migrate:status
```

### ESLint/Prettier conflicts
```bash
# Fix all formatting
npm run lint && npm run format
```

### OpenCode / Laravel Boost MCP Issues

If you encounter issues with OpenCode or Laravel Boost MCP:

```bash
# Verify MCP server is running
php artisan boost:mcp --help

# Check Laravel Boost is installed
composer show laravel/boost

# Regenerate MCP configuration
php artisan boost:update
```

### BMad Agents Not Working

```bash
# Verify OpenCode configuration
cat opencode.json

# Check skills are installed
ls -la .opencode/skills/
```

---

## Resources

### Laravel & PHP
- [Laravel Documentation](https://laravel.com/docs)
- [Laravel Boost](https://laravel.com/docs/boost)
- [Pest PHP](https://pestphp.com/)
- [Laravel Wayfinder](https://github.com/laravel/wayfinder)

### Frontend
- [Inertia.js Documentation](https://inertiajs.com/)
- [React Documentation](https://react.dev/)
- [Tailwind CSS v4](https://tailwindcss.com/docs)
- [shadcn/ui](https://ui.shadcn.com/)

### AI Development
- [OpenCode Documentation](https://opencode.ai)
- [BMad Agents](https://github.com/anomalyco/opencode-agents)

### Deployment
- [Laravel Forge](https://forge.laravel.com/)
- [Laravel Vapor](https://vapor.laravel.com/)
- [Railway](https://railway.app/)
- [Render](https://render.com/)

---

## License

MIT
