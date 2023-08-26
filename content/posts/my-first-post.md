---
title: "My First Post"
date: 2023-08-25T10:57:11+02:00
---

## Unsafe

{{< outer >}}
    {{< inner >}}Plain content is fine{{< /inner >}}
    {{< inner >}}## Markdown content is fine{{< /inner >}}
    {{< inner >}}<span>HTML content</span> is rendered as plain text{{< /inner >}}
    <mark>This unsafe code is not omitted</mark>
{{< /outer >}}

## Safe

{{< outer-safe>}}
    {{< inner-safe >}}Plain content is fine{{< /inner-safe >}}
    {{< inner-safe >}}## Markdown content is fine{{< /inner-safe >}}
    {{< inner-safe >}}<span>HTML content</span> is rendered as plain text{{< /inner-safe >}}
    <mark>This unsafe code is ignored</mark>
{{< /outer-safe >}}
