# Actions for tree-sitter grammars

## test

### Options

```yaml
lint:
  description: Lint
  default: 'false'
generate:
  description: Verify generated parser
  default: 'false'
test-grammar:
  description: Test the grammar
  default: 'true'
test-library:
  description: Test the rust library
  default: 'false'
examples:
  description: Glob patterns of example files to parse
```

### Example configuration

```yaml
name: CI

on:
  push:
    branches: [master]
    paths:
      - grammar.js
      - src/**
      - bindings/**
      - binding.gyp
  pull_request:
    paths:
      - grammar.js
      - src/**
      - bindings/**
      - binding.gyp

jobs:
  test:
    name: Run tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          cache: npm
      - uses: actions-rust-lang/setup-rust-toolchain@v1.8
      - run: npm ci
      - run: echo "$PWD/node_modules/.bin" >> "$GITHUB_PATH"
      - uses: tree-sitter-grammars/actions/test@main
        with:
          test-library: true
          examples: examples/*
```

## update

### Options

```yaml
parent-name:
  description: The parent language name (for scanner updates)
language-name:
  description: The current language name (for scanner updates)
pr-branch:
  description: The name of the PR branch
  default: update-parser-pr
jq-script:
  description: The jq script that generates the PR body
  default: |-
    .packages | to_entries | del(.[0])[] |
      "- [`\(.key[13:])@\(.value.version)`]" +
      "(https://www.npmjs.com/package/\(.key[13:])/v/\(.value.version))"
```

### Example configuration

```yaml
name: Update

on:
  schedule:
    - cron: '0 0 * * 0'
  workflow_dispatch:

jobs:
  test:
    name: Update dependencies
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          cache: npm
      - uses: tree-sitter-grammars/actions/update@main
        with:
          parent-name: cpp
          language-name: arduino
```

## fuzz

See [tree-sitter-grammars/tree-sitter-fuzz-action](https://github.com/tree-sitter-grammars/tree-sitter-fuzz-action)

## examples

See [tree-sitter-grammars/tree-sitter-examples-action](https://github.com/tree-sitter-grammars/tree-sitter-examples-action)
