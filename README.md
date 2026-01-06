# 🎲 BGG Portfolio Generator Action

**GitHub Action to generate a static portfolio website from a [BoardGameGeek](https://boardgamegeek.com) account.**

This action installs and runs [`jan-wennrich/bgg-portfolio-generator`](https://github.com/JanWennrich/BGG-Portfolio-Generator) to build a personal board game portfolio site directly from your BoardGameGeek collection.

## 📦 Inputs

| Name           | Description                                               | Required |
|----------------|-----------------------------------------------------------|----------|
| `bgg_username` | The BoardGameGeek username to generate the portfolio for. | ✅ Yes    |
| `bgg_password` | The password of the BoardGameGeek account to authenticate with. | ❌ No*    |
| `bgg_token` | A BoardGameGeek API access token to authenticate with. | ❌ No*    |

_* Either `bgg_password` or `bgg_token` is required_

Information about the authentication methods can be found in the ["BGG Portfolio Generator" repository](https://github.com/JanWennrich/bgg-portfolio-generator?tab=readme-ov-file#-authentication).


## 🚀 Usage Example

Here's an example on how to use this action to periodically generate a BoardGameGeek portoflio and publish it to GitHub pages.  
_(Before you copy this example, make sure your repository has [GitHub Pages enabled](https://docs.github.com/en/pages/quickstart) and a `bgg_password` secret configured)_

```yaml
name: Generate & Publish BGG Portfolio

on:
  # Run on push to "main"
  push:
    branches: [main]
  # Run every night at 04:00
  schedule:
    - cron: "0 4 * * *" 
  # Allow manual execution
  workflow_dispatch:

jobs:
  # Generate HTML
  build:
    runs-on: ubuntu-latest
    steps:
      # Generate portfolio
      - uses: JanWennrich/BGG-Portfolio-Generator-Action@main
        with:
          bgg_username: Klabauterjan
          bgg_password: ${{ secrets.BGG_PASSWORD }}
      # Store portfolio HTML as a pages artifact
      - name: Upload Pages Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "public"

  # Deploy to GitHub Pages
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## ⚙️ Installation Details

Under the hood, this action:

1. Sets up PHP 8.3.
2. Installs [`jan-wennrich/bgg-portfolio-generator`](https://packagist.org/packages/jan-wennrich/bgg-portfolio-generator) via Composer.
3. Runs the `bgg-portfolio-generator.php` command for the given BGG username to generate a static portfolio website in `public/`


## 📝 Example Output

The output is a static HTML site listing your BGG games with cover images, ratings, and more.

See a live example at:
📍 [https://janwennrich.github.io/boardgames/](https://janwennrich.github.io/boardgames/)


## 📄 License

GPL-3 or later — use it freely and build awesome board game portfolios!
