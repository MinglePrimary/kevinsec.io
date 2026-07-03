---
title: Testing Cover Image
date: 2026-07-03T20:21:00+07:00
lastmod: 2026-07-03T20:21:00+07:00
draft: false
description: ""
cover:
  relative: false
  image: ""
author: KevinSec
ShowToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
---
{{ $src := .Get "src" }}
{{ $alt := .Get "alt" | default "" }}
{{ $width := .Get "width" | default "700" }}
{{ $caption := .Get "caption" | default "" }}

<figure class="ks-image" style="text-align:center;">
  <img src="{{ $src }}"
       alt="{{ $alt }}"
       style="width:{{ $width }}px; max-width:100%; height:auto;">
  {{ with $caption }}
    <figcaption>{{ . }}</figcaption>
  {{ end }}
</figure>

Testing image upload 1.

![](attacker_red.png)

Testing image upload 2.
<img src="attacker_red.png" alt="Attacker Logo" width="220">

Testing image upload 3.
<img src="/images/uploads/KevinSec-logo_Demo.png" width="220">

Testing image upload 3.
{{< image src="attacker_red.png" width="120" alt="Testing banner" caption="Demo image upload" >}}
