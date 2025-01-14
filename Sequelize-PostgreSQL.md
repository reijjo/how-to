# Using Sequelize with PostgreSQL
## Database connection
- Install some dependencies `bun add pg pg-hstore sequelize`
- Create `utils/config.ts` file for your .env variables:
```ts
const envVars = [
	"PORT",
  "DB_NAME",
  "DB_PORT",
  "DB_HOST",
	"DB_MAINTENANCE",
  "POSTGRES_USER",
  "POSTGRES_PASSWORD",
	"PGADMIN_DEFAULT_EMAIL",
	"PGADMING_DEFAULT_PASSWORD",
	"PG_PORT"
] as const;

// Validates that env variables exists
for (const varName of envVars) {
	if (!Bun.env[varName]) {
		throw new Error(`Missing env variable: ${varName}`)
	}
}

const { PORT, DB_NAME, DB_PORT, DB_HOST, DB_MAINTENANCE, POSTGRES_USER, POSTGRES_PASSWORD, PGADMIN_DEFAULT_EMAIL, PGADMIN_DEFAULT_PASSWORD, PG_PORT } = Bun.env
const DATABASE_URL = `postgres://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}`

export const config = {
	PORT,
	DB_NAME,
	DB_PORT,
	DB_HOST,
	DB_MAINTENANCE,
	POSTGRES_USER,
	POSTGRES_PASSWORD,
	PGADMIN_DEFAULT_EMAIL,
	PGADMIN_DEFAULT_PASSWORD,
	PG_PORT,
	DATABASE_URL
}
```

- Create `utils/db/db.ts` file:
```ts
import { ConnectionRefusedError, Sequelize } from "sequelize";
import { config } from '../config'
import { blueBright, redBright } from "colorette";

const { DATABASE_URL } = config

const sequelize = new Sequelize(DATABASE_URL, {
	retry: {
		max: 3,
		timeout: 3000
	},
	pool: {
		max: 5,
		min: 0,
		acquire: 30000,
		idle: 10000,
	},
	dialectOptions: {
    connectTimeout: 10000,
  },
	logging: false
});

export const connectToDB = async () => {
	try {
		await sequelize.authenticate()
		console.log(blueBright('Connected to database.'))
	} catch (error: unknown) {
		if (error instanceof ConnectionRefusedError) {
			console.error(redBright("Database connection refused!"));
			console.log(blueBright('Check that database is running.'))
		} else {
			console.error(redBright('Unexpected error during startup: '), error)
		}
		process.exit(1)
	}
}
```
- `errorHandler.ts` can't handle that ConnectionRefusedError because that error happens in the server init so that's why we handle that error there

Modify your `index.ts` file:
- `index.ts`:
```ts
import * as http from "http";
import { cyanBright, yellowBright, redBright } from "colorette";
import app from "./app";
import { connectToDB } from "./utils/db/db";
import { config } from "./utils/config";

const { PORT } = config
let server = http.createServer(app);

// const restartServer = () => {
//   server.close(() => {
//     server = http.createServer(app);
//     startServer();
//   });
// };

const startServer = async () => {
	try {
		await connectToDB()
    server.listen(PORT, () => {
      console.log(yellowBright(`ENV = '${Bun.env.NODE_ENV}'`));
      console.log(cyanBright(`Server running on port ${PORT}`));
    });
  } catch (error: unknown) {
		throw error
  }
};

startServer();
```
## Database Models
Create `models` folder in your `src` folder -> Create `betModel.ts` file in the `models` folder:
```ts
import { DataTypes, Model, type Optional } from "sequelize";
import { sequelize } from "../utils/db/db";
import { BetStatus, BetType, Bookmaker, SportLeague } from "../utils/enums";
import type { Bet } from "../utils/types";

export interface BetCreation extends Optional<Bet, 'id'> {}

export class BetModel extends Model<Bet, BetCreation> implements Bet {
	declare id: number;
	declare user_id: number;
	declare stake: number;
	declare bookmaker: Bookmaker;
	declare tipper?: string;
	declare status: BetStatus;
	declare bet_final_type: BetType;
	declare sport: SportLeague;
	declare notes?: string;
}

BetModel.init(
	{
		id: {
			type: DataTypes.INTEGER,
			autoIncrement: true,
			primaryKey: true
		},
		user_id: {
			type: DataTypes.INTEGER,
			allowNull: false,
			defaultValue: 1,
			// references: {
			// 	model: 'UserModel',
			// 	key: 'id'
			// },
			// onDelete: 'CASCADE'
		},
		stake: {
			type: DataTypes.DECIMAL(10, 2),
			allowNull: false
		},
		bookmaker: {
			type: DataTypes.ENUM(...Object.values(Bookmaker)),
			allowNull: false
		},
		tipper: {
			type: DataTypes.STRING,
		},
		status: {
			type: DataTypes.ENUM(...Object.values(BetStatus)),
			allowNull: false
		},
		bet_final_type: {
			type: DataTypes.ENUM(...Object.values(BetType)),
			allowNull: false
		},
		sport: {
			type: DataTypes.ENUM(...Object.values(SportLeague)),
			allowNull: false
		},
		notes: {
			type: DataTypes.STRING
		},
	},
	{
		sequelize,
		underscored: true,
		timestamps: true,
		modelName: 'Bet',
	}
)

```
- The userModel is created later so thats why its commented out
Also make the `betDetailsModel.ts` in the `models` folder:
```ts
import { DataTypes, Model } from "sequelize";
import { sequelize } from "../utils/db/db";
import { BetType } from "../utils/enums";
import { BetModel } from "./betModel";

class BetDetailsModel extends Model {}

BetDetailsModel.init({
	id: {
		type: DataTypes.INTEGER,
		autoIncrement: true,
		primaryKey: true
	},
	bet_id: {
		type: DataTypes.INTEGER,
		allowNull: false,
		references: {
			model: BetModel,
			key: 'id'
		},
		onDelete: 'CASCADE'
	},
	date: {
		type: DataTypes.DATE,
		allowNull: false
	},
	home_team: {
		type: DataTypes.STRING,
	},
	away_team: {
		type: DataTypes.STRING,
	},
	selection: {
		type: DataTypes.STRING,
		allowNull: false
	},
	odds: {
		type: DataTypes.DECIMAL(10, 2),
		allowNull: false
	},
	home_result: {
		type: DataTypes.STRING,
	},
	away_result: {
		type: DataTypes.STRING,
	},
	betbuilder_selection: {
		type: DataTypes.ARRAY(DataTypes.STRING),
	},
	betbuilder_result: {
		type: DataTypes.ARRAY(DataTypes.STRING),
	},
	freebet: {
		type: DataTypes.BOOLEAN,
		defaultValue: false,
		allowNull: false
	},
	livebet: {
		type: DataTypes.BOOLEAN,
		defaultValue: false,
		allowNull: false
	},
	bet_type: {
		type: DataTypes.ENUM(...Object.values(BetType)),
		allowNull: false
	}
}, {
	sequelize,
	underscored: true,
	timestamps: true,
	modelName: 'BetDetails'
})

export { BetDetailsModel }
```

Then create `index.ts` file in the `models` folder where you mange the connections between tables:
```ts
import { blueBright } from "colorette";
import { sequelize } from "../utils/db/db";
import { BetModel } from "./betModel";
import { BetDetailsModel } from "./betDetailModel";

BetModel.hasMany(BetDetailsModel, {
	foreignKey: 'bet_id',
	onDelete: 'CASCADE',
	as: 'betDetails'
});

BetDetailsModel.belongsTo(BetModel, {
	foreignKey: 'bet_id',
	as: 'bet'
});

// Sync models with database
const syncDatabase = async () => {
  try {
    // In development, you might want to use:
    await sequelize.sync({ alter: true }); // This updates existing tables
    // Or for complete reset:
    // await sequelize.sync({ force: true }); // WARNING: This drops all tables

    console.log(blueBright("Database synchronized successfully."));
  } catch (error: unknown) {
    console.error("Error synchronizing database:", error);
    process.exit(1); // Exit if sync fails
  }
};

// Export a function to initialize the database
export const initializeDatabase = async () => {
  await syncDatabase();
};

export { BetModel, BetDetailsModel }
```
- And add `initializeDatabase` function to your `index.ts` file:
```ts
...
const startServer = async () => {
	try {
		await connectToDB()
		await initializeDatabase();

    server.listen(PORT, () => {
      console.log(yellowBright(`ENV = '${Bun.env.NODE_ENV}'`));
      console.log(cyanBright(`Server running on port ${PORT} ${String.fromCodePoint(0x1F41F)}`));
    });
  } catch (error: unknown) {
		throw error
  }
};

startServer();
```

### Controllers and Routes
Create folders `controllers` and `routes` in your `server/src` folder.
- `controllers/betController.ts`:
```ts
import { BetDetailsModel, BetModel } from "../models";
import type { GetBetsApiResponse } from "../utils/api-response-types";
import { sequelize } from "../utils/db/db";
import type { BetDetails } from "../utils/types";
import type { NextFunction, Request, Response } from "express";

//
// GET
// Get all bets
export const getBets = async (
  _req: Request,
  res: Response,
  next: NextFunction,
): Promise<void> => {
  try {
    const bets = await BetModel.findAll({
      include: [
        {
          model: BetDetailsModel,
          as: "betDetails",
        },
      ],
    });
    res.status(200).json({ data: bets } as GetBetsApiResponse);
  } catch (error: unknown) {
    next(error);
  }
};

//
// POST
// Create a bet
export const createBet = async (
  req: Request,
  res: Response,
  next: NextFunction,
): Promise<void> => {
  const transaction = await sequelize.transaction(); // Transactions are used to ensure that all operations are completed successfully before committing the changes to the database

  try {
    const {
      user_id,
      stake,
      bookmaker,
      tipper,
      status,
      bet_final_type,
      sport,
      notes,
      betDetails,
    } = req.body;
    // The create method is used to create a new bet in the database
    // Transaction in the end makes sure this operation is tied to the transaction
    const newBet = await BetModel.create(
      {
        user_id,
        stake,
        bookmaker,
        tipper,
        status,
        bet_final_type,
        sport,
        notes,
      },
      { transaction },
    );

    // The map method is used to create a new array of bet details with the bet_id set to the id of the newly created bet
    const betDetailsData = betDetails.map((details: BetDetails) => ({
      ...details,
      bet_id: newBet.id,
    }));

    // bulkCreate is used to create multiple bet details in the database
    await BetDetailsModel.bulkCreate(betDetailsData, { transaction });
    await transaction.commit();

    res
      .status(201)
      .json({ data: newBet, message: "Bet created." } as CreateBetApiResponse);
  } catch (error: unknown) {
    // If an error occurs, the transaction is rolled back
    await transaction.rollback();
    next(error);
  }
};
```
- I also added `api-response-types.ts` file in my `utils` folder but it's about useless
```ts
import type { Bet } from "./types"

export type GetBetsApiResponse = {
	data: Bet[];
}

export type CreateBetApiResponse = {
	data: Bet;
	message: string;
}
```
- `routes/betRoute.ts`:
```ts
import { createBet, getBets } from "../controllers/betController";
import express from "express";

export const betRouter = express.Router({ mergeParams: true });

betRouter.post("/", createBet);
betRouter.get("/", getBets);
```

And add the betRouter to the `app.ts` file:
```ts
...
app.use(helmet());
app.use(morgan("dev"));
app.use(express.json());
app.use(cors(corsOptions));

app.use("/api/bets", betRouter);

app.use(notFoundHandler);
app.use(errorHandler);

export default app;

```

Also updated the `errorHandler.ts` middleware:
```ts
import type { NextFunction, Request, Response } from "express";
import { ValidationError } from "sequelize";

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

	if (error instanceof ValidationError) {
		console.error(`[ValidationError] ${error.message}`, errorResponse);
	} else {
		console.error(`[Error] ${error.message}`, errorResponse);
	}

	res.status(statusCode).json(errorResponse);
}
```