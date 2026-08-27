---
title: "search"
description: "find anything across posts, notes and talks"
layout: "search"

# Weight 10 puts search last in the bar, after speaking (9).
menu:
  main:
    weight: 10

# Nothing here for a crawler to index — the results are built client-side from
# /index.json, and the pages themselves are already indexed.
sitemap:
  disable: true
_build:
  list: never
  render: always
---
