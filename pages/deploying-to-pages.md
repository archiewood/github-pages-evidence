---
title: Deploying to GitHub Pages
---

## Add a basePath to evidence.config.yaml

```yaml
deployment:
  basePath: /github-pages-evidence
```

## Update the build command in package.json

```json
"build": "EVIDENCE_BUILD_DIR=./build/github-pages-evidence evidence build"
```

## Add a workflow to deploy to GitHub Pages

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: "main"

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Install Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install dependencies
        run: npm install

      - name: build
        env:
          BASE_PATH: "/${{ github.event.repository.name }}"
        run: |
          npm run sources
          npm run build

      - name: Upload Artifacts
        uses: actions/upload-pages-artifact@v3
        with:
          # this should match the `pages` option in your adapter-static options
          path: "build/github-pages-evidence"

  deploy:
    needs: build
    runs-on: ubuntu-latest

    permissions:
      pages: write
      id-token: write

    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    steps:
      - name: Deploy
        id: deployment
        uses: actions/deploy-pages@v4
```
