# Repository Copilot Instructions

## Documentation conventions

Apply these rules whenever creating or editing documentation in `docs/`:

1. Expand each domain-specific abbreviation at its first occurrence in a document.
   - In English, write the full English term followed by the abbreviation, for example: `Median Absolute Deviation (MAD)`.
   - In Chinese, write the Chinese term followed by the full English term and abbreviation, for example: `中位数绝对偏差（Median Absolute Deviation，MAD）`.
   - Use only the abbreviation after it has been introduced. Do not repeatedly expand it in the same document.
   - Keep terminology and structure synchronized when corresponding English and Chinese documents both exist.

2. Use the repository's Jekyll/kramdown math convention for equations.
   - Set `layout: math-page` in the front matter of every page containing equations.
   - Write inline math with both kramdown delimiters on the same line: `The value is $$x_t$$.`
   - Write display math with the delimiters on separate lines:

     ```markdown
     $$
     z_t = \frac{x_t - \mu}{\sigma}
     $$
     ```

   - Do not write `\(...\)` or `\[...\]` directly in Markdown. kramdown treats those backslashes as escapes; it generates these MathJax delimiters from `$$...$$`.
   - Put literal formulas that should not render inside backticks or fenced code blocks.

Before completing documentation changes, preserve valid YAML front matter, check local links, run `git diff --check`, and, when math or layouts change, run:

```sh
JEKYLL_ENV=production bundle exec jekyll build --source docs --strict_front_matter
```
