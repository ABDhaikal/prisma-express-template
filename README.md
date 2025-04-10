# creating template express prisma

1. initialize `package json`
`npm i —y`
2. install typescript, types/node types/express nodemon ts-node and types/cors -d
`npm i typescript @types/node @types/express nodemon ts-node @types/cors -d`
3. install express ,cors ,and prisma
 `npm i express cors dotenv prisma @prisma/client`
4. initialize tsc
`tsc —init`
5. change `tsconfig.json` `outdir`

![image.png](attachment:1bd7d2db-3163-40fb-9ad3-915cdc02417a:image.png)

so default `tsconfig.json` should be 

```json
{
   "compilerOptions": {
      "target": "es2016",
      "module": "commonjs",
      "outDir": "./dist",
      "esModuleInterop": true,
      "forceConsistentCasingInFileNames": true,
      "strict": true,
      "skipLibCheck": true
   }
}

```

1. go to `package.json`  and add comment to run dev run build and run start

```
"scripts": {
    "build": "tsc",
    "start": "npm run build && node dist/index.js",
    "dev": "nodemon src/index.ts"
  },
```

1. create  `src/config/env.ts`

```tsx
import dotenv from 'dotenv';

dotenv.config();

export const PORT = process.env.PORT
```

1. create `src/index.ts`

```tsx
import express from "express";
import { PORT } from "./config/env";

const app = express();

app.listen(PORT, () => {
   console.log(`Server is running at http://localhost:${PORT}`);
});

```

1. initialize prisma

`npx prisma init`

1. setup `prisma/schema.prisma` into default

```
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

1. create `src/config/prisma.ts`

```
import { PrismaClient } from "@prisma/client";

export default new PrismaClient();
```

1. create middleware folder and file `src/middlewares/error.middleware.ts`

```
import { NextFunction, Request, Response } from "express";
import { ApiError } from "../utils/api-error";

export const errorMiddleware = (
   err: ApiError,
   _req: Request,
   res: Response,
   next: NextFunction
) => {
   const status = err.status || 500;
   const message = err.message || "Something went wrong";
   res.status(status).send(message);
};

```

1. create utils folder and file  `src/utils/api-error.ts`

```
export class ApiError extends Error {
   status: number;
   constructor(message: string, status: number = 500) {
      super(message);
      this.status = status;
   }
}
```

1. add your error middleware to your index.ts so its can be run

```tsx
import express from "express";
import { PORT } from "./config/env";
import { errorMiddleware } from "./middlewares/error.middleware";

const app = express();
// your routes should be here

// your error middle ware
app.use(errorMiddleware);

// listening to the server
app.listen(PORT, () => {
   console.log(`Server is running at http://localhost:${PORT}`);
});

```

1. create another template folder

```tsx
mkdir src/controllers
mkdir src/routes
mkdir src/types
mkdir src/services
mkdir src/validators
```

## how to create new model and service

1. create your dummy table in `prisma/schema.prisma`

```
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
}
```

1. generate prisma db
`npx prisma generate`