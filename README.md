# Twixio Docs

## Prerequisites

- [Bun](https://bun.sh/) v1.0+  (or Node.js v18+)

## Local Development

```bash
# Install dependencies
bun install

# Start dev server (hot-reload at http://localhost:5173)
bun run docs:dev
```

## Build & Preview

```bash
# Build static output to docs/.vitepress/dist
bun run docs:build

# Preview the production build locally
bun run docs:preview
```

## Adding Content

- Drop `.md` files into the appropriate `docs/` subdirectory.
- Update the corresponding `sidebar.json5` for that section to include the new page.
- Navigation is configured in `docs/.vitepress/config.mjs`.

## Deployment

The build output is a static site (`docs/.vitepress/dist/`) that can be served from any static host or a reverse proxy like Nginx.

### Static hosting (e.g. Vercel, Netlify, GitHub Pages)

Point the host's build command to `bun run docs:build` and the publish directory to `docs/.vitepress/dist`.

### Self-hosted with Nginx

1. Build the site:

   ```bash
   bun run docs:build
   ```

2. Copy the output to your web root:

   ```bash
   sudo cp -r docs/.vitepress/dist /var/www/twixio-docs
   ```

3. Configure Nginx to serve the directory and forward unknown paths to `index.html` (for clean URLs):

   ```nginx
   server {
       listen 80;
       server_name docs.twixio.com;

       root /var/www/twixio-docs;
       index index.html;

       location / {
           try_files $uri $uri/ /index.html;
       }
   }
   ```

4. Reload Nginx:

   ```bash
   sudo nginx -t && sudo systemctl reload nginx
   ```

### CI/CD (GitHub Actions example)

```yaml
name: Deploy Docs

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: oven-sh/setup-bun@v2
        with:
          bun-version: latest

      - run: bun install
      - run: bun run docs:build

      # Upload dist/ to your server or static host here
```
