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