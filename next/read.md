# read

### app\api\item\route.ts
```ts
export async function GET() {
  // const userId = await getUserId();
  // if (!userId) return NextResponse.json({ error: "Unauthorized" }, { status: 401 });

  try {
    const plants = await prisma.plant.findMany({ //#1
      // where: { userId },
      orderBy: { createdAt: "desc" },
    });

    return NextResponse.json(plants);//#2
  } catch (error) {
    console.error("Error fetching plants:", error);
    return NextResponse.json({ error: "Error fetching data" }, { status: 500 });
  }
}
```
### app\page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
"use client";

import { useEffect, useState } from "react";

type Plant = {//#1
  id: string;
  name: string;
  description?: string;
  stock: number;
  price: number;
  createdAt: string;
};

export default function PlantsPage() {
  const [plants, setPlants] = useState<Plant[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchPlants() { //#2
      try {
        const res = await fetch("/api/item");
        if (!res.ok) {
          const err = await res.json();
          throw new Error(err.error || "Failed to fetch");
        }
        const data = await res.json();
        setPlants(data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    fetchPlants();
  }, []);

  if (loading) return <p className="p-4">Loading plants...</p>;
  if (error) return <p className="p-4 text-red-500">Error: {error}</p>;

  return (
    <div className="max-w-3xl mx-auto mt-10 p-4">
      <h1 className="text-2xl font-bold mb-4">My Plants</h1>
      {plants.length === 0 ? (
        <p>No plants found.</p>
      ) : (
        <ul className="space-y-4">
          {plants.map((plant) => (
            <li key={plant.id} className="border rounded p-4 shadow">
              <h2 className="text-lg font-semibold">{plant.name}</h2>
              {plant.description && (
                <p className="text-gray-600">{plant.description}</p>
              )}
              <div className="mt-2 text-sm text-gray-500">
                Stock: {plant.stock} | Price: ${plant.price.toFixed(2)}
              </div>
              <div className="text-xs text-gray-400">
                Added: {new Date(plant.createdAt).toLocaleString()}
              </div>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}

```