# GitHub Actions: Reusable Workflows for Monorepos

Monorepos are great for managing multiple related projects under one roof, but CI/CD pipelines can quickly become messy if every package duplicates the same workflows. Thankfully, GitHub Actions supports **reusable workflows** that let you share CI steps across projects while keeping your configuration clean and maintainable.

---

## Why Reusable Workflows?

* **Consistency**: Ensure all packages follow the same build, lint, and test standards.
* **Maintainability**: Update a workflow in one place, and all consumers benefit.
* **Scalability**: Onboard new packages faster by reusing proven workflows.
* **DRY Principle**: Avoid duplication of complex YAML logic across repositories.

---

## Example Repository Structure

```
my-monorepo/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── reusable-test.yml
├── packages/
│   ├── api/
│   └── web/
```

* `ci.yml`: Entry workflow triggered on pull requests.
* `reusable-test.yml`: Defines test steps that other workflows can call.

---

## Defining a Reusable Workflow

Create `.github/workflows/reusable-test.yml`:

```yaml
name: Reusable Test Workflow

on:
  workflow_call:
    inputs:
      package:
        required: true
        type: string

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
      - name: Install dependencies
        run: npm install --workspace ${{ inputs.package }}
      - name: Run tests
        run: npm test --workspace ${{ inputs.package }}
```

This workflow can now be reused by other workflows inside the monorepo.

---

## Calling the Reusable Workflow

Inside `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
    paths:
      - 'packages/**'

jobs:
  api-tests:
    uses: ./.github/workflows/reusable-test.yml
    with:
      package: packages/api

  web-tests:
    uses: ./.github/workflows/reusable-test.yml
    with:
      package: packages/web
```

This way, each package’s CI pipeline stays minimal and consistent.

---

## Tips for Success

* Use **matrix strategies** to test multiple Node.js versions or environments.
* Share caching logic in the reusable workflow for faster builds.
* Keep workflows modular (e.g., separate `lint`, `test`, `deploy`).
* Document the inputs so teammates know how to consume workflows.

---

## Next Steps

* Explore GitHub’s [workflow\_call documentation](https://docs.github.com/en/actions/using-workflows/reusing-workflows) for more advanced use cases.
* Consider publishing reusable workflows to a central repo if multiple monorepos need them.

---

✅ With reusable workflows, your monorepo CI/CD becomes clean, scalable, and much easier to maintain.