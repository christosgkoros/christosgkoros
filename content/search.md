---
title: "search"
description: "find anything across posts, notes and talks"
layout: "search"

# The nav entry is defined in config.toml, not here. Hugo 0.123 stopped
# registering front-matter menu entries for pages with `_build.list: never`,
# so this page built but was linked from nowhere — the config menu does not
# depend on page-collection semantics and behaves the same on both versions.
#
# Nothing here for a crawler to index: results are built client-side from
# /index.json, and the pages themselves are already indexed. `list: never`
# is what keeps it out of the sitemap.
_build:
  list: never
  render: always
---
