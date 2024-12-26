# Redux Toolkit (RTK)

## Get Started
- Install dependencies `npm install @reduxjs/toolkit react-redux`
- Create folder `store` in the `client/src` folder
- `client/src/store/store.ts`file:
```ts
import { configureStore } from "@reduxjs/toolkit";

import todosReducer from "../features/todos/todosSlice";

export const store = configureStore({
  reducer: {
    todos: todosReducer, // We make this a bit later so no worries
  },
});

export type AppStore = typeof store;
// Infer the `AppDispatch` type from the store itself
export type AppDispatch = typeof store.dispatch;
// Same for the `RootState` type
export type RootState = ReturnType<typeof store.getState>;

```
- `client/src/store/hooks.ts`file:
```ts
// This file serves as a central hub for re-exporting pre-typed Redux hooks.
import { useDispatch, useSelector } from 'react-redux'
import type { AppDispatch, RootState } from './store'

// Use throughout your app instead of plain `useDispatch` and `useSelector`
export const useAppDispatch = useDispatch.withTypes<AppDispatch>()
export const useAppSelector = useSelector.withTypes<RootState>()
```

- Provide the store in the `main.tsx` file:
```tsx
import "./index.css";

import { createRoot } from "react-dom/client";
import { Provider } from "react-redux";

import { App } from "./App.tsx";
import { store } from "./store/store.ts";

createRoot(document.getElementById("root")!).render(
  <Provider store={store}>
    <App />
  </Provider>,
);

```
## Create todo reducer
- Todo type `client/src/utils/types.ts`
```ts
export interface Todo {
  id: number;
  content: string;
  done: boolean;
}
```

- Create reducer `client/src/features/todos/todosSlice.ts`:
```ts
import { createSlice } from "@reduxjs/toolkit";

import { Todo } from "../../utils/types";

// Empty array as an initial state
const initialState: Todo[] = [];

const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {},
});

export default todosSlice.reducer;
```