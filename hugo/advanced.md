# Secondary content implementation

Within a template we can render content from pages other than the primary page:

`template.html`
```html
{{ with .Site.GetPage "page" "otherPage" }}
  {{ .Content }}
{{ end }}
```

## Partial implementation

If we name secondary content using the `content.partb.md` convention (where _partb_ is the arbitrary id of the additional content), we can buid a partial to do the lookup for us:

`template.html`
```html
{{ partial "sub" (dict "name" "partb" "context" . ) }}
```

`layouts/partials/sub.html`
```html
{{ with .context.Site.GetPage "page" ( printf "%s.%s" ( cond (eq .context.File.ContentBaseName "_index") "index" .context.File.ContentBaseName ) .name) }}
  {{ .Content }}
{{ end }}
```

Note: For the index page we need to replace `_index` with `index`, otherwise Hugo will complain.

## Shortcode implementation

With a _shortcode_ we can include secondary content right from the primary content file:

`main.md`
```markdown
{{< sub partb >}}
```

`layouts/shortcodes/sub.html`
```html
{{ partial "sub" (dict "name" ( index .Params 0 ) "context" .Page ) }}
```

In this case we leverage the previously created `sub` partial.
