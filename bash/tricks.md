# Find recursive

Check for appearances (case insensitive):

```bash
grep -r -E -i "(my-component|my_component|mycomponent)" .
```

# Search/replace

Replace respecting casing:

```bash
find . -type f ! \( -name '*.gif' -o -name '*.gpg' -o -path '*/\.git/*' \) -exec sed -i '' -e 's/MyComponent/FlowLayout/g' {} +
```
