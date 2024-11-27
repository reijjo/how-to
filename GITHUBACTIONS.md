# GitHub Actions (CI/CD pipeline)

## Create workflow

- Create folder `.github/workflows` in the root of your project
- Make a `tests.yml` file

```yml
name: Tests

on:
  push:
    branches:
      - main
  pull_request:
    branches: [main]
    types: [opened, synchronize]

jobs:
  frontend-tests:
    name: Frontend Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: lts/*
      - name: Install Frontend Dependencies
        run: cd client && npm install
      - name: Run Frontend Tests
        run: cd client && npm run test

  backend-tests:
    name: Backend Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: lts/*
      - name: Install Backend Dependencies
        run: cd client && npm install
      - name: Run Backend Tests
        run: |
          cd server
          node -e "console.log('Backend test placeholder: All systems go!')"

  e2e-tests:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:17
        ports:
          - "5433:5432"
        env:
          POSTGRES_USER: tester
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test_todos
    env:
      DATABASE_URL: postgres://tester:test@localhost:5433/test_todos
      CICD_URL: http://localhost:3000
      PORT: 3000
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v1
        with:
          bun-version: latest
      - name: Install e2e-test Dependencies
        run: cd e2e-tests && npm install

      - name: Install Playwright Browsers
        run: npx playwright install --with-deps

      - name: Build and Start App
        run: |
          cd client
          bun install

          cd ../server
          bun install
          bun run build
          bun run test:cicd &
          sleep 5

      - name: Run e2e tests
        run: |
          cd e2e-tests
          bun run test:cicd --reporter=html
      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: playwright-report
          path: e2e-tests/playwright-report/
          retention-days: 30

```

This requires that you have some tests done and installed playwright etc.

## Protect the branch in GitHub

- Go to your branch settings -> Branches -> Add rule
- _Branch name pattern_: `main`
- Check these boxes on _Protect matching branches_ -> `Require a pull request before merging`, `Require approvals`, `Require status checks to pass before merging`, `Require branches to be up to date before merging`
- Search for `Backend Tests`, `Frontend Tests`, `e2e-tests` for the status checks and Save changes
- Create `pre-push` file in `.git/hooks` folder in the root of your repository:

```
#!/bin/bash
protected_branch="main"
current_branch=$(git rev-parse --abbrev-ref HEAD)

if [ "$current_branch" = "$protected_branch" ]; then
  echo "🚨 Direct pushes to '$protected_branch' are not allowed! Please create a pull request."
  exit 1
fi
```

- `chmod +x .git/hooks/pre-push` to make it executable
  This makes sure that you can't push directly in the main branch.

- Also create `pre-commit` file in the `.git/hooks` folder in the root of your repository:

```
#!/bin/bash

protected_branch="main"
current_branch=$(git rev-parse --abbrev-ref HEAD)

if [ "$current_branch" = "$protected_branch" ]; then
  if ! git diff --cached --quiet; then
    echo "🚨 Staged changes are not allowed on the '$protected_branch' branch!"
    echo "Unstaging files..."
    # Unstage all files
    git reset
    exit 1
  fi
fi


```

- `chmod +x .git/hooks/pre-commit` to make it executable
  This prevents commiting files on the main branch
