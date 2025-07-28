npm i -D prisma

npx prisma init

npx prisma generate  *** modify the schema ***

npx prisma db push

npm i @prisma/client

npx prisma migrate reset *** reset database ***

# .env

DATABASE_URL=postgresql

# prisma\schema.prisma

# lib\db.ts or prisma.ts
```ts

import { PrismaClient } from "@prisma/client";

declare global {
  var prisma: PrismaClient | undefined;
}

export const db = globalThis.prisma || new PrismaClient();

if (process.env.NODE_ENV !== "production") globalThis.prisma = db;
```
