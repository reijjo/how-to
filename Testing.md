# Testing
<div id='top' style="font-size: 1.5rem; display: flex; justify-content: space-around;">
	<a href='#frontend'>Frontend</a> |
	<a href='#backend'>Backend</a> |
	<a href='#end-to-end'>End-to-End</a>
</div>

## Frontend
### Setting up (vitest)
Install packages
`bun add -d vitest @testing-library/react jsdom @vitejs/plugin-react @testing-library/jest-dom @testing-library/user-event @types/node`

Create `vitest.setup.ts` file in the root of the frontend folder:
```ts
import "@testing-library/jest-dom";
import { cleanup } from "@testing-library/react";
import { afterEach } from "vitest";

afterEach(() => {
  cleanup();
});
```

Update `vite.config.ts`:

```ts
import react from "@vitejs/plugin-react";
import { defineConfig } from "vitest/config";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  base: "./",
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: ["./vitest.setup.ts"],
  },
});
```

Add types to `tsconfig.app.json` file:

```json
{
  "compilerOptions": {
		...
    "types": ["@testing-library/jest-dom", "node"],
		...
  },
  "include": ["src"]
}
```

And also to `tsconfig.node.json` file:

```json
{
  "compilerOptions": {
		...
    "types": ["node"],
		...
  },
  "include": ["vite.config.ts"]
}
```
### Example test
This is the file `Footer.tsx` to test:
```tsx
export const Footer = () => {
  return (
    <footer>
      <div className="wrapper">
        <p>&copy; 2024 Reijjo</p>
      </div>
    </footer>
  );
};
```

Create `Footer.spec.tsx` file in the same folder where the `Footer.tsx`:
```tsx
import { render, screen } from "@testing-library/react";
import { describe, expect, test } from "vitest";

import { Footer } from "./Footer";

describe("Footer", async () => {
  test("renders Footer", () => {
    render(<Footer />);

    const copyright = screen.getByText(/2024 Reijjo/i);
    expect(copyright).toBeInTheDocument();
  });

  test("adds numbers", () => {
    expect(1 + 1).toBe(2);
  });
});

```

### Running test
Add to package.json:
```json
  "scripts": {
    ...
    "test": "NODE_ENV=test vitest"
  },
```

In the client folder `bun run test` and that's it.

<a href='#top'>Back to top</a>


## Backend
### Setting up (bun:test)
- Install packages `bun add -d supertest @types/supertest`
- Create `tests` folder in the `src` folder -> Create file `app.spec.ts` in the `tests` folder:
```ts
import { describe, expect, test } from "bun:test";

import app from "../app";
import supertest from "supertest";

const api = supertest(app);

describe("APP.TS", () => {
  test("should return 200", async () => {
    const res = await api.get("/");

    expect(res.status).toBe(200);
    expect(res.text).toBe("hello todo");
  });
});

```
- Add test script to `package.json` file:
```json
{
  ...
  "scripts": {
    "dev": "NODE_ENV=development bun --watch run src/index.ts",
    "clean": "rm -rf node_modules .bun bun.lockb **/dist data",
    "clean:db": "rm -rf data",
    "test": "NODE_ENV=test bun test",
    "test:cover": "NODE_ENV=test bun test --coverage"
  },
  ...
}

```
- Run test with `bun run test` and to see the test coverage run `bun run test:cover`



<a href='#top'>Back to top</a>

## End-to-End
<a href='#top'>Back to top</a>

### Setting up
- Create new folder for the End-to-End tests (for example `e2e`)
- Install Playwright with `npm init playwright@latest` command
- Create `tsconfig.json` in the root of the folder:
```ts
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "CommonJS",
    "lib": ["ESNext", "DOM"],
    "moduleResolution": "Node",
    "strict": true,
    "esModuleInterop": true,
    "types": ["node"],
    "resolveJsonModule": true,
    "skipLibCheck": true
  },
  "include": ["**/*.ts"]
}
```
- Add scripts to the `package.json` file:
```json
{
  ...
  "main": "index.js",
  "scripts": {
    "test": "playwright test",
    "test:report": "playwright show-report",
		"test:ui": "playwright test --ui"
  },
  ...
}

```

- Run the example tests with `npm run test` or `npm run test:report`
- Or with my favorite `npm rum test:ui`

### First tests
- Create `tests` folder in the root of the folder
- Modify `playwright.config.ts` file:
```ts
export default defineConfig({
  testDir: "./tests",
  ...
})
```
- Create first test into the `tests` folder (`filename.spec.ts`)
```ts
import { expect, test } from "@playwright/test";

test.beforeEach(async ({ page }) => {
  await page.goto("http://localhost:5173");
});

test.describe("Homepage", () => {
  test("should display header and form", async ({ page }) => {
    const header = page.locator("h1");
    const form = page.locator("form");

    expect(header).toBeVisible();
    expect(form).toBeVisible();
  });
});

```
