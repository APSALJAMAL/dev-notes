# prisma
```txt
npm i -D prisma

npx prisma init

npm i @prisma/client

npx prisma db push

npx prisma generate  *** modify the schema ***

npx prisma migrate reset *** reset database ***
```

### .env
```
DATABASE_URL=postgresql
```
### prisma\schema.prisma
```ts
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  plants    Plant[]  // 1:N relationship — one user has many plants
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Plant {
  id          String   @id @default(cuid())
  name        String
  description String?
  category    String
  stock       Int
  price       Float
  domain      String
  stack       String
  imageUrl    String?
  userId      String             // Foreign key
  user        User   @relation(fields: [userId], references: [id]) // Define relation
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Example {
  id        String   @id @default(cuid())
  name      String
  age       Int
  salary    Decimal
  active    Boolean  @default(true)
  metadata  Json?
  avatar    Bytes?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

```

### lib\db.ts or prisma.ts
```ts

// lib/prisma.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = global as unknown as { prisma: PrismaClient };

export const prisma =
  globalForPrisma.prisma || new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;
```
