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

## Async Logic and Data Fetching
- Create `store/withTypes.ts` file for AsyncThunk:
```ts
import { createAsyncThunk } from "@reduxjs/toolkit";

import type { RootState, AppDispatch } from "./store";

// Create a pre-typed `createAsyncThunk` function
export const createAppAsyncThunk = createAsyncThunk.withTypes<{
	state: RootState,
	dispatch: AppDispatch,
}>();
```

- Update `features/todos/todosSlice.ts` with TodosState:
```ts
...
interface TodosState {
  todos: Todo[];
  status: "idle" | "pending" | "succeeded" | "failed";
  error: string | null;
}

// Updated initial state with the new TodosState interface
const initialState: TodosState = {
  todos: [],
  status: "idle",
  error: null,
};

const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    addTodo: {
      reducer(state, action: PayloadAction<Todo>) {
        state.todos.push(action.payload);
      },
      ...
    },
    updateTodo(state, action: PayloadAction<Todo>) {
      const { id, done } = action.payload;
      const existingTodo = state.todos.find((todo) => todo.id === id); // Finds the correct todo by ID

      ...
    },
    deleteTodo(state, action: PayloadAction<number | string>) {
      const id = action.payload;
      state.todos = state.todos.filter((todo) => todo.id !== id);
    },
  },
});

...

// Helper state selectors
export const findTodos = (state: RootState) => state.todos.todos;
export const findTodoById = (state: RootState, id: number | string) =>
  state.todos.todos.find((todo) => todo.id === id);
export const todosStatus = (state: RootState) => state.todos.status;
export const todosError = (state: RootState) => state.todos.error;

```

- Fetching Data with `createAsyncThunk`. Update `features/todos/todosSlice.ts`:
```ts
import axios from "axios";
...
interface TodosState {
  todos: Todo[];
  status: "idle" | "pending" | "succeeded" | "failed";
  error: string | null;
}

const URL = 'http://localhost:3001'

export const fetchTodos = createAppAsyncThunk("todos/fetchTodos", async () => {
  const response = await axios.get<Todo[]>(`${URL}/api/todos`);
  return response.data;
});

// Updated initial state with the new TodosState interface
const initialState: TodosState = {
  todos: [],
  status: "idle",
  error: null,
};

```

- Add cases to `todosSlice.ts`:
```ts
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    ...
    deleteTodo(state, action: PayloadAction<number | string>) {
      const id = action.payload;
      state.todos = state.todos.filter((todo) => todo.id !== id);
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchTodos.pending, (state) => {
        state.status = "pending";
      })
      .addCase(fetchTodos.fulfilled, (state, action) => {
        state.status = "succeeded";
        state.todos.push(...action.payload);	// Add any fetched todos to the array
      })
			.addCase(fetchTodos.rejected, (state, action) => {
				state.status = "failed";
				state.error = action.error.message ?? 'Unknown error';
			})
  },
});
...
```

- Use the fetchTodos in the `TodoList.tsx` component:
```tsx
import "./TodoList.css";

import { useEffect } from "react";

import {
  fetchTodos,
  findTodos,
  todosStatus,
} from "../../features/todos/todosSlice";
import { useAppDispatch, useAppSelector } from "../../store/hooks";
import { TodoComponent } from "./TodoComponent";

export const TodoList = () => {
  const dispatch = useAppDispatch();
  const todos = useAppSelector(findTodos); // Gets the todos from the Redux store
  const todoStatus = useAppSelector(todosStatus); // Gets the status of the todos from the Redux store

  // To prevent re-renders we start fetching when the status is idle
  useEffect(() => {
    if (todoStatus === "idle") {
      dispatch(fetchTodos());
    }
  }, [dispatch, todoStatus]);

  return (
    <div className="todo-list" data-testid="todo-list">
      {todos.map((todo) => (
        <TodoComponent key={todo.id} todo={todo} />
      ))}
    </div>
  );
};

```

- Double check to prevent re-renders in `todosSlice.ts`:
```ts
...
// Condition double checks that there is no re-renders
export const fetchTodos = createAppAsyncThunk(
  "todos/fetchTodos",
  async () => {
    const response = await axios.get<Todo[]>(`${URL}/api/todos`);
    return response.data;
  },
  {
    condition(_arg, thunkApi) {
      const status = todosStatus(thunkApi.getState());
      if (status !== "idle") {
        return false;
      }
    },
  },
);

```

- Update the fetch to match the backend response type `todosSlice.ts`:
```ts
interface TodoApiResponse {
  data: Todo[];
  ok: boolean;
  message: string;
}

...

// Condition double checks that there is no re-renders
export const fetchTodos = createAppAsyncThunk(
  "todos/fetchTodos",
  async () => {
    const response = await axios.get<TodoApiResponse>(`${URL}/api/todos`);
    return response.data.data;
  },
  {
    condition(_arg, thunkApi) {
      const status = todosStatus(thunkApi.getState());
      if (status !== "idle") {
        return false;
      }
    },
  },
);
```

- Adding the new Todos `todosSlice.ts`:
```ts
...
interface TodoCreateResponse {
  data: Todo;
  ok: boolean;
  message: string;
}

type NewTodo = Pick<Todo, "content">;
...
export const addNewTodo = createAppAsyncThunk(
  "todos/addNewTodo",
  async (initialTodo: NewTodo) => {
    const response = await axios.post<TodoCreateResponse>(
      `${URL}/api/todos`,
      initialTodo,
    );
    return response.data.data;
  },
);
...
// Remove the addTodo from the reducers
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {
    ...
  },
  extraReducers: (builder) => {
    builder
      ...
      .addCase(addNewTodo.fulfilled, (state, action) => {
        state.todos.push(action.payload);
      });
  },
});

export const { updateTodo, deleteTodo } = todosSlice.actions;
...

```

- Update the `Homepage.tsx`:
```tsx
...
export const Homepage = () => {
  const [todo, setTodo] = useState("");
  const [addTodoStatus, setAddTodoStatus] = useState<"idle" | "pending">(
    "idle",
  );
	...
	  const createTodo = async (e: SyntheticEvent) => {
    e.preventDefault();

    if (addTodoStatus === "pending") return;
    if (!todo.trim()) return;

    try {
      setAddTodoStatus("pending");
      await dispatch(addNewTodo({ content: todo })).unwrap(); // Unwrap only resolves the promise if there is no error
      setTodo("");
    } catch (error: unknown) {
      console.log("Error creating todo", error);
    } finally {
      setAddTodoStatus("idle");
    }
  };
	...
```

- And then we also need update and delete thunks `todosSlice.ts`:
```ts
...
type UpdateTodo = Pick<Todo, "id" | "done">;
...
export const updateTodoStatus = createAppAsyncThunk(
  "todos/updateTodoStatus",
  async (todo: UpdateTodo) => {
    const response = await axios.patch<TodoCreateResponse>(
      `${URL}/api/todos/${todo.id}`,
      { done: !todo.done },
    );
    return response.data.data;
  },
);

export const removeTodo = createAppAsyncThunk(
  "todos/removeTodo",
  async (id: number | string) => {
    await axios.delete(`${URL}/api/todos/${id}`);
    return id;
  },
);
...
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {},
  extraReducers: (builder) => {
    builder
		...
 			.addCase(updateTodoStatus.fulfilled, (state, action) => {
        const todo = state.todos.find((todo) => todo.id === action.payload.id);
        if (todo) {
          todo.done = action.payload.done;
        }
      })
      .addCase(removeTodo.fulfilled, (state, action) => {
        state.todos = state.todos.filter((todo) => todo.id !== action.payload);
      });
  },
});
```
- And update `TodoComponent.tsx`:
```tsx
...
export const TodoComponent = ({ todo }: TodoComponentProps) => {
  const [todoStatus, setTodoStatus] = useState<"idle" | "pending">("idle");
  // Finds the todo by ID
  const foundTodo = useAppSelector((state) => findTodoById(state, todo.id));
  const dispatch = useAppDispatch();

  const handleTodoDone = async () => {
    if (todoStatus === "pending") return;
    if (!foundTodo) return;

    try {
      setTodoStatus("pending");
      await dispatch(
        updateTodoStatus({ id: foundTodo.id, done: !foundTodo.done }),
      ).unwrap();
    } catch (error: unknown) {
      console.log("Error updating todo", error);
    } finally {
      setTodoStatus("idle");
    }
  };

  const handleTodoDelete = async () => {
    if (todoStatus === "pending") return;
    if (!foundTodo) return;

    try {
      setTodoStatus("pending");
      await dispatch(removeTodo(foundTodo.id)).unwrap();
    } catch (error: unknown) {
      console.log("Error deleting todo", error);
    } finally {
      setTodoStatus("idle");
    }
  };
	...
```


## RTK Query Basics
- And now lets get rid of the thunks when we got all ready
- Create apiSlice in `features/api/apiSlice.ts`:
```ts
// RTK Query methods
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

import { config } from "../../utils/config";
// Use the `Todo` type from the `types.ts` file
// and the re-export it for ease of use
import { Todo, TodosResponse } from "../../utils/types";

export type { Todo };
const { URL } = config;

// Define single API slice object
export const apiSlice = createApi({
  reducerPath: "api", // The cache reducer expects to be added at `state.api` by default
  baseQuery: fetchBaseQuery({ baseUrl: `${URL}/api` }), // Base URL for all requests
  // Define the endpoints
  endpoints: (builder) => ({
    // The return value is a `Todo[]` array and it takes no arguments
    getTodos: builder.query<Todo[], void>({
      query: () => "/todos", // The endpoint URL
			// We need the transformResponse because our API returns an object with a `data` key
      transformResponse: (response: TodosResponse) => {
        return response.data;
      },
    }),
    getTodoById: builder.query<Todo, number>({
      query: (id) => `/todos/${id}`,
    }),
  }),
});

// Export the auto-generated hooks for the API endpoints
export const { useGetTodosQuery, useGetTodoByIdQuery } = apiSlice;


```

- Update `store/store.ts`:
```ts
import { configureStore } from "@reduxjs/toolkit";

import { apiSlice } from "../features/api/apiSlice";
import todoReducer from "../features/todos/todosSlice";

export const store = configureStore({
  reducer: {
    todos: todoReducer,
    [apiSlice.reducerPath]: apiSlice.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(apiSlice.middleware),
  devTools: import.meta.env.NODE_ENV !== "production",
});

export type AppStore = typeof store;
export type AppDispatch = typeof store.dispatch; // Infer the `AppDispatch` type from the store itself
export type RootState = ReturnType<typeof store.getState>; // Same for the `RootState` type

```

- Clean up `todosSlice.ts`:
```ts
import { createSlice } from "@reduxjs/toolkit";

import { RootState } from "../../store/store";
import { Todo } from "../../utils/types";
import { apiSlice } from "../api/apiSlice";

type TodosState = {
  list: Todo[];
  status: "idle" | "pending" | "succeeded" | "failed";
  error: string | null;
};

// Updated initial state with the new TodosState interface
const initialState: TodosState = {
  list: [],
  status: "idle",
  error: null,
};

// addMatcher handles different actions from API calls
const todosSlice = createSlice({
  name: "todos",
  initialState,
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addMatcher(
        apiSlice.endpoints.getTodos.matchFulfilled,
        (state, action) => {
          state.list = action.payload;
        },
      )
      .addMatcher(
        apiSlice.endpoints.getTodos.matchRejected,
        (state, action) => {
          state.status = "failed";
          state.error = action.error.message ?? null;
        },
      );
  },
});

export default todosSlice.reducer;

// Helper state selectors
export const findTodoById = (state: RootState, id: number | string) =>
  state.todos.list.find((todo) => todo.id === id);

```

- And use the RTK Query in the `TodoList.tsx` element:
```tsx
import "./TodoList.css";

import { useMemo } from "react";

import { useGetTodosQuery } from "../../features/api/apiSlice";
import { TodoComponent } from "./TodoComponent";

export const TodoList = () => {
  const { data: todos = [], isLoading, isError, error } = useGetTodosQuery();

	// useMemo prevents the sorting function from being called on every render
  const sortedTodos = useMemo(() => {
    const sortedTodos = todos.slice();
    sortedTodos.sort((a, b) => Number(a.id) - Number(b.id));

    return sortedTodos;
  }, [todos]);

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>{error.toString()}</div>;

  return (
    <div className="todo-list" data-testid="todo-list">
      {sortedTodos.map((todo) => (
        <TodoComponent key={todo.id} todo={todo} />
      ))}
    </div>
  );
};

```

- Next we need to `create`, `update` and `delete` RTK Queries (mutations) for the other API endpoints in our `apiSlice.ts`:
```ts
...
export const apiSlice = createApi({
  reducerPath: "api", // The cache reducer expects to be added at `state.api` by default
  baseQuery: fetchBaseQuery({ baseUrl: `${URL}/api` }), // Base URL for all requests
  tagTypes: ["Todo"], // Define the tag types for the cache
  // Define the endpoints
  endpoints: (builder) => ({
		...
		addNewTodo: builder.mutation<Todo, Partial<Todo>>({
      query: (content) => ({
        url: "/todos",
        method: "POST",
        body: content,
      }),
      invalidatesTags: ["Todo"], // Tells the RTK Query that cache data is outdated and makes a refetch
    }),
    editTodo: builder.mutation<Todo, Partial<Todo>>({
      query: (todo) => ({
        url: `/todos/${todo.id}`,
        method: "PATCH",
        body: todo,
      }),
      invalidatesTags: ["Todo"], // Tells the RTK Query that cache data is outdated and makes a refetch
    }),
    deleteTodo: builder.mutation<void, number>({
      query: (id) => ({
        url: `/todos/${id}`,
        method: "DELETE",
      }),
      invalidatesTags: ["Todo"],
    }),
  }),
});

// Export the auto-generated hooks for the API endpoints
export const {
  useGetTodosQuery,
  useGetTodoByIdQuery,
  useAddNewTodoMutation,
  useEditTodoMutation,
  useDeleteTodoMutation,
} = apiSlice;

```
- Update `TodoComponent.tsx`:
```tsx
import "./TodoComponent.css";

import {
  useDeleteTodoMutation,
  useEditTodoMutation,
} from "../../features/api/apiSlice";
import { findTodoById } from "../../features/todos/todosSlice";
import { useAppSelector } from "../../store/hooks";
import { Todo } from "../../utils/types";
import { Icon } from "../common/icon/Icon";

interface TodoComponentProps {
  todo: Todo;
}

export const TodoComponent = ({ todo }: TodoComponentProps) => {
  const foundTodo = useAppSelector((state) => findTodoById(state, todo.id));
  const [updateTodo, { isLoading }] = useEditTodoMutation();
  const [deleteTodo] = useDeleteTodoMutation();

  console.log("foundTodo", foundTodo);

  const handleTodoDone = async () => {
    if (!foundTodo) return;

    console.log("update", foundTodo);

    try {
      await updateTodo({ ...foundTodo, done: !foundTodo.done }).unwrap();
    } catch (error: unknown) {
      console.log("Error updating todo", error);
    }
  };

  const handleTodoDelete = async () => {
    if (!foundTodo) return;

    console.log("delete", todo.id);

    try {
      await deleteTodo(Number(foundTodo.id)).unwrap();
    } catch (error: unknown) {
      console.log("Error deleting todo", error);
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
          disabled={isLoading}
        />
        <a className={`todo-content-wrapper ${todo.done ? "todo-done" : ""}`}>
          <p className="todo-content">{todo.content}</p>
        </a>
      </label>
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

- And `Homepage.tsx`:
```tsx
import "./Homepage.css";

import { ChangeEvent, SyntheticEvent, useState } from "react";

import { useAddNewTodoMutation } from "../../features/api/apiSlice";
import { Container, TextInputWithButton } from "../common";
import { TodoList } from "./TodoList";

export const Homepage = () => {
  const [todo, setTodo] = useState("");
  const [addNewTodo, { isLoading }] = useAddNewTodoMutation();

  const handleTodoChange = (e: ChangeEvent<HTMLInputElement>) => {
    setTodo(e.target.value);
  };

  const createTodo = async (e: SyntheticEvent) => {
    e.preventDefault();
    if (!todo.trim()) return;

    try {
      await addNewTodo({ content: todo }).unwrap();
      setTodo("");
    } catch (error: unknown) {
      console.log("Error creating todo", error);
    }
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
            disabled={isLoading}
          />
          <TodoList />
        </Container>
      </div>
    </main>
  );
};

```

- Update the tags in `apiSlice.ts` to minimize the backend usage:
```ts
export const apiSlice = createApi({
  reducerPath: "api", // The cache reducer expects to be added at `state.api` by default
  baseQuery: fetchBaseQuery({ baseUrl: `${URL}/api` }), // Base URL for all requests
  tagTypes: ["Todo"], // Define the tag types for the cache
  // Define the endpoints
  endpoints: (builder) => ({
    // The return value is a `Todo[]` array and it takes no arguments
    getTodos: builder.query<Todo[], void>({
      query: () => "/todos", // The endpoint URL
      providesTags: (result = []) => [
        "Todo",
        ...result.map(({ id }) => ({ type: "Todo", id }) as const), // Add tags for each todo item
      ],
      // We need the transformResponse because our API returns an object with a `data` key
      transformResponse: (response: TodosResponse) => {
        return response.data;
      },
    }),
    getTodoById: builder.query<Todo, number>({
      query: (id) => `/todos/${id}`,
      providesTags: (_result, _error, arg) => [{ type: "Todo", id: arg }], // Minimizes the backend usage
    }),
    addNewTodo: builder.mutation<Todo, Partial<Todo>>({
      ...
      invalidatesTags: ["Todo"], // Tells the RTK Query that cache data is outdated and makes a refetch
    }),
    editTodo: builder.mutation<Todo, Partial<Todo>>({
      ...
      invalidatesTags: (_result, _error, { id }) => [{ type: "Todo", id }],
    }),
    deleteTodo: builder.mutation<void, number>({
      ...
      invalidatesTags: (_result, _error, id) => [{ type: "Todo", id }],
    }),
  }),
});
```