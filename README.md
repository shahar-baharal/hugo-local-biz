# hugo-local-biz

Hugo theme for local business lead-generation websites. Covers the full homepage with configurable sections (hero, services, hours, testimonials, FAQ), a contact box, SEO/schema markup, and Decap CMS compatibility. MIT licensed.

## Requirements

| Dependency | Minimum version | Why |
|---|---|---|
| Hugo **extended** | 0.110.0 | SCSS compilation via `toCSS` |
| Go | 1.21 | Hugo modules dependency resolution |

## Quickstart — new site

```bash
# 1. Create site
hugo new site my-business-site
cd my-business-site

# 2. Initialize as a Hugo module (use your actual GitHub repo URL)
hugo mod init github.com/yourname/my-business-site

# 3. Add theme import to hugo.toml (or config.toml)
cat >> hugo.toml <<'EOF'
[module]
  [[module.imports]]
    path = "github.com/shahar-baharal/hugo-local-biz"
EOF

# 4. Fetch the theme
hugo mod get github.com/shahar-baharal/hugo-local-biz@latest
hugo mod tidy

# 5. Start dev server
hugo server
```

Copy the data file schemas (below) and the `config.toml` params to configure your site.

## Updating the theme

```bash
hugo mod get -u github.com/shahar-baharal/hugo-local-biz
hugo mod tidy
git add go.mod go.sum
git commit -m "Update theme"
git push
```

---

## `config.toml` params reference

All keys live under `[params]` unless noted.

| Key | Type | Default | Description |
|---|---|---|---|
| `google_analytics_id` | string | `""` | Google Analytics measurement ID (`G-XXXXXXXX`). Leave empty to disable. |
| `google_tag_manager_id` | string | `""` | Google Tag Manager container ID (`GTM-XXXXXXX`). Leave empty to disable. |
| `favicon` | string | — | Path to favicon relative to `static/`, e.g. `images/favicon.ico` |
| `description` | string | — | Default meta description used on pages that don't set their own |

### `[params.homepage]`

| Key | Type | Default | Description |
|---|---|---|---|
| `sticky_header` | bool | `false` | Makes the header stick to the top on scroll |
| `show_info_box` | bool | `false` | Shows the contact info box (phone + email) in the hero section |
| `sections` | `[]string` | all five in default order | Ordered list of homepage sections to render. See [Homepage sections](#homepage-sections). |

### `[params.logo]`

| Key | Type | Description |
|---|---|---|
| `desktop` | string | Path to desktop logo (relative to `static/`), e.g. `images/logo/logo.svg` |
| `desktop_height` | string | CSS height value, e.g. `"40px"` |
| `mobile` | string | Path to mobile logo. Falls back to `desktop` if omitted. |
| `mobile_height` | string | CSS height for mobile logo, e.g. `"36px"` |
| `alt` | string | Alt text for the logo image |

### `[params.fonts]`

| Key | Type | Description |
|---|---|---|
| `google_fonts` | string | Full Google Fonts URL, e.g. `"https://fonts.googleapis.com/css2?family=..."`. Omit to skip the `<link>` tag. |
| `heading` | string | CSS font-family for headings, e.g. `"Playfair Display"` |
| `base` | string | CSS font-family for body text, e.g. `"Source Sans Pro"` |

### `[params.colors]`

These are injected as SCSS variables. All values must be valid CSS color strings.

| Key | Description |
|---|---|
| `primary` | Brand/accent color — used for buttons, links, highlights |
| `black` | Dark text / dark backgrounds |
| `white` | Light backgrounds |
| `white_offset` | Slightly tinted white — used for alternating section backgrounds |
| `grey` | Secondary text, muted elements |

### `[params.footer]`

| Key | Type | Description |
|---|---|---|
| `copyright_text` | string | HTML-safe copyright line, e.g. `"Copyright &copy; 2026 Acme LLC"` |

### `[params.seo]`

| Key | Type | Description |
|---|---|---|
| `meta_og_image` | string | Default OG image path (relative to `static/`) used when `data/seo.yaml` is absent |

### Menus

Define in `[menu]` blocks. Two menus are used:

```toml
[[menu.main]]
  name = "Services"
  url = "/#services"
  weight = 1

[[menu.footer]]
  name = "Home"
  url = "/"
  weight = 1
```

- `main` — rendered in the desktop header and mobile drawer
- `footer` — rendered in the footer

---

## Data file schemas

Each site must provide these files in its `data/` directory. All fields are required unless marked optional.

### `data/services.json`

```json
{
  "services": [
    {
      "title": "Service Name",
      "summary": "Short description shown under the title on the homepage.",
      "icon": "images/services/my-icon.png"
    }
  ]
}
```

- `icon` — optional. Path relative to `static/`. Rendered as a 100×100 `<img>`.
- The services section is only rendered if `services` array is non-empty.

### `data/hours.json`

```json
{
  "hours": [
    { "day": "Sunday",    "opens": "09:00", "closes": "17:00" },
    { "day": "Monday",    "opens": "09:00", "closes": "17:00" },
    { "day": "Saturday",  "closed": true }
  ]
}
```

- `opens` / `closes` — 24-hour format strings, e.g. `"09:00"`. Omit when `closed: true`.
- `closed` — optional boolean. Renders "Closed" instead of hours.
- Days can be any string — not restricted to weekday names.

### `data/testimonials.json`

```json
{
  "testimonials": [
    {
      "quote": "Great service, highly recommend.",
      "name": "John D.",
      "location": "South Philly",
      "service": "Air Duct Cleaning"
    }
  ]
}
```

- `location` — optional.
- `service` — optional. Free-form string.
- Testimonials section is only rendered if the array is non-empty.

### `data/faq.json`

```json
{
  "items": [
    {
      "question": "How often should I clean my air ducts?",
      "answer": "Every 3–5 years for most homes."
    }
  ]
}
```

### `data/contact.yaml`

```yaml
phone: "215-555-0100"
email: "hello@example.com"
```

Used by the contact info box in the hero and the `call` partial. Both fields are optional — omitting a field hides that contact method.

### `data/seo.yaml`

```yaml
og_image: /images/og.png          # Default OG image for social previews (absolute path from static/)
og_description: "Your default social preview description."
twitter_site: "@yourhandle"       # optional, include @
twitter_card: summary_large_image # or: summary
```

All fields are optional. `og_image` and `og_description` can be overridden per page in front matter.

### `data/schema.yaml`

Populates the JSON-LD `LocalBusiness` structured data block in `<head>`.

```yaml
business_type: "LocalBusiness"   # or a more specific schema.org type, e.g. "HVACBusiness"
address_locality: "Philadelphia"
address_region: "PA"
address_country: "US"
area_served:
  - "Philadelphia"
  - "Montgomery County"
price_range: "$$"                 # optional, schema.org priceRange
```

---

## Homepage sections

Configure which sections appear and in what order via `params.homepage.sections` in `hugo.toml`:

```toml
[params.homepage]
  sections = ["hero", "services", "hours", "testimonials", "faq"]
```

Omit a name to hide that section. Reorder entries to change display order. If `sections` is not set, the default order above is used.

### Available sections

| Name | Partial | Data source | Notes |
|---|---|---|---|
| `hero` | `sections/hero.html` | `content/_index.md` + `data/contact.yaml` | Always shown when included |
| `services` | `sections/services.html` | `data/services.json` | Self-hides if `services` array is empty |
| `hours` | `sections/hours.html` | `data/hours.json` | Always shown when included |
| `testimonials` | `sections/testimonials.html` | `data/testimonials.json` | Self-hides if `testimonials` array is empty |
| `faq` | `sections/faq.html` | `data/faq.json` | Always shown when included |
| `features` | `sections/features.html` | `data/features.json` | Not in default list; self-hides if `features` array is empty |

### Adding a custom section

The full pattern has four parts: data file, template, CMS collection, and registration. Here's a complete walkthrough using a "promotions" section as the example.

#### 1. Create the data file

Add `data/promotions.json` in your site repo:

```json
{
  "promotions": [
    {
      "title": "10% Off First Visit",
      "description": "Book online and save on your first service."
    }
  ]
}
```

Use a top-level wrapper key matching the filename (`"promotions"`). This is required by Decap CMS's file collection format.

Access it in templates via `hugo.Data.promotions.promotions`.

#### 2. Create the template partial

Add `layouts/partials/sections/promotions.html` in your site repo (not the theme — Hugo's lookup order merges site and theme layouts):

```html
{{ if hugo.Data.promotions.promotions }}
<div id="promotions" class="strip">
  <div class="container pt-6 pb-6 pt-md-10 pb-md-10">
    <div class="row justify-content-center">
      <div class="col-12 text-center mb-4">
        <h2>Current Promotions</h2>
      </div>
    </div>
    <div class="row justify-content-center">
      {{ range hugo.Data.promotions.promotions }}
      <div class="col-12 col-md-6 mb-4">
        <h3>{{ .title }}</h3>
        <p>{{ .description }}</p>
      </div>
      {{ end }}
    </div>
  </div>
</div>
{{ end }}
```

Wrapping the whole thing in `{{ if hugo.Data.promotions.promotions }}` makes the section self-hide when the data file is empty or absent — no config change needed to toggle it off.

#### 3. Add a CMS collection

Add this block to `static/admin/config.yml` under `collections:`:

```yaml
- name: "promotions"
  label: "Promotions"
  files:
    - label: "Promotions"
      name: "promotions"
      file: "data/promotions.json"
      fields:
        - label: "Promotions"
          name: "promotions"
          widget: "list"
          fields:
            - { label: "Title", name: "title", widget: "string" }
            - { label: "Description", name: "description", widget: "text" }
```

Each field in `fields` maps to a key in the JSON objects. Widget types: `string` (single line), `text` (multiline), `image` (file picker), `boolean`, `select`.

#### 4. Register it in `hugo.toml`

Add the section name to the `sections` array in the position you want it to appear:

```toml
[params.homepage]
  sections = ["hero", "services", "promotions", "hours", "faq"]
```

#### Optional: add styles

Add `assets/scss/components/_promotions.scss` in your site repo, then create `assets/scss/style.scss` in the site repo with:

```scss
@import 'components/promotions';
```

Hugo merges asset directories from site and theme, so this file extends (not replaces) the theme's `style.scss`.

---

## Header customization

### Overriding the logo area

The top-left corner of the header is rendered by `layouts/partials/header-logo.html`. Hugo's lookup order lets any site replace it by dropping a file at the same path inside the site's own `layouts/` directory — no theme edits required.

**Default behavior** (icon only, from the theme):

```
themes/hugo-local-biz/layouts/partials/header-logo.html
```

**To override in your site**, create:

```
layouts/partials/header-logo.html
```

The partial receives the full Hugo page context (`.`), so `.Site.Params`, `.Site.Title`, menus, etc. are all available.

**Example: icon + company name side by side**

```html
<div class="logo">
  <a href="{{ .Site.Home.RelPermalink }}" style="display: flex; align-items: center; gap: 10px; text-decoration: none;">
    <img height="{{ .Site.Params.logo.desktop_height }}" alt="{{ .Site.Params.logo.alt }}" src="{{ .Site.Params.logo.desktop | relURL }}" />
    <span style="font-family: var(--font-heading); font-weight: 600; color: var(--primary); font-size: 1.15rem; white-space: nowrap;">{{ .Site.Title }}</span>
  </a>
</div>
<div class="logo-mobile">
  <a href="{{ .Site.Home.RelPermalink }}" style="display: flex; align-items: center; gap: 8px; text-decoration: none;">
    <img height="{{ .Site.Params.logo.mobile_height }}" alt="{{ .Site.Params.logo.alt }}" src="{{ .Site.Params.logo.mobile | relURL }}" />
    <span style="font-family: var(--font-heading); font-weight: 600; color: var(--primary); font-size: 1rem; white-space: nowrap;">{{ .Site.Title }}</span>
  </a>
</div>
```

The `.logo` / `.logo-mobile` wrappers are still required — the theme's `_logo.scss` uses those classes to handle desktop/mobile visibility. The CSS custom properties `--font-heading`, `--font-base`, `--primary`, `--black`, etc. are all available (set from `hugo.toml` params at build time).

---

## SCSS architecture

All styles live in `assets/scss/`. The entry point is `style.scss`, which imports in order:

1. Hugo param values (colors, fonts) injected as SCSS variables, then a `:root {}` block exposing them as CSS custom properties (`--primary`, `--black`, etc.)
2. `_breakpoints.scss` — `media-breakpoint-up()` mixin with the four standard breakpoints (sm/md/lg/xl)
3. `_reset.scss` — minimal modern CSS reset (box-sizing, zero margins, font smoothing)
4. `_grid.scss` — flexbox grid: `.container`, `.row`, `.col-*` for the column classes used in templates
5. `_utilities.scss` — only the utility classes used in templates (spacing, text, display, etc.)
6. `libraries/hamburgers/` — hamburger menu animation library
7. Component files (`_header`, `_footer`, `_main-menu`, `_buttons`, `_intro`, `_call`, etc.)
8. Page-specific files under `pages/`

To restyle a specific element, find its component file (e.g. `_header.scss` for the sticky header, `_call.scss` for the contact box) and edit there. Avoid inline styles in templates — put them in the component SCSS.

All color and font values are exposed as CSS custom properties. Components use `var(--primary)`, `var(--black)`, etc. rather than SCSS variables directly — this makes them visible in browser devtools and overridable at runtime.

---

## Decap CMS compatibility

This theme's data schemas are designed to work with Decap CMS file collections. Each `data/*.json` / `data/*.yaml` file maps to a CMS collection. The `data/features.json` object-wrapper pattern (`{ "services": [...] }` instead of a bare array) is required by Decap.

When setting up a new site's `static/admin/config.yml`, mirror the collection definitions from an existing site (see philly-green-clean for a working example).
