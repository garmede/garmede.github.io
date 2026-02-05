# CLAUDE.md - AI Assistant Guide for GeoGon Blog

This document provides comprehensive guidance for AI assistants working with the GeoGon blog repository.

## Project Overview

**GeoGon** is a personal technical blog about computer graphics, written in Korean. The site focuses on shaders, rendering, game engines, and technical art topics.

- **Site URL:** https://garmede.github.io
- **Author:** 차영곤 (Cha Young-gon)
- **Language:** Korean (ko-KR)
- **Framework:** Jekyll with Chirpy theme
- **Hosting:** GitHub Pages

## Tech Stack

| Component | Technology |
|-----------|------------|
| Static Site Generator | Jekyll 4.2.2 |
| Theme | jekyll-theme-chirpy (~> 5.2.1) |
| Language | Ruby |
| Markdown Processor | Kramdown with GFM |
| Syntax Highlighting | Rouge |
| Comments | Giscus (GitHub Discussions) |
| Analytics | Google Analytics |
| CI/CD | GitHub Actions |

## Directory Structure

```
garmede.github.io/
├── _config.yml              # Main Jekyll configuration
├── _posts/                  # Published blog posts (YYYY-MM-DD-slug.md)
├── _drafts/                 # Draft posts (no date prefix needed)
├── _tabs/                   # Static pages (about, archives, categories, tags)
├── _data/
│   ├── author.yml           # Author definitions
│   ├── contact.yml          # Contact/social links config
│   ├── share.yml            # Social sharing options
│   ├── locales/             # i18n translations (ko-KR is primary)
│   └── assets/              # Asset loading configuration
├── _sass/                   # SCSS custom styles
├── _plugins/                # Jekyll Ruby plugins
│   └── posts-lastmod-hook.rb  # Auto-detects post modification dates
├── assets/
│   ├── img/
│   │   ├── blog/            # Blog post images (.webp)
│   │   ├── tutorial/        # Tutorial images (.webp)
│   │   └── favicons/        # Site favicons
│   ├── fonts/               # Custom fonts
│   └── lib/                 # Submodule: chirpy-static-assets
├── tools/
│   └── deploy.sh            # Deployment script
├── .github/workflows/
│   └── pages.yml            # GitHub Actions CI/CD workflow
├── Gemfile                  # Ruby dependencies
└── .editorconfig            # Code style conventions
```

## Development Workflow

### Local Development

```bash
# Install dependencies
bundle install

# Start development server with drafts
bundle exec jekyll serve --drafts

# Build for production
JEKYLL_ENV=production bundle exec jekyll build
```

### Creating New Content

#### Blog Posts

1. Create a new file in `_posts/` with the naming pattern:
   ```
   YYYY-MM-DD-title-slug.md
   ```

2. Use this frontmatter template:
   ```yaml
   ---
   title: "제목 (Korean title)"
   categories: [Blog]  # or [Tutorial]
   tags: [태그1, 태그2]  # Korean tags
   image:
     path: /assets/img/blog/image-name.webp
   author: gon
   ---
   ```

3. Write content in Korean using Markdown

#### Draft Posts

- Place drafts in `_drafts/` without date prefix
- Filename: `title-slug.md`
- Use `jekyll serve --drafts` to preview

### Image Conventions

- **Format:** WebP (preferred for web optimization)
- **Blog images:** `/assets/img/blog/`
- **Tutorial images:** `/assets/img/tutorial/`
- **Alignment:** Use `{: .align-center}` for centered images
- **Captions:** Add `*caption text*` on a new line after image

Example:
```markdown
![Alt text](/assets/img/blog/image.webp){: .align-center}
*Image caption here*
```

## Content Writing Conventions

### Language & Formatting

- **Primary language:** Korean (ko-KR)
- **Abbreviations:** Define at end of post with `*[TERM]: Definition`
- **Emphasis:** Use `**bold**` for key terms
- **Quotes/Tips:** Use Chirpy's prompt blocks

### Prompt Blocks (Chirpy Theme)

```markdown
> **Title**\
> Content here
{: .prompt-tip }

> Warning content
{: .prompt-warning }

> Info content
{: .prompt-info }

> Danger content
{: .prompt-danger }
```

### Embedded Media

YouTube:
```html
<iframe width="100%" height="450"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="YouTube video player" frameborder="0"
  allow="accelerometer; autoplay; clipboard-write;
  encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen></iframe>
```

## Configuration Reference

### Key `_config.yml` Settings

| Setting | Value | Notes |
|---------|-------|-------|
| `lang` | `ko-KR` | Korean language |
| `timezone` | `Asia/Seoul` | Korean timezone |
| `theme_mode` | (empty) | Auto light/dark |
| `comments.active` | `giscus` | GitHub Discussions comments |
| `paginate` | `10` | Posts per page |
| `toc` | `true` | Table of contents enabled |

### Author Configuration

Defined in `_data/author.yml`:
```yaml
gon:
  name: 차영곤
  github: garmede
  url: https://garmede.github.io
```

Reference in posts with `author: gon`

## Deployment

### Automatic (Recommended)

Push to `main` branch triggers GitHub Actions workflow:

1. Checks out code with submodules
2. Sets up Ruby 3.1 with bundler caching
3. Builds Jekyll site
4. Deploys to GitHub Pages

### Manual Deployment

Use the deployment script (runs in CI only):
```bash
./tools/deploy.sh
```

Options:
- `--dry-run`: Build without deploying
- `--config FILE`: Use custom config

## Code Style

Defined in `.editorconfig`:

- **Charset:** UTF-8
- **Indentation:** 2 spaces
- **Line endings:** LF (Unix-style)
- **Trailing whitespace:** Trimmed
- **Final newline:** Required

## Git Workflow

### Branch Strategy

- `main`: Production branch, triggers auto-deployment
- Feature branches: For new content or changes

### Commit Messages

Use Korean or English commit messages. Recent examples:
- `테마 업데이트` (Theme update)
- `초안 추가` (Draft addition)
- `엔진리뷰-유니티 추가` (Engine review - Unity addition)

## Important Notes for AI Assistants

### Do's

- Write content in Korean
- Use WebP format for new images
- Follow existing frontmatter patterns
- Use Chirpy theme's built-in styling classes
- Test with `bundle exec jekyll serve --drafts` before committing

### Don'ts

- Don't change the theme configuration without explicit request
- Don't add new dependencies without discussion
- Don't modify `assets/lib/` (it's a git submodule)
- Don't push directly to `main` for major changes

### Common Tasks

1. **Add new blog post:**
   - Create file in `_posts/` with proper date prefix
   - Add frontmatter with title, categories, tags, image, author
   - Place images in `assets/img/blog/`

2. **Edit existing post:**
   - The `posts-lastmod-hook.rb` plugin auto-updates modification date
   - No manual `last_modified_at` needed

3. **Preview drafts:**
   - Place in `_drafts/` folder
   - Run `bundle exec jekyll serve --drafts`

4. **Update site configuration:**
   - Modify `_config.yml`
   - Restart Jekyll server to apply changes

## Related Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Chirpy Theme Wiki](https://github.com/cotes2020/jekyll-theme-chirpy/wiki)
- [Kramdown Syntax](https://kramdown.gettalong.org/syntax.html)
