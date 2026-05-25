# shahar-local-biz

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
    path = "github.com/shaharbest/shahar-local-biz"
EOF

# 4. Fetch the theme
hugo mod get github.com/shaharbest/shahar-local-biz@latest
hugo mod tidy

# 5. Start dev server
hugo server
```

Copy the data file schemas (below) and the `config.toml` params to configure your site.

## Updating the theme

```bash
hugo mod get -u github.com/shaharbest/shahar-local-biz
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

Sections are rendered in this fixed order by `layouts/index.html`:

| Section | Partial | Data source | Conditional |
|---|---|---|---|
| Hero | `sections/hero.html` | `content/_index.md` body + `data/contact.yaml` | Always shown |
| Services | `sections/services.html` | `data/services.json` | Hidden if `services` array is empty |
| Hours | `sections/hours.html` | `data/hours.json` | Always shown |
| Testimonials | `sections/testimonials.html` | `data/testimonials.json` | Hidden if `testimonials` array is empty |
| FAQ | `sections/faq.html` | `data/faq.json` | Always shown |

---

## SCSS architecture

All styles live in `assets/scss/`. The entry point is `style.scss`, which imports in order:

1. `_variables.scss` — receives Hugo param values (colors, fonts) injected as SCSS variables
2. Bootstrap partials — `reboot`, `grid`, `utilities` only (not full Bootstrap)
3. `libraries/hamburgers/` — hamburger menu animation library
4. Component files (`_header`, `_footer`, `_main-menu`, `_buttons`, `_intro`, `_call`, etc.)
5. Page-specific files under `pages/`

To restyle a specific element, find its component file (e.g. `_header.scss` for the sticky header, `_call.scss` for the contact box) and edit there. Avoid inline styles in templates — put them in the component SCSS.

---

## Decap CMS compatibility

This theme's data schemas are designed to work with Decap CMS file collections. Each `data/*.json` / `data/*.yaml` file maps to a CMS collection. The `data/features.json` object-wrapper pattern (`{ "services": [...] }` instead of a bare array) is required by Decap.

When setting up a new site's `static/admin/config.yml`, mirror the collection definitions from an existing site (see philly-green-clean for a working example).
