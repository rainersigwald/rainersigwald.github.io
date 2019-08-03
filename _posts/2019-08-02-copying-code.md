---
layout: post
title:  "Copying code the right way"
---

Sometimes the most pragmatic way to solve a problem is by copying code--forking a small subset of some other repo into your codebase.
Doing some Git&nbsp;Gymnastics™ can help your future self when you inevitably need to take updates of the code you're copying.

## When to copy

When you can't avoid it.

## Features of a well-done copy

What do you, the future maintainer of some code, want to see when you encounter some code that was copied from another repo?

You want to be able to **make local changes**, which is probably why you forked the code in the first place.

You want to easily **attribute it to its source**, so you can see what's going on upstream. Maybe in the next release they solved your problem, so you can reference via package.

You want to easily **update the local copy** when a relevant change happens upstream.

You want the code to **play nicely in your repo**, fitting into your build system and not causing too many warnings or errors.

Some of these are in tension: if upstream uses tabs instead of spaces, or `snake_case` instead of `camelCase`, you probably _don't_ want to change every line; that would make updating hard. Likewise, if you're going to rewrite the whole thing, just do that, don't fork first.

## How to do it (summary)

## Detailed walkthough
