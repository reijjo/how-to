# Express.js with Bun
## Setup
- Create `server` folder in the root of your project folder and run `bun init`
- Create `src` folder and move the `index.ts` file there just to keep stuff cleaner

### Install some dependencies
- `bun add cors express colorette helmet`
- `bun add @types/cors @types/express @types/morgan morgan -d`

### .env file
- Create `.env` file in the root of the `server` folder:
```.env
# Port
PORT = 3001
```

### package.json file
Add scripts to the file so it looks like this:
```json
{
  "name": "server",
  "module": "src/index.ts",
  "type": "module",
  "scripts": {
    "dev": "NODE_ENV=development bun --watch run src/index.ts",
    "clean": "rm -rf node_modules .bun bun.lockb dist"
  },
  "devDependencies": {
		...
	}
}
```
- `bun run dev` runs the server in `watch` mode that is the same as using `nodemon`
- `bun run clean` the name says what it does

### server/src folder
Create `app.ts` file:
```ts
import express from "express";
import morgan from "morgan";
import cors from "cors";
import helmet from "helmet";

const app = express();

const corsOptions = {
	origin: 'http://localhost:5173',
	methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
	credentials: true,
	optionsSuccessStatus: 200
}

app.use(helmet())
app.use(morgan("dev"));
app.use(express.json());
app.use(cors(corsOptions));

app.get("/", (_req, res) => {
  res.send("hello todo");
});

export default app;
```

And `index.ts` file should look like this:
```ts
import * as http from "http";
import { cyanBright, yellowBright, redBright } from "colorette";
import app from "./app";

const PORT = Bun.env.PORT || 3000;
let server = http.createServer(app);

const restartServer = () => {
  server.close(() => {
    server = http.createServer(app);
    startServer();
  });
};

// Error handlers
process.on("uncaughtException", (err) => {
  console.error(redBright("UNCAUGHT EXCEPTION! Restarting..."));
  console.error(err);
  restartServer();
});

process.on("unhandledRejection", (err) => {
  console.error(redBright("UNHANDLED REJECTION! Restarting..."));
  console.error(err);
  restartServer();
});

const startServer = async () => {
  try {
    server.listen(PORT, () => {
      console.log(yellowBright(`ENV = '${Bun.env.NODE_ENV}'`));
      console.log(cyanBright(`Server running on port ${PORT}`));
    });
  } catch (error) {
    console.error(redBright("Error starting server:"), error);
    setTimeout(startServer, 5000);
  }
};

startServer();
```

- Check that server runs with `bun run dev` and go with your browser to `localhost:3000`. It should return `hello todo` text

## Basic structure
### Utils folder
Create `utils` folder inside the `src` folder and add there youre utils files for example `types.ts` where you add your types
- `types.ts`:
```ts
export type TodoContent = {
  id: number;
  content: string;
  done: boolean;
};
```

### Controllers and Routes
Create `controllers` and `routes` folders inside your `src` folder (these are just an example. Another `how-to` file shows how to use the database with these)
- `routes/todoRoute.ts`:
```ts
import {
  allTodos,
  createTodo,
  deleteTodo,
  updateTodo,
} from "../controllers/todoController";
import { todoFinder } from "../middleware/todoFinder";
import express from "express";

const todoRouter = express.Router({ mergeParams: true });

todoRouter.get("/", allTodos);
todoRouter.post("/", createTodo);
todoRouter.patch("/:id", todoFinder, updateTodo);
todoRouter.delete("/:id", todoFinder, deleteTodo);

export default todoRouter;

```
- `controllers/todoController.ts`:
```ts
import { TodoModel } from "../models";
import type { Request, Response } from "express";

//
// GET
// Get all todos
export const allTodos = async (_req: Request, res: Response): Promise<void> => {
  try {
    const todos = await TodoModel.findAll({});
    res.status(200).json({ data: todos, ok: true });
  } catch (error: unknown) {
    console.error("Error fetching all todos", error);
    res
      .status(500)
      .json({ message: "Server error fetching todos.", ok: false });
  }
};

//
// POST
// Create a todo
export const createTodo = async (
  req: Request,
  res: Response,
): Promise<void> => {
  const { content } = req.body;

  if (!content) {
    res
      .status(400)
      .json({ message: "Don't even try to add empty todo.", ok: false });
    return;
  }

  try {
    const todo = await TodoModel.create({ content: content });
    res.status(201).json({ data: todo, ok: true, message: "Todo created." });
  } catch (error: unknown) {
    console.error("Error creating todo", error);
    res.status(500).json({ message: "Server error creating todo.", ok: false });
  }
};

//
// PATCH
// Update todo status
export const updateTodo = async (
  req: Request,
  res: Response,
): Promise<void> => {
  const todo = req.todo as TodoModel;

  try {
    const updatedTodo = await todo.update({
      done: !todo.getDataValue("done"),
    });

    res.status(200).json({
      ok: true,
      message: "Todo updated.",
      data: updatedTodo,
    });
  } catch (error: unknown) {
    console.error("Error updating todo", error);
    res.status(500).json({ message: "Server error updating todo.", ok: false });
  }
};

//
// DELETE
// Delete todo
export const deleteTodo = async (
  req: Request,
  res: Response,
): Promise<void> => {
  const todo = req.todo as TodoModel;

  try {
    await todo.destroy();
    res.status(200).json({ ok: true, message: "Todo deleted." });
  } catch (error: unknown) {
    console.error("Error deleting todo", error);
    res.status(500).json({ message: "Server error deleting todo.", ok: false });
  }
};
```

- Add the route to the `app.ts`:
```ts
...
app.use(cors());

app.use("/api/todos", todoRouter);

export default app;
```

### Middlewares
Create `middleware` folder in your `src` folder
- `errorHandler.ts`:
```ts
import type { NextFunction, Request, Response } from "express";

interface CustomError extends Error {
	statusCode?: number;
}

export const errorHandler = (error: CustomError, req: Request, res: Response, next: NextFunction) => {
	const statusCode = error.statusCode || res.statusCode || 500;

	const errorResponse = {
		success: false,
		timestamp: new Date().toISOString(),
		path: req.path,
		method: req.method,
		message: error.message || 'Internal server error',
		status: statusCode,
		// ...(Bun.env.NODE_ENV === 'development' && { stack: error.stack }),
	}

	console.error(`[Error] ${error.message}`, errorResponse);

	res.status(statusCode).json(errorResponse);
}
```
- `notFoundHandler.ts`:
```ts
import type { Request, Response, NextFunction } from "express";

export const notFoundHandler = (req: Request, res: Response, next: NextFunction) => {
  const error = new Error(`Not Found - ${req.originalUrl}`);
  res.status(404);
  next(error);
};
```

And use these in the `app.ts` file:
```ts
...
app.use(express.json());
app.use(cors(corsOptions));

app.get("/", (_req, res) => {
  res.send("hello todo");
});

app.use(notFoundHandler);
app.use(errorHandler);

export default app;
```
