# Actions for tree-sitter grammars

## setup

### Options

```yaml
depth:
  description: Fetch depth
  default: '2'
submodules:
  description: Initialize submodules
  default: 'false'
node-version:
  description: The NodeJS version
  required: true
```

### Example configuration

See [below](#example-configuration-1)

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
      - uses: tree-sitter-grammars/actions/setup@main
        with:
          node-version: 20
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
script-body:
  description: The JS function body that returns package links
  default: |-
    return this.map(([key, value]) => {
      const name = key.substring(13), version = value.version;
      return `- [${name}@${version}](https://www.npmjs.com/package/${name}/v/${version})`;
    }).join('\n');
```

### Example configuration

```yaml
name: Update

on:
  schedule:
    - cron: '0 0 * * 0'
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

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
