# Laravel 5.2 → Laravel 12 Fresh Build Plan

## Overview
Build a new Laravel 12 Recipe Manager application from scratch using the existing Laravel 5.2 app as functional requirements and acceptance criteria. Implement modern backend patterns (Laravel 12, Scout, Eloquent), modern frontend patterns (Tailwind CSS v4, Alpine.js v3), and contemporary best practices.

## Strategy
Rather than upgrading in-place, create a clean Laravel 12 application with the same feature set and database schema, using the existing app as a specification document. This approach yields:
- Zero technical debt
- Modern Laravel conventions throughout
- Contemporary frontend user experience
- Cleaner, more maintainable codebase
- Preserved database schema and migrations

---

## Phase 1: Project Setup

1. Create new Laravel 12 project: `laravel new recipe-manager`
2. Configure database connection in `.env` (MySQL/PostgreSQL)
3. Set PHP version requirement to 8.5+ in `composer.json`
4. Initialize git repository and establish version control
5. Create project structure with `app/Models/`, `app/Http/Controllers/`, `resources/views/`

**Version Requirements** (Latest Supported):
- PHP 8.5+ (latest stable)
- Laravel 12.x
- Node.js 25+
- Tailwind CSS 4+

---

## Phase 2: Frontend Stack Configuration

**Modern Frontend Stack**:
- **CSS Framework**: Tailwind CSS 4+ (latest, with improved performance and features)
- **JavaScript Framework**: Alpine.js 3.x (latest, lightweight, Blade-friendly)
- **Build Tool**: Vite (modern, 10x faster than Webpack)
- **UI Components**: Blade components with Tailwind styling

6. Install Tailwind CSS 4+: `npm install -D tailwindcss@latest postcss autoprefixer`
7. Create `tailwind.config.js` with modern configuration
8. Create `postcss.config.js` for Tailwind processing
9. Install Alpine.js 3.x via npm: `npm install alpinejs@latest`
10. Configure Vite in `vite.config.js` with Laravel plugin
11. Update `package.json` with build scripts: `npm run dev` and `npm run build`
12. Add Tailwind CSS to `resources/css/app.css`
13. Create Alpine.js entry point in `resources/js/app.js`
14. Update Blade layout to use `@vite(['resources/css/app.css', 'resources/js/app.js'])`

---

## Phase 3: Bootstrap Removal & Tailwind Migration

**Rationale for Removal**:
- Bootstrap 3 (current) is 10+ years old, unmaintained
- Bootstrap requires jQuery dependency (deprecated)
- Bootstrap CSS is heavy (~180KB uncompressed)
- Tailwind is lighter, more flexible, aligns with modern frontend practices

**Migration Path**:

15. Audit existing Bootstrap 3 usage in `resources/views/`:
    - Identify Grid classes (`.row`, `.col-xs-*`, `.col-md-*`)
    - Identify Component classes (`.btn`, `.btn-primary`, `.form-control`, `.panel`, etc.)
    - Identify Utility classes (`.text-center`, `.m-t-*`, `.pull-left`, etc.)

16. Remove Bootstrap dependencies from `package.json` and `gulpfile.js`
17. Remove all Bootstrap imports and CSS files

**Bootstrap 3 → Tailwind CSS 4 Mapping**:

```
Bootstrap Grid              → Tailwind Grid/Flexbox
.row                       → flex or grid (Tailwind)
.col-xs-12                 → w-full
.col-md-6                  → md:w-1/2
.col-lg-4                  → lg:w-1/3
.hidden-xs                 → hidden sm:block
.visible-md-block          → hidden md:block

Bootstrap Components       → Tailwind Utilities
.btn .btn-primary          → px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700
.form-control              → px-3 py-2 border border-gray-300 rounded focus:outline-none focus:ring-2
.panel .panel-default      → p-4 border border-gray-300 rounded-lg shadow
.label .label-default      → px-2 py-1 bg-gray-200 text-gray-800 rounded text-sm

Bootstrap Utilities        → Tailwind Utilities
.text-center               → text-center
.text-right                → text-right
.m-t-20                    → mt-5
.m-b-10                    → mb-2
.pull-left                 → float-left
.pull-right                → float-right
.hidden-sm                 → hidden sm:block
```

18. Update all view templates: Replace `.container` with `mx-auto px-4 max-w-7xl`
19. Update grid layouts: Convert `.row .col-*` to Tailwind Flexbox or CSS Grid
20. Update all button styles: Replace `.btn .btn-primary` with Tailwind button utilities
21. Update form inputs: Replace `.form-control` with Tailwind form utilities
22. Update cards/panels: Replace `.panel .panel-*` with Tailwind card components
23. Update navigation: Rebuild navbar with Tailwind + Alpine.js for mobile menu
24. Update alerts/messages: Replace Bootstrap alerts with Tailwind-styled alternatives
25. Remove all inline Bootstrap class references
26. Test responsive behavior on mobile, tablet, desktop viewports

---

## Phase 4: Modern Blade View Patterns

**Key Changes from Bootstrap 3 Blade Syntax**:

27. Replace deprecated view helpers with Blade components:
    - Remove: `{{ Form::open() }}`, `{{ Form::close() }}`, `{{ Form::text() }}`
    - Replace with: `<form class="..." method="POST">`

28. Implement modern Blade component architecture:
    - Create reusable components: `resources/views/components/button.blade.php`
    - Create form components: `resources/views/components/input.blade.php`
    - Create layout components: `resources/views/components/card.blade.php`

29. Use Blade component syntax throughout:
    ```blade
    <x-button type="primary">Click me</x-button>
    <x-input name="email" type="email" />
    <x-card title="Recipe Details">...</x-card>
    ```

30. Replace inline styles with Tailwind classes
31. Implement `@class()` directive for conditional Tailwind classes: `@class(['text-red-500' => $error])`
32. Use `@checked()`, `@selected()`, `@disabled()` directives for form elements
33. Replace deprecated `@unless` with modern `@if (!$condition)`
34. Create slot-based layouts for flexible component composition

---

## Phase 5: Database Schema & Migrations

35. Create migration: `recipes` table with columns (name, ingredients, instructions, notes, servings, calories, fat, cholesterol, sodium, protein, date_added, marked, classification_id, source_id)
36. Create migration: `classifications` table (id, name)
37. Create migration: `sources` table (id, name)
38. Create migration: `cookbooks` table (id, name)
39. Create migration: `meals` table (id, name)
40. Create migration: `preparations` table (id, name)
41. Create migration: `courses` table (id, name)
42. Create migration: `cookbook_recipes` (id, cookbook_id, recipe_id) - pivot table
43. Create migration: `recipe_meals` (id, recipe_id, meal_id) - pivot table
44. Create migration: `recipe_preparations` (id, recipe_id, preparation_id) - pivot table
45. Create migration: `recipe_courses` (id, recipe_id, course_id) - pivot table
46. Add foreign key constraints to all pivot tables and recipe table
47. Create migration: `users` table (for potential future authentication)
48. Run migrations: `php artisan migrate`

---

## Phase 6: Eloquent Models

49. Create `Recipe` model with relationships: `classification()`, `source()`, `meals()`, `preparations()`, `courses()`, `cookbooks()`
50. Create `Classification` model with `recipes()` relationship
51. Create `Source` model with `recipes()` relationship
52. Create `Cookbook` model with `recipes()` many-to-many relationship (with ordering by classification.name, then recipe.name)
53. Create `Meal` model with `recipes()` relationship
54. Create `Preparation` model with `recipes()` relationship
55. Create `Course` model with `recipes()` relationship
56. Add timestamps to all models
57. Define fillable/guarded attributes on all models
58. Add query scopes to Recipe for common filters (e.g., `orderByDateAdded()`, `orderByName()`)

---

## Phase 7: Search Implementation

59. Install Laravel Scout: `composer require laravel/scout`
60. Publish Scout config: `php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"`
61. Configure Scout to use database driver (simplest, no external dependency)
62. Add `Searchable` trait to `Recipe` model
63. Define `toSearchableArray()` on Recipe to index name and ingredients
64. Create a `Recipe::search()` scope for database-backed search
65. Implement search logic in RecipeController using Scout or custom query scope

---

## Phase 8: Controllers & Routing

66. Create `RecipeController` with `index()` and `show()` methods
67. Implement index method: accept `sortField` (name, date_added), `sortOrder` (asc, desc), `displayCount` (20, 30, all), `search` query parameters
68. Implement pagination (20 per page minimum, support "all" option)
69. Implement sorting: by date_added (descending default) or name (ascending default)
70. Implement search: query recipes by name and ingredients
71. Create `CookbookController` with `index()` and `show()` methods
72. Implement cookbook listing and detail views
73. Create `ContactController` with `index()` and `store()` for contact form (optional, from existing app)
74. Set up API-style routing using Route groups
75. Define route model binding for Recipe and Cookbook
76. Add middleware for any necessary request validation

---

## Phase 9: Views - Layout & Components

77. Create base layout in `resources/views/layouts/app.blade.php` with Tailwind classes
78. Create reusable components:
    - `resources/views/components/button.blade.php` - button styles
    - `resources/views/components/input.blade.php` - form input styling
    - `resources/views/components/card.blade.php` - card containers
    - `resources/views/components/recipe-card.blade.php` - recipe display
    - `resources/views/components/pagination.blade.php` - pagination links
    - `resources/views/components/sort-controls.blade.php` - sorting UI

79. Implement mobile-responsive navigation bar with Tailwind + Alpine.js
80. Create footer with project information
81. Add flash message display for user feedback
82. Implement breadcrumb navigation using Tailwind styling
83. Style form inputs and buttons with Tailwind utilities

---

## Phase 10: Views - Recipe Pages

84. Create `recipes/index.blade.php` with:
    - Search form (name, ingredients)
    - Sort controls (by date added, by name)
    - Display count selector (20, 30, all)
    - Recipe card grid using Tailwind responsive classes
    - Pagination controls
85. Create `recipes/show.blade.php` with:
    - Recipe details (name, ingredients, instructions, servings, nutritional info)
    - Classification and source display
    - Related meals, preparations, courses
    - Back to listing link
86. Implement recipe card component showing name, classification, date added, and nutrition summary

---

## Phase 11: Views - Cookbook Pages

87. Create `cookbooks/index.blade.php` with:
    - Cookbook listing
    - Each cookbook showing recipe count
    - Link to individual cookbook
88. Create `cookbooks/show.blade.php` with:
    - Cookbook name
    - Grouped recipe listing (ordered by classification, then name)
    - Recipe count and stats
89. Implement cookbook card component

---

## Phase 12: Views - Contact Form (Optional)

90. Create `contact/index.blade.php` with contact form
91. Create `contact/store()` controller action to handle form submission
92. Configure mail settings if sending emails
93. Add success/error flash messages

---

## Phase 13: Frontend Interactivity with Alpine.js

94. Implement sort controls using Alpine `@click` to toggle sort direction
95. Implement display count selector with Alpine to update page
96. Create search form with debounced input using Alpine (optional live search preview)
97. Add mobile menu toggle for navigation using Alpine
98. Implement recipe card hover effects with Tailwind
99. Add smooth transitions for pagination and filtering
100. Create Alpine components for common patterns (modals, dropdowns, tabs)

---

## Phase 14: Asset Pipeline & Build

101. Configure Vite entry points in `resources/js/app.js` and `resources/css/app.css`
102. Add npm scripts: `npm run dev` (watch mode), `npm run build` (production)
103. Build assets: `npm run build`
104. Verify Vite directives load correctly in Blade templates
105. Test hot module replacement (HMR) in development mode

---

## Phase 15: Database Seeding

106. Create seeders for Classifications, Sources, Meals, Preparations, Courses
107. Create Factory classes for Recipe, Cookbook for generating test data
108. Create DatabaseSeeder to run all seeders
109. Seed database: `php artisan migrate --seed`

---

## Phase 16: API Structure (Optional)

110. Consider creating API routes if future JSON endpoints needed
111. Use `api.php` route file for API endpoints
112. Create API resources for Recipe, Cookbook serialization
113. Add API documentation (optional)

---

## Phase 17: Testing & Quality Assurance

114. Create Feature test for `RecipeController@index` (test sorting, search, pagination)
115. Create Feature test for `RecipeController@show`
116. Create Feature test for `CookbookController@index` and `@show`
117. Create Unit test for Recipe model scopes and relationships
118. Create Unit test for Cookbook model relationship ordering
119. Test search functionality with various query inputs
120. Verify pagination with different display counts
121. Test sorting by name and date in both directions
122. Test responsive design on mobile viewports
123. Verify all links and navigation work correctly
124. Test form submissions (contact form if included)
125. Performance testing: verify queries are optimized (use eager loading)
126. Test with production build (`npm run build`)

---

## Phase 18: Optimization & Polish

127. Add database indexes on frequently queried columns (name, classification_id, date_added)
128. Optimize images and assets
129. Implement query optimization: eager load relationships to prevent N+1 queries
130. Add caching for recipe listings if needed
131. Configure environment variables for production
132. Set up error handling and logging
133. Add 404 and error page views with Tailwind styling
134. Implement accessibility best practices (alt text, ARIA labels, semantic HTML)
135. Verify Lighthouse score (>90 on all metrics)
136. Minify assets in production build

---

## Phase 19: Documentation & Deployment

137. Create `README.md` with:
    - Project overview
    - Tech stack (Laravel 12, Tailwind CSS 4+, Alpine.js 3, Vite, PHP 8.5+, Node.js 25+)
    - Setup instructions
    - Database schema overview
    - Feature documentation
138. Update `.github/copilot-instructions.md` with new modern patterns
139. Document API endpoints if created
140. Create `DEVELOPMENT.md` with development workflow
141. Set up `.env.example` with all required variables
142. Configure deployment strategy (Vercel, Railway, Laravel Forge, etc.)
143. Test fresh installation from scratch
144. Set up CI/CD pipeline (GitHub Actions) if desired

---

## Success Criteria

✅ Application boots without errors on Laravel 12  
✅ All tests pass (Feature and Unit)  
✅ Recipe listing functional with sorting by name and date (ascending/descending)  
✅ Pagination works with configurable display count (20, 30, all)  
✅ Search functionality works on name and ingredients  
✅ Cookbook listing and detail pages work  
✅ All relationships display correctly (classifications, sources, meals, preparations, courses)  
✅ UI is modern, responsive, and uses Tailwind CSS 4 consistently  
✅ No N+1 database queries  
✅ Contact form works (if included)  
✅ No console errors or warnings  
✅ Production build passes static analysis  
✅ Similar look and feel to original but with modern design language  
✅ Fresh database setup works: `php artisan migrate --seed`  
✅ All code follows modern Laravel 12 conventions  
✅ Bootstrap completely removed with no legacy CSS  
✅ All views use Blade components and Tailwind utilities  
✅ Lighthouse performance score >90  
✅ Mobile responsive on all screen sizes
