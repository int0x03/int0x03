# Experiments

Use this directory for small, runnable scripts that validate concepts described in
`docs/`.

## Conventions

- Keep each experiment self-contained and give it a descriptive name.
- Add a short header that states its purpose, prerequisites, and expected result.
- Prefer deterministic inputs and assertions over output that requires manual inspection.
- Document extra dependencies next to the experiment or in the repository dependency manifest.
- Never commit credentials, private data, generated artifacts, or large datasets.

Run scripts from the repository root unless an experiment says otherwise. For example:

```sh
python3 scripts/example.py
# or
bash scripts/example.sh
```
