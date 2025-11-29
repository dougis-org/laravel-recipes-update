# Laravel Recipe Manager - AI Coding Agent Instructions

## Project Overview
**Legacy Modernization Project**: Updating a Laravel 5.2 recipe management application to the latest Laravel version with modern patterns and best practices. The application displays recipes and cookbooks with many-to-many relationships through pivot tables. Core entities: **Recipes**, **Cookbooks**, **Classifications**, **Courses**, **Meals**, and **Preparations**.

**Modernization Goals**:
- Upgrade from Laravel 5.2 → latest Laravel version (11+)
- Replace deprecated patterns (Eloquence trait → native Laravel search)
- Update routing conventions (modularized Routes → modern Route groups)
- Modernize model relationships and conventions
- Adopt current Blade templating and form handling
- Implement modern testing patterns (Pest/PHPUnit latest)
- Remove outdated dependencies; use current alternatives

## Architecture & Data Model

### Entity Relationships
- **Recipe** → belongs to one **Classification** & one **Source**
- **Recipe** ↔ **Meal** (many-to-many via `recipe_meals`)
- **Recipe** ↔ **Preparation** (many-to-many via `recipe_preparations`)
- **Recipe** ↔ **Course** (many-to-many via `recipe_courses`)
- **Cookbook** ↔ **Recipe** (many-to-many via `cookbook_recipes`, ordered by classification then name)

**Key Pattern**: All many-to-many relationships use explicit pivot tables in `database/migrations/`. Model relationships defined in `app/Models/` using `belongsToMany()` and `hasOne()`.

### Models & Searchability
- Models in `app/Models/` use `Eloquence` trait (e.g., `Recipe.php`) for fluent searching on `['name', 'ingredients']`
- All models have timestamps enabled and fillable attributes defined
- Example: `$recipe->search('pasta')->get()` leverages `Eloquence` trait

## Route & Controller Structure

### Routing Pattern
Routes are **modularized** in `app/Http/Routes/` (not in single file):
- `recipe.php`: `Route::resource('recipe', 'RecipeController', ['only' => ['index', 'show']])`
- `cookbook.php`, `contact.php` similarly defined
- Main router loads all partials via `foreach (File::AllFiles(__DIR__ . '/Routes'))`
- Default route: `/` → redirects to recipe index with sorting params: `sortField=date_added`, `sortOrder=desc`, `displayCount=30`

### Key Controllers
- **RecipeController**: Handles listing (supports `sortField`, `sortOrder`, `displayCount` query params), search functionality
- **CookbookController**: Similar structure
- Controllers receive form requests from `app/Http/Requests/`

## Development Workflow

### Setup & Running
```bash
# Install dependencies (uses modern composer packages)
composer install
npm install

# Run migrations (database/migrations/)
php artisan migrate

# Seed database (database/seeds/DatabaseSeeder.php)
php artisan db:seed

# Start development server
php artisan serve

# Clear cache
php artisan optimize:clear
```

### Modernization Notes
- **Namespace Updates**: Move from `App\Models\` PSR-4 to modern structure as needed
- **Blade Updates**: Update legacy Blade syntax to modern conventions
- **Dependency Replacement**: Sofa/Eloquence (search) → Laravel Scout or native query methods
- **Route Files**: Consolidate modularized routes into Route groups with modern conventions
- **Testing**: Update ExampleTest.php to use modern testing syntax (Pest 2.0+ or PHPUnit 10+)

### Testing
- Test runner: `phpunit` (configured in `phpunit.xml`)
- Test directory: `tests/`
- Example test in `tests/ExampleTest.php` uses `TestCase` with functional testing traits
- Environment during tests: `APP_ENV=testing`, drivers use array/sync (no persistence)

### Build & Assets
- CSS/JS built via Gulp (`gulpfile.js` + Laravel Elixir)
- Bootstrap Sass included as dependency
- Compiled assets: `public/css/`, `public/js/`

## Code Conventions

### Model Relationships
Use method names for relationships, not properties:
```php
// Recipe.php
public function getSource() { return $this->hasOne('App\Models\Source', 'id', 'source_id'); }
public function getClassification() { return $this->hasOne(...); }
```
Call as: `$recipe->getSource()` (not `$recipe->source`)

### Validation & Requests
Form validation in `app/Http/Requests/` classes (PSR-4 namespace `App\Http\Requests\`)

### Views & Templating
- Blade templates in `resources/views/` with subdirectories per resource: `recipe/`, `cookbook/`, `contact/`, `emails/`
- Partial views in `partials/`, templates in `templates/`
- Main layout likely in `templates/` or `partials/`

### Database Migrations
- All migrations in `database/migrations/` with consistent timestamp prefix `2015_12_21_*`
- Foreign key migrations separate: `add_foreign_keys_to_*_table.php` (e.g., for cookbook_recipes, recipe_courses)

## Integration Points

### External Dependencies
- **Guzzle** (`guzzlehttp/guzzle ~6.0`): HTTP client for external API calls
- **LaravelCollective HTML** (`laravelcollective/html`): Form & HTML helpers
- **Sofa Eloquence** (`sofa/eloquence ~5.1`): Search trait on models
- **Faker, Mockery, PHPSpec**: Testing utilities

### Environment Configuration
- Config in `config/` (app.php, database.php, mail.php, etc.)
- Migrations run in order by timestamp
- No custom service providers visible; uses default Laravel providers

## Project-Specific Conventions

1. **Sorting/Filtering**: Controllers accept query parameters (`sortField`, `sortOrder`, `displayCount`) - preserve this pattern when adding features
2. **Pivot Table Ordering**: Cookbook recipes ordered by classification name then recipe name (see `Cookbook::recipes()`)
3. **Timestamps**: All models include `public $timestamps = true`
4. **Guarded Properties**: All models have `protected $guarded = []` (opposite of fillable)
5. **Route Cache**: `/reset-cache` endpoint calls `optimize:clear` for development debugging

## Debugging & Troubleshooting

- Enable debug mode: set `APP_DEBUG=true` in `.env`
- Check logs: `storage/logs/`
- Test database uses array driver (no persistence between tests)
- Middleware stack in `app/Http/Kernel.php` includes CSRF verification, session handling
