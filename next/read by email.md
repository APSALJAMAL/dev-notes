# read by email

### actions\user.action.ts
```ts
export async function getUserEmail() {
  const userId = await getUserId();
  if (!userId) return null;

  const user = await getUserDetails(userId);
  return user?.email || null;
}
```
```tsx
import { getUserEmail } from "@/lib/user"; // adjust the path to where your function is

export default async function Page() {
  const email = await getUserEmail();

  return (
    <main className="p-4">
      <h1 className="text-xl font-bold">User Email</h1>
      {email ? (
        <p>{email}</p>
      ) : (
        <p className="text-gray-500">No email found or user not logged in.</p>
      )}
    </main>
  );
}
```
### app\api\item\route.ts
```ts
export async function getItemsByUserEmail() {
  const email = await getUserEmail();
  if (!email) return [];

  const plants = await prisma.plant.findMany({
    where: {
      user: {
        email,
      },
    },
  });

  return plants;
```
### page.tsx
```tsx
/* eslint-disable react/no-unescaped-entities */

import { getItemsByUserEmail } from "./api/item/route";

export default async function Page() {
  const plants = await getItemsByUserEmail();

  if (!plants.length) {
    return <p>No plants found for this user.</p>;
  }

  return (
    <div className="p-4">
      <h1 className="text-xl font-bold mb-4">User's Plants</h1>
      <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
        {plants.map((plant) => (
          <div
            key={plant.id}
            className="border rounded-lg p-4 shadow-sm hover:shadow-md transition"
          >
            <h2 className="font-semibold text-lg">{plant.name}</h2>
            <p className="text-gray-600">
              {plant.description || "No description"}
            </p>
            <p className="mt-2">
              <span className="font-medium">Stock:</span> {plant.stock}
            </p>
            <p>
              <span className="font-medium">Price:</span> ₹{plant.price}
            </p>
          </div>
        ))}
      </div>
    </div>
  );
}

```