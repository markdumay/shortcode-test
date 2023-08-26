# shortcode-test

Test for embedded Hugo shortcodes.

## Introduction

Hugo has the option to embed shortcodes. Embedded shortcodes have access to their parent using the `.Parent` variable. The parent has no knowledge of its children, but can access its inner content (which includes any *rendered* children) using the `.Inner` variable.

For example, let's consider the following shortcodes:

**`layouts/partials/inner.html`**

```html
<p>{{ .Inner | .Page.RenderString }}</p>
```

**`layouts/partials/outer.html`**

```html
<div>
    {{ .Inner }}
</div>
```

Calling these shortcodes from within a regular page would generate the following HTML output when Goldmark uses safe rendering (omitting HTML input from the output):

**Markdown content**

```go-template
{{< outer >}}
    {{< inner >}}Plain content is fine{{< /inner >}}
    {{< inner >}}## Markdown content is fine{{< /inner >}}
    {{< inner >}}<span>HTML content</span> is rendered as plain text{{< /inner >}}
{{< /outer >}}
```

**HTML output**

```html   
<p>Plain content is fine</p>
<p><h2 id="markdown-content-is-fine">Markdown content is fine</h2>
</p>
<p><!-- raw HTML omitted -->HTML content<!-- raw HTML omitted --> is rendered as plain text</p>
```

## Challenge

The approach of the `outer` shortcode is unsafe, as the call to `{{ .Inner }}` outputs *any* content to the final HTML page. For example, the following unsafe HTML is rendered to the output, despite Goldmark's safe mode.

**Unsafe Markdown content**

```go-template
{{< outer >}}
    {{< inner >}}Plain content is fine{{< /inner >}}
    {{< inner >}}## Markdown content is fine{{< /inner >}}
    {{< inner >}}<span>HTML content</span> is rendered as plain text{{< /inner >}}
    <mark>This unsafe code is not omitted</mark>
{{< /outer >}}
```

**Unssafe HTML output**

```html   
<p>Plain content is fine</p>
<p><h2 id="markdown-content-is-fine">Markdown content is fine</h2>
</p>
<p><!-- raw HTML omitted -->HTML content<!-- raw HTML omitted --> is rendered as plain text</p>
<mark>This unsafe code is not omitted</mark>
```

## Possible workaround

As the children are processed before the parent, and the parent has simply no knowledge of any children, we cannot use `{{ .Inner | .Page.RenderString }}` for `outer.html`. The variable `.Inner` contains generated HTML output of the various calls to `inner.html`. However, we cannot trust the `.Inner` content of `outer.html` as demonstrated earlier.

Instead, a possible workaround could be to be pass any generated output as scratch variable from `inner.html` to its parent (if any).  The parent would consider that scratch variable as safe and generate that output. Any other (remaining) input can be ignored.

## Implementation

This repository contains a working demo of the workaround. It is based on [Hugo's quick start](https://gohugo.io/getting-started/quick-start/).
