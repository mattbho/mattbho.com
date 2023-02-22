---
title: Meaningful Git Commits
date: '2023-02-21'
tags: ['tips']
draft: false
summary: How to write git commits that are meaningful and provide meaningful context to your changes.
images: []
layout: PostLayout
canonicalUrl:
---

# Meaningful Git Commits

Earlier today, I had a friend ask me how they should structure their commits since they were working on a take home assignment for a front-end role.
Although I'm not an expert on the subject, I do follow a guideline that is provided by a wonderful website called Conventional Commits.

I'll give you a quick overview as to what your commit's should look like, however I strongly recommend reading more [here](https://conventionalcommits.org).

Writing commits in an organized and meaningful way allows others to understand your changes quickly, boosting productivity. It also allows you to create automated tooling around your changes.

# Example Commit Message

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]

```

The above is an example template of how to write your commits. At Boulevard, my commits would normally look like this.

```
feat(COPS-122): add giftcard history modal

Businesses can now look through the history of gift card transactions via a modal table.

```

Here you can see a clear description of the committed change, and the tag for the Kanban board ticket. That way you can reference the acceptance criteria of the feature as well.

# Types of Commits

A common list (that I use) of commit types include the following:

1. **fix** - A commit that patches a bug in your codebase.
2. **feat** - A commit that adds a new feature to your codebase.
3. **chore** - Grunt task. Nothing a user would see, and nothing that would affect them.
4. **refactor** - Refactoring of existing feature.
