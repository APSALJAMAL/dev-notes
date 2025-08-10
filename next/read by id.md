# read by id

### app\api\item\[id]\route.ts
```ts
import { NextResponse } from "next/server";
import { PrismaClient } from "@prisma/client";
import { getUserId } from "@/actions/user.action";

const prisma = new PrismaClient();

export async function GET(
  req: Request,
  context: { params: Promise<{ id: string }> }
) {
  const userId = await getUserId();
  if (!userId) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  const { id } = await context.params; // ✅ await params

  try {
    const plant = await prisma.plant.findUnique({
      where: { id, userId },
    });

    if (!plant) {
      return NextResponse.json({ error: "Plant not found" }, { status: 404 });
    }

    return NextResponse.json(plant);
  } catch (error) {
    console.error("Error fetching plant:", error);
    return NextResponse.json({ error: "Internal Server Error" }, { status: 500 });
  }
}
```
### app\ [id]\page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
"use client";

import { useEffect, useState } from "react";
import { useParams } from "next/navigation";

type Plant = {
  id: string;
  name: string;
  description?: string;
  stock: number;
  price: number;
  createdAt: string;
};

export default function PlantDetailsPage() {
  const params = useParams();
  const id = typeof params?.id === "string" ? params.id : undefined;

  const [plant, setPlant] = useState<Plant | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    if (!id) return; // ✅ Avoid calling API without id

    async function fetchPlant() {
      try {
        setLoading(true);
        const res = await fetch(`/api/item/${id}`);
        if (!res.ok) {
          const err = await res.json();
          throw new Error(err.error || "Failed to fetch");
        }
        const data = await res.json();
        setPlant(data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchPlant();
  }, [id]);

  if (!id) return <p className="p-4">Invalid plant ID</p>;
  if (loading) return <p className="p-4">Loading plant...</p>;
  if (error) return <p className="p-4 text-red-500">Error: {error}</p>;
  if (!plant) return <p className="p-4">No plant found.</p>;

  return (
    <div className="max-w-xl mx-auto mt-10 p-4 border rounded shadow">
      <h1 className="text-2xl font-bold mb-2">{plant.name}</h1>
      {plant.description && <p className="mb-2">{plant.description}</p>}
      <p className="text-sm text-gray-600 mb-1">Stock: {plant.stock}</p>
      <p className="text-sm text-gray-600 mb-4">
        Price: ${plant.price.toFixed(2)}
      </p>
      <p className="text-xs text-gray-400">
        Added: {new Date(plant.createdAt).toLocaleString()}
      </p>
    </div>
  );
}
```