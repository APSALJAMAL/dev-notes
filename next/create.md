# create

### schema.prisma
```ts
generator client {
  provider = "prisma-client-js"
  // output   = "../lib/generated/prisma"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id                         String   @id @default(uuid())
  email                      String   @unique
  name                       String?
  displayName                String?
  profileImageUrl            String?
  createdAt                  DateTime?
  updatedAt                  DateTime?

  Plant Plant[] //#2
}

model Plant {  //#1
  id          String   @id @default(cuid())
  name        String
  description String?
  stock       Int
  price       Float
  
  userId      String
  user        User     @relation(fields: [userId], references: [id])

  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

### app\api\item\route.ts
```ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import { getUserId } from '@/actions/user.action';


export async function POST(req: Request) {
  const userId = await getUserId();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  const body = await req.json();
  const { name, description, stock, price } = body; //#1

  try {
    const plant = await prisma.plant.create({ //#2
      data: {
        name, description, stock, price,
        userId,
      },
    });
    return NextResponse.json(plant);//#3
  } catch (error) {
    return NextResponse.json({ error: 'Error creating item' }, { status: 500 });
  }
}
```
### page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
"use client";

import { useState } from "react";
import { useForm } from "react-hook-form";

type PlantFormInputs = {//#1
  name: string;
  description?: string;
  stock: number;
  price: number;
};

export default function PlantForm() {
  const [loading, setLoading] = useState(false);
  const [message, setMessage] = useState("");
  const { register, handleSubmit, reset, formState: { errors } } = useForm<PlantFormInputs>();

  const onSubmit = async (data: PlantFormInputs) => {
    setLoading(true);
    setMessage("");

    try {
      const res = await fetch("/api/item", {//#2
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data),
      });

      if (!res.ok) {
        const err = await res.json();
        throw new Error(err.error || "Something went wrong");
      }

      const plant = await res.json();
      setMessage(`✅ Plant "${plant.name}" created successfully!`);
      reset();
    } catch (error: any) {
      setMessage(`❌ ${error.message}`);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="max-w-lg mx-auto mt-10 p-6 border rounded-lg shadow">
      <h1 className="text-2xl font-bold mb-4">Add New Plant</h1>
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4 ">
      {/* Name */}
      <div>
        <label className="block font-medium">Name</label>
        <input
          type="text"
          {...register("name", { required: "Name is required" })}
          className="w-full border rounded p-2"
        />
        {errors.name && <p className="text-red-500 text-sm">{errors.name.message}</p>}
      </div>

      {/* Description */}
      <div>
        <label className="block font-medium">Description</label>
        <textarea
          {...register("description")}
          className="w-full border rounded p-2"
        />
      </div>

      {/* Stock */}
      <div>
        <label className="block font-medium">Stock</label>
        <input
          type="number"
          {...register("stock", { required: "Stock is required", valueAsNumber: true })}
          className="w-full border rounded p-2"
        />
        {errors.stock && <p className="text-red-500 text-sm">{errors.stock.message}</p>}
      </div>

      {/* Price */}
      <div>
        <label className="block font-medium">Price</label>
        <input
          type="number"
          step="0.01"
          {...register("price", { required: "Price is required", valueAsNumber: true })}
          className="w-full border rounded p-2"
        />
        {errors.price && <p className="text-red-500 text-sm">{errors.price.message}</p>}
      </div>

      <button
        type="submit"
        disabled={loading}
        className="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 disabled:opacity-50"
      >
        {loading ? "Saving..." : "Create Plant"}
      </button>

      {message && <p className="mt-3 font-medium">{message}</p>}
    </form>
    </div>
  );
}

```