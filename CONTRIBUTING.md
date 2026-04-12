
# Contributing to Throne Documentation

We welcome all contributions to improve the documentation.

## How to Contribute

1. **Fork the repository** to your own GitHub account.
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/your-username/throneproj.github.io.git
   ```
3. **Create a new branch** for your changes:
   ```bash
   git checkout -b docs-improvement
   ```
4. **Make your changes** and verify them locally using `zola serve`.
5. **Commit and push** your changes to your fork:
   ```bash
   git add .
   git commit -m "docs: description of your changes"
   git push origin docs-improvement
   ```
6. **Open a Pull Request** from your fork's branch to the original repository's `main` branch.

## Translation Guidelines

This project supports multiple languages using file suffixes:
* `filename.md` — English (Source)
* `filename.ru.md` — Russian
* `filename.zh.md` — Chinese
* `filename.fa.md` — Persian

When adding a translation, please ensure it stays consistent with the English source file.

## Content Style

* Keep instructions concise and technical.
* Use code blocks for commands and file paths.
* If adding a new section, update the `weight` in the file's front matter to maintain correct ordering.
```
