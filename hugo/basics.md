# Summaries

Use `<!--more-->` inside your content files to explicitily define the end of the summary paragraphs.

`content.md`
```markdown
`td;lr` content
<!--more-->
…long content…
```

To generate a list of the first 10 summaries:

`template.html`
```html
{{ range first 10 (where site.RegularPages ".Section" "post") }}
    <article>
      <!-- this <div> includes the title summary -->
      <div>
        <h2><a href="{{ .RelPermalink }}">{{ .Title }}</a></h2>
        {{ .Summary }}
      </div>
      {{ if .Truncated }}
      <!-- This <div> includes a read more link, but only if the summary is truncated... -->
      <div>
        <a href="{{ .RelPermalink }}">Read More…</a>
      </div>
      {{ end }}
    </article>
{{ end }}
```
