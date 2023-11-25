---
weight: 10
title: "Website Modifications"
bookFlatSection: true
---

# **Based on UX/UI / bookHidden: true**

# Hugo Framework

{{< hint info >}}
The wiki is built locally using the [Hugo framework](https://gohugo.io/getting-started/installing/). You can run a local environment by following these steps:

- Install the [Hugo Framework](https://gohugo.io/getting-started/installing/)
- Clone this repo
- Run `hugo server --minify` in the root directory
- Open your browser and go to http://localhost:1313 (or as indicated by the Hugo output){{< /hint >}}

# Hugo Book Theme Modifications

I've customized [Hugo Book theme template](https://themes.gohugo.io/hugo-book/) by modifying default settings to offer users more detailed information. I expanded the default three hint options to include three additional key options, alongside color correction. You can still utilize hint shortcode normally.

## READ_ME - Changing Shortcodes

- [This can be customize in this section of the hugo-book/layout/shortcodes/hint-html ]([https://github.com/alex-shpak/hugo-book/blob/master/layouts/shortcodes/hint.html])

Replacing your HTML code with:

- Followed by modifications in your SCSS file:

```scss
.book-hint {
    @each $name, $color in $hint-colors {
    &.#{$name} {
        border-color: $color;
        background-color: rgba($color, 0.1);
    }
    }
}

.hint-title {
@each $name, $color in $hint-colors {
    &.#{$name} {
    color: white;
    border-color: $color;
    background-color: $color;
    border-radius: $border-radius $border-radius 0 0;
    font-weight: 700;
    text-transform: capitalize;
    padding: $padding-4 $padding-16;
    margin: (-$padding-8) (-$padding-16) $padding-8 (-$padding-16 + $padding-4);
    }
}
}

.hint-title svg {
height: 1em;
width: 1em;
vertical-align: middle;
margin-inline-end: $padding-8;
fill: currentColor;
}

.hint-title span {
vertical-align: middle;
}

- In addition to:

```scss
$hint-colors: (
  info: #608ADC, 
  note: #608ADC,
  important: #FFD06B,
  warning: #FF8EAA,
  danger: #FF8EAA, 
  tip: #00AD93,
  example: #A887DE
) !default;

@mixin theme-light {
  --gray-100: #f8f9fa;
  --gray-200: #e9ecef;
  --gray-500: #adb5bd;

  --color-link: #0055bb;
  --color-visited-link: #8440f1;

  --body-background: white;
  --body-font-color: black;

  --icon-filter: none;

  --hint-color-info: #608ADC;
  --hint-color-important: #FFD06B;
  --hint-color-warning: #FF8EAA;
  --hint-color-tip: #00AD93;
  --hint-color-note: #608ADC;
  --hint-color-example: #A887DE;
}
```

```

- Lastly, adapt your manifest.json file accordingly, and can also switch your source icon in .svg format. This way you can also add your personalized icon to your shortcode hint:

```json
{
  "name": "{{ .Site.Title }}",
  "short_name": "{{ .Site.Title }}",
  "start_url": "{{ "/" | relURL }}",
  "scope": "{{ "/" | relURL }}",
  "display": "standalone",
  "background_color": "#000000",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "{{ "/favicon.svg" | relURL }}",
      "sizes": "512x512"
    }
  ]
}
```

{{< hint warning >}}

Adjust your relative path to the hint-icons.svg

```html
{{- $noticeType := .Get 0 -}}
{{- $replaced := .Get 0 -}}
{{- $type := .Get 1 -}}

{{ if ne $type "noTitle" }}
  <blockquote class="book-hint2 {{ $noticeType }}">
    <p class="hint-title {{ $noticeType }}">
      <svg class="book-icon">
        <use href="/svg/hint-icons.svg#{{- $replaced -}}-notice"></use>
      </svg><span>{{ $replaced }}</span></p>
    {{ .Inner | markdownify }}
  </blockquote>
{{ else }}
  <blockquote class="book-hint {{ .Get 0 }}">
    {{ .Inner | markdownify }}
  </blockquote>
{{ end }}
```
{{< hint info >}}

# Developer

- Page created and modified by [Ricardo Chin](https://github.com/roaked)

