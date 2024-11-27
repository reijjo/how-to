# Testing
<div id='top' style="font-size: 1.5rem; display: flex; justify-content: space-around;">
	<a href='#frontend'>Frontend</a> |
	<a href='#backend'>Backend</a> |
	<a href='#end-to-end'>End-to-End</a>
</div>

## Frontend
### Setting up
Install packages
`bun add -d vitest @testing-library/react jsdom @vitejs/plugin-react @testing-library/jest-dom @types/node`

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

## Backend
<a href='#top'>Back to top</a>

## End-to-End
<a href='#top'>Back to top</a>