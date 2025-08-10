# delete
### app\api\item\ [id]\route.ts
```ts
export async function DELETE(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params; // ✅ await params

  try {
    const userId = await getUserId();
    if (!userId) {
      return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
    }

    await prisma.plant.delete({
      where: {
        id,
      
      },
    });

    return NextResponse.json({ success: true });
  } catch (err: any) {
    return NextResponse.json(
      { error: err.message || "Failed to delete" },
      { status: 500 }
    );
  }
}
```
### page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
"use client";

import { useEffect, useState } from "react";

type Plant = {
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
    fetchPlants();
  }, []);

  async function fetchPlants() {
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

  async function deletePlant(id: string) {
    if (!confirm("Are you sure you want to delete this plant?")) return;
    try {
      const res = await fetch(`/api/item/${id}`, { method: "DELETE" });
      if (!res.ok) {
        const err = await res.json();
        throw new Error(err.error || "Failed to delete");
      }
      // Remove plant from state without re-fetch
      setPlants((prev) => prev.filter((p) => p.id !== id));
    } catch (err: any) {
      alert(err.message);
    }
  }

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
            <li
              key={plant.id}
              className="border rounded p-4 shadow flex justify-between items-start"
            >
              <div>
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
              </div>
              <button
                onClick={() => deletePlant(plant.id)}
                className="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600"
              >
                Delete
              </button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}

``` 