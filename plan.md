# Plan: Transform Hugo Site into Minimalist Tech Blog with Hugo-XMin Theme

## Current State Analysis
- **Template**: One Click Hugo CMS (Kaldi coffee shop template)
- **Structure**: Small business site with products, values, contact sections
- **Content**: Coffee shop marketing content
- **CMS**: Decap CMS integration
- **Build**: Webpack-based with PostCSS

## Transformation Goals
- Use **hugo-xmin** theme (extremely minimal - ~140 lines of code)
- Integrate with Decap CMS for content management
- Create clean, developer-focused tech blog
- Maintain current build system compatibility

---

## Phase 1: Theme Installation & Setup

### 1.1 Install Hugo-XMin Theme
- [ ] Add hugo-xmin as a Git submodule in `site/themes/hugo-xmin`:
  ```bash
  cd site
  git submodule add https://github.com/yihui/hugo-xmin.git themes/hugo-xmin
  ```
- [ ] Or copy theme files directly to `site/themes/hugo-xmin/` (if avoiding submodules)

### 1.2 Update Hugo Configuration
- [ ] Update `site/hugo.toml`:
  - Set `theme = "hugo-xmin"`
  - Update site title for tech blog
  - Enable RSS feeds (remove from `disableKinds`)
  - Enable taxonomies: tags and categories (remove from `disableKinds`)
  - Configure menu items (Home, About, Tags)
  - Set up permalinks: `/blog/:year/:month/:title/`
  - Enable syntax highlighting with Chroma
  - Configure author info in params

---

## Phase 2: Content Structure Cleanup

### 2.1 Remove Business Content
- [ ] Delete `site/content/products/`
- [ ] Delete `site/content/values/`
- [ ] Delete `site/content/contact/`
- [ ] Delete existing posts in `site/content/post/`:
  - `brewing-chemex/`
  - `making-sense/`
  - `jamaica-blue/`

### 2.2 Create New Blog Structure
- [ ] Rename `site/content/post/` to `site/content/blog/` (optional, or keep as `post/`)
- [ ] Create `site/content/about.md` for author/blog info
- [ ] Update `site/content/_index.md` for homepage
- [ ] Create sample tech blog posts (2-3):
  - Introduction to the blog
  - Sample tutorial with code
  - Technical deep-dive example

---

## Phase 3: Decap CMS Configuration

### 3.1 Update CMS Collections
- [ ] Modify `site/static/admin/config.yml`:
  - Remove `products` collection
  - Remove `values` section
  - Remove `contact` section
  - Update `post` collection name to `blog` (or keep as `post`)
  - Configure blog post fields:
    - title (string)
    - date (datetime)
    - author (string)
    - tags (list)
    - categories (list)
    - draft (boolean)
    - body (markdown with code support)
  - Remove homepage business sections (blurb, intro, products, values)
  - Simplify homepage to: title, subtitle, intro text

### 3.2 Configure Editor Settings
- [ ] Add code-friendly editor settings:
  - Enable markdown preview
  - Configure code block support
  - Set up syntax highlighting in editor
  - Enable draft/publish workflow

---

## Phase 4: Theme Customization

### 4.1 Override Theme Layouts (if needed)
- [ ] Create `site/layouts/` overrides for:
  - `partials/header.html` - Add site name/logo
  - `partials/footer.html` - Add copyright, links
  - `partials/head_custom.html` - Add custom meta tags, analytics
  - `partials/foot_custom.html` - Add custom scripts if needed

### 4.2 Customize Theme Styling
- [ ] Create `site/static/css/custom.css` for additional styles:
  - Code block styling improvements
  - Syntax highlighting theme
  - Mobile responsiveness tweaks
  - Any brand-specific colors
- [ ] Link custom CSS in layout overrides

### 4.3 Theme Features to Utilize
- [ ] Configure hugo-xmin's built-in features:
  - Single post layout (minimal and clean)
  - List pages for blog index
  - Taxonomy templates for tags/categories
  - RSS feed (auto-generated)
  - Simple navigation

---

## Phase 5: Build System Integration

### 5.1 Update Webpack Configuration
- [ ] Modify webpack configs to work with theme:
  - Keep existing asset processing
  - Ensure CSS builds to `site/static/css/`
  - Remove business-specific asset compilation
  - Update paths if needed

### 5.2 Remove Unused Assets
- [ ] Clean up `src/` directory:
  - Remove business-specific JavaScript
  - Simplify CSS imports
  - Keep only essential build files
- [ ] Clean up `site/static/img/`:
  - Remove coffee shop images
  - Keep only blog placeholders/icons

### 5.3 Update Package Scripts
- [ ] Verify `package.json` scripts work:
  - `yarn start` / `npm start` for development
  - `yarn build` / `npm run build` for production
  - Ensure Hugo builds with new theme

---

## Phase 6: Blog Features & Configuration

### 6.1 Enable Hugo Features
- [ ] Configure in `hugo.toml`:
  - Syntax highlighting (Chroma):
    ```toml
    [markup]
      [markup.highlight]
        style = "monokai"
        lineNos = true
        lineNumbersInTable = true
    ```
  - Table of contents:
    ```toml
    [markup]
      [markup.tableOfContents]
        startLevel = 2
        endLevel = 4
    ```
  - Goldmark markdown settings for code

### 6.2 Set Up Taxonomies
- [ ] Configure taxonomies in `hugo.toml`:
  ```toml
  [taxonomies]
    tag = "tags"
    category = "categories"
  ```
- [ ] Create taxonomy pages (handled by theme)
- [ ] Define initial categories and tags

### 6.3 RSS & Meta Tags
- [ ] Configure RSS feed settings
- [ ] Set up Open Graph meta tags
- [ ] Add Twitter Card support
- [ ] Configure canonical URLs

---

## Phase 7: Navigation & Menus

### 7.1 Configure Main Menu
- [ ] Set up in `hugo.toml`:
  ```toml
  [[menu.main]]
    name = "Home"
    url = "/"
    weight = 1

  [[menu.main]]
    name = "Blog"
    url = "/post/"
    weight = 2

  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 3

  [[menu.main]]
    name = "Tags"
    url = "/tags/"
    weight = 4
  ```

### 7.2 Update Theme Header
- [ ] Override `layouts/partials/header.html`:
  - Add site name/title
  - Implement menu navigation
  - Keep minimal design
  - Mobile-friendly menu

---

## Phase 8: Content Creation

### 8.1 Write Homepage Content
- [ ] Update `site/content/_index.md`:
  - Brief introduction to the blog
  - Focus area/topics
  - Recent posts (auto-generated by theme)

### 8.2 Create About Page
- [ ] Write `site/content/about.md`:
  - Author bio
  - Blog purpose
  - Contact info (optional)
  - Tech stack used

### 8.3 Create Sample Blog Posts
- [ ] Write 3 initial posts in `site/content/post/`:
  1. **Welcome/Intro post** - Blog goals and topics
  2. **Technical tutorial** - With code examples
  3. **Project/tool review** - Demonstrate formatting

- [ ] Each post should include:
  - Front matter: title, date, tags, categories
  - Code blocks with syntax highlighting
  - Images (if applicable)
  - Proper markdown formatting

---

## Phase 9: Decap CMS Integration Testing

### 9.1 Test CMS Functionality
- [ ] Verify Decap CMS can:
  - Create new blog posts
  - Edit existing posts
  - Preview posts correctly
  - Save drafts
  - Publish posts
  - Manage tags/categories
  - Upload images

### 9.2 Configure Media Library
- [ ] Set up in `site/static/admin/config.yml`:
  ```yaml
  media_folder: "site/static/img/uploads"
  public_folder: "/img/uploads"
  ```
- [ ] Test image uploads through CMS
- [ ] Verify images display in posts

### 9.3 Preview Templates
- [ ] Create CMS preview templates if needed:
  - Match hugo-xmin styling in CMS preview
  - Ensure code blocks render correctly
  - Test responsive preview

---

## Phase 10: Styling & Design Polish

### 10.1 Review Hugo-XMin Defaults
- [ ] Examine default styles in theme's `static/css/style.css`
- [ ] Identify areas needing customization
- [ ] Keep minimal aesthetic

### 10.2 Add Custom Enhancements
- [ ] Create `site/static/css/custom.css`:
  - Enhanced code block styling
  - Better syntax highlighting colors
  - Improved mobile spacing
  - Optional: dark mode support
  - Link/hover effects
  - Image captions styling

### 10.3 Typography Refinement
- [ ] Optimize reading experience:
  - Line height for paragraphs
  - Code font selection
  - Heading hierarchy
  - Mobile font sizes

---

## Phase 11: Performance & SEO

### 11.1 Optimize Assets
- [ ] Minify CSS/JS in production
- [ ] Optimize images with Hugo's image processing
- [ ] Enable Hugo's minification:
  ```toml
  [minify]
    minifyOutput = true
  ```

### 11.2 SEO Configuration
- [ ] Add to `hugo.toml`:
  - Site description
  - Author information
  - Social media links
- [ ] Set up `robots.txt`
- [ ] Configure sitemap.xml (auto-generated)
- [ ] Add structured data (JSON-LD) for articles

### 11.3 Meta Tags
- [ ] Create `layouts/partials/head_custom.html`:
  - Open Graph tags
  - Twitter Cards
  - Canonical URLs
  - Favicon links

---

## Phase 12: Build & Deployment

### 12.1 Update Documentation
- [ ] Rewrite `README.md`:
  - Update project description (tech blog)
  - Hugo-xmin theme info
  - Local development instructions
  - Blog post creation guide
  - Deployment steps

### 12.2 Update Build Configuration
- [ ] Verify `netlify.toml` settings
- [ ] Ensure build command works with theme
- [ ] Test production build locally
- [ ] Verify all assets compile correctly

### 12.3 Testing Checklist
- [ ] Build site: `hugo -s site`
- [ ] Test locally: `yarn start`
- [ ] Verify all pages render
- [ ] Check responsive design
- [ ] Test code syntax highlighting
- [ ] Verify RSS feed: `/index.xml`
- [ ] Test tag/category pages
- [ ] Check Decap CMS access: `/admin`
- [ ] Test creating post via CMS
- [ ] Validate HTML/CSS
- [ ] Check console for errors

### 12.4 Deploy
- [ ] Push to repository
- [ ] Deploy to Netlify (auto-deploy)
- [ ] Verify production site
- [ ] Test CMS on production
- [ ] Configure custom domain (if applicable)

---

## Phase 13: Optional Enhancements

### 13.1 Extended Features
- [ ] Add reading time to posts
- [ ] Implement table of contents
- [ ] Add "Edit on GitHub" links
- [ ] Create archive page (posts by date)
- [ ] Add related posts section
- [ ] Implement search (lunr.js or similar)

### 13.2 Analytics & Engagement
- [ ] Add analytics (Plausible, Fathom, or GA)
- [ ] Set up comment system:
  - Utterances (GitHub issues)
  - Giscus (GitHub discussions)
  - Disqus
- [ ] Newsletter integration (optional)
- [ ] Social sharing buttons

### 13.3 Developer Features
- [ ] Set up archetypes for new posts:
  ```bash
  hugo new post/my-new-post.md
  ```
- [ ] Create shortcodes for common elements
- [ ] Add code copy buttons
- [ ] Syntax highlighting theme selector

---

## Hugo-XMin Theme Notes

### Why Hugo-XMin?
- **Minimal**: Only ~140 lines of code total
- **Fast**: Lightweight, no bloat
- **Educational**: Easy to understand and customize
- **Flexible**: Simple to extend
- **Accessible**: Clean semantic HTML

### Theme Structure
```
themes/hugo-xmin/
├── layouts/
│   ├── _default/
│   │   ├── list.html (20 lines)
│   │   ├── single.html (12 lines)
│   │   └── terms.html (13 lines)
│   ├── partials/
│   │   ├── header.html (20 lines)
│   │   └── footer.html (9 lines)
│   └── 404.html (5 lines)
└── static/css/
    ├── style.css (51 lines)
    └── fonts.css (7 lines)
```

### Customization Strategy
1. **Don't modify theme files directly**
2. **Override in `site/layouts/`** - Hugo gives precedence to root layouts
3. **Add custom CSS** in `site/static/css/custom.css`
4. **Use partials** `head_custom.html` and `foot_custom.html` for injections

---

## Decap CMS Integration Specifics

### Key Configuration Points

1. **Content Path**: Ensure CMS points to correct content folder
   ```yaml
   collections:
     - name: "blog"
       label: "Blog"
       folder: "site/content/post"
   ```

2. **Preview**: CMS preview may not match theme exactly (acceptable)

3. **Widgets**: Use appropriate widgets for front matter:
   - String for title
   - DateTime for date
   - List for tags/categories
   - Boolean for draft status
   - Markdown for body

4. **Identity**: Keep existing Netlify Identity setup

---

## Expected File Changes Summary

### Files to Modify:
- `site/hugo.toml` - Major configuration changes
- `site/static/admin/config.yml` - CMS configuration
- `site/content/_index.md` - New homepage content
- `webpack.*.js` - Minor path/asset updates
- `README.md` - Complete rewrite
- `package.json` - Script verification

### Files to Create:
- `site/themes/hugo-xmin/` - Theme installation
- `site/content/about.md` - About page
- `site/content/post/*.md` - New blog posts
- `site/static/css/custom.css` - Custom styles
- `site/layouts/partials/head_custom.html` - Custom head content
- `site/layouts/partials/foot_custom.html` - Custom footer scripts

### Files to Delete:
- `site/content/products/`
- `site/content/values/`
- `site/content/contact/`
- `site/content/post/*` - Old posts
- `site/layouts/section/products.html`
- `site/layouts/section/values.html`
- `site/layouts/section/contact.html`
- Many partials in `site/layouts/partials/`
- Business images in `site/static/img/`
- Current layouts in `site/layouts/` (replaced by theme)

---

## Success Criteria

✅ Hugo-xmin theme installed and working
✅ Decap CMS can create/edit blog posts
✅ Clean, minimal design renders correctly
✅ Code syntax highlighting works
✅ Tags and categories function properly
✅ RSS feed generates correctly
✅ Mobile responsive
✅ Build process works (dev and production)
✅ Site deploys successfully
✅ All old business content removed
✅ Sample blog posts demonstrate features

---

## Timeline Estimate

- **Phase 1-2**: 1-2 hours (Theme setup, content cleanup)
- **Phase 3-4**: 2-3 hours (CMS config, theme customization)
- **Phase 5-7**: 1-2 hours (Build system, features, navigation)
- **Phase 8-9**: 2-4 hours (Content creation, CMS testing)
- **Phase 10-11**: 1-2 hours (Styling, optimization)
- **Phase 12**: 1-2 hours (Testing, deployment)
- **Phase 13**: Optional, as needed

**Total**: ~8-16 hours depending on customization depth
