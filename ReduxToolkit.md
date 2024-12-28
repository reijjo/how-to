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
### Create todo reducer
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

## Usage
- Get the todos from the Redux store `client/src/components/homepage/todoList.tsx`
```tsx
import "./TodoList.css";

import { useAppSelector } from "../../store/hooks";
import { Todo } from "../../utils/types";
import { TodoComponent } from "./TodoComponent";

export const TodoList = ({ list, updateTodo, deleteTodo }: TodoListProps) => {
  const todos = useAppSelector((state) => state.todos); // Gets the todos from the Redux store

  return (
    <div className="todo-list" data-testid="todo-list">
      {todos.map((todo) => (
        <TodoComponent
          key={todo.id}
          todo={todo}
        />
      ))}
    </div>
  );
};
```

- Create addTodo reducers in todosSlice `features/todos/todosSlice.ts`:
```ts
import { PayloadAction, createSlice } from "@reduxjs/toolkit";
...
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
		// The type of action.payload will be Todo object
    addTodo(state, action: PayloadAction<Todo>) {
      state.push(action.payload);
    },
  },
});

// Export the action creator with the same name
export const { addTodo } = todosSlice.actions;
```

- Make a createTodo function in `Homepage.tsx`:
```tsx
import "./Homepage.css";

import { ChangeEvent, SyntheticEvent, useState } from "react";

// Random ID generator
import { useDispatch } from "react-redux";

import { addTodo } from "../../features/todos/todosSlice";
import { Container, TextInputWithButton } from "../common";
import { TodoList } from "./TodoList";

export const Homepage = () => {
  const [todo, setTodo] = useState("");

  const dispatch = useDispatch();

  const handleTodoChange = (e: ChangeEvent<HTMLInputElement>) => {
    setTodo(e.target.value);
  };

  const createTodo = async (e: SyntheticEvent) => {
    e.preventDefault();

    dispatch(addTodo(todo));
    setTodo("");
  };

  return (
    <main className="wrapper">
      <div className="align-center">
        <h1>To-do list</h1>
        <Container width="min(100%, 600px)" backgroundColor="transparent">
          <TextInputWithButton
            label="create todo"
            name="todo"
            id="todo"
            placeholder="What to do..."
            value={todo}
            onChange={handleTodoChange}
            width="75%"
            buttonText="add"
            onClick={createTodo}
          />
          <TodoList />
        </Container>
      </div>
    </main>
  );
};


```

- Add updateTodo reducer to `features/todos/todosSlice.ts`:
```ts
...
const todosSlice = createSlice({
  ...
  reducers: {
    addTodo(state, action: PayloadAction<Todo>) {
      state.push(action.payload);
    },
		updateTodo(state, action: PayloadAction<Todo>) {
			const { id, done } = action.payload;
			const existingTodo = state.find((todo) => todo.id === id);

			if (existingTodo) {
				existingTodo.done = done;
			}
		}
  },
});

// Export the action creator with the same name
export const { addTodo, updateTodo } = todosSlice.actions;
...
```

- And add the updateTodo to the `TodoComponent.tsx`:
```tsx
import "./TodoComponent.css";

import { updateTodo } from "../../features/todos/todosSlice";
import { useAppDispatch, useAppSelector } from "../../store/hooks";
import { Todo } from "../../utils/types";
import { Icon } from "../common/icon/Icon";

interface TodoComponentProps {
  todo: Todo;
}

export const TodoComponent = ({
  todo,
}: TodoComponentProps) => {
  // Finds the todo by ID
  const foundTodo = useAppSelector((state) =>
    state.todos.find((t) => t.id === todo.id),
  );
  const dispatch = useAppDispatch();

  const handleTodoDone = () => {
    // If the foundTodo exists, dispatch the updateTodo action
    if (foundTodo) {
      dispatch(
        updateTodo({
          ...foundTodo,
          done: !foundTodo.done,
        }),
      );
    }
  };

  return (
    <div className="single-todo">
      <label className="single-todo-label">
        <input
          type="checkbox"
          className="todo-checkbox"
          checked={todo.done}
          onChange={handleTodoDone}
          name="todo-checkbox"
        />
        <a className={`todo-content-wrapper ${todo.done ? "todo-done" : ""}`}>
          <p className="todo-content">{todo.content}</p>
        </a>
      </label>
      <button
        className="todo-delete"
        disabled={!todo.done}
      >
        <Icon name="trash" strokeWidth={2} />
      </button>
    </div>
  );
};


```

- Let's add a prepare to the addTodo reducer so the reducer is more stable `todosSlice.ts`:
```ts
...
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    addTodo: {
      reducer(state, action: PayloadAction<Todo>) {
        state.push(action.payload);
      },
      // Now we dont need to pass the ID from the component
      prepare(content: string) {
        return {
          payload: {
            id: nanoid(),
            content,
            done: false,
          },
        };
      },
    },
...
  },
});
...
```

And update the `Homepage.tsx`
```tsx
...
  const createTodo = async (e: SyntheticEvent) => {
    e.preventDefault();

    dispatch(addTodo(todo));
    setTodo("");
  };
...
```

- Add helper state selectors to `todosSlice.ts`:
```ts
import { RootState } from "../../store/store";
...
// Export the action creator with the same name
export const { addTodo, updateTodo } = todosSlice.actions;

export default todosSlice.reducer;

// Helper state selectors
export const findTodos = (state: RootState) => state.todos;
export const findTodoById = (state: RootState, id: number | string) =>
  state.todos.find((todo) => todo.id === id);
```

- Now we can use those in `TodoList.tsx`:
```tsx
...
import { findTodos } from "../../features/todos/todosSlice";
...
export const TodoList = () => {
  const todos = useAppSelector(findTodos); // Gets the todos from the Redux store
...
};
```

And in `TodoComponent.tsx`:
```tsx
...
import { findTodoById, updateTodo } from "../../features/todos/todosSlice";
...
export const TodoComponent = ({ todo }: TodoComponentProps) => {
  // Finds the todo by ID
  const foundTodo = useAppSelector((state) => findTodoById(state, todo.id));
  ...
};

```

- We also need deleteTodo reducer in `todosSlice.ts`:
```ts
...
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    ...
  	deleteTodo(state, action: PayloadAction<number | string>) {
      const id = action.payload;
      const todoToDelete = state.find((todo) => todo.id === id);

      if (todoToDelete) {
        return state.filter((todo) => todo.id !== id);
      }
    },
  },
});

// Export the action creator with the same name
export const { addTodo, updateTodo, deleteTodo } = todosSlice.actions;
...
```

- And add the delete function in `TodoComponent.tsx`:
```tsx
...
import {
  deleteTodo,
  findTodoById,
  updateTodo,
} from "../../features/todos/todosSlice";
...
export const TodoComponent = ({ todo }: TodoComponentProps) => {
  // Finds the todo by ID
  const foundTodo = useAppSelector((state) => findTodoById(state, todo.id));
  const dispatch = useAppDispatch();
	...
  const handleTodoDelete = () => {
    // If the foundTodo exists, dispatch the deleteTodo action
    if (foundTodo) {
      dispatch(deleteTodo(foundTodo.id));
    }
  };

  return (
    <div className="single-todo">
      ...
      <button
        className="todo-delete"
        disabled={!todo.done}
        onClick={handleTodoDelete}
      >
        <Icon name="trash" strokeWidth={2} />
      </button>
    </div>
  );
};

```