# stack auth
```txt
npx @stackframe/init-stack@latest
```
### .env
```txt
NEXT_PUBLIC_STACK_PROJECT_ID=
NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY=
STACK_SECRET_SERVER_KEY=
DATABASE_URL=
```
```
npm i @neondatabase/serverless
```
### actions/user.action.ts
```tsx
'use server';

import { stackServerApp } from '@/stack';
import { neon } from '@neondatabase/serverless';

export async function getUserDetails(userId: string | undefined) {
  if (!process.env.DATABASE_URL) {
    throw new Error('DATABASE_URL is not set');
  }

  if (!userId) {
    return null;
  }

  const sql = neon(process.env.DATABASE_URL!);
  const [user] =
    await sql`SELECT * FROM neon_auth.users_sync WHERE id = ${userId};`;
  return user;
}

export async function getUserId() {
  const user = await stackServerApp.getUser(); //get user details from neon
  const userId = user?.id;

  if (!userId) return;

  return userId;
}
```
### Navbar.tsx
```tsx
import Link from "next/link";

import { HomeIcon, LogIn, LogOut, Sprout } from "lucide-react";

import { stackServerApp } from "@/stack";
import { UserButton } from "@stackframe/stack";
import Image from "next/image";
import logo from "@/app/favicon.ico";
import { Button } from "../ui/button";
import { ThemeToggle } from "./ThemeToggle";

async function Navbar() {
  const user = await stackServerApp.getUser();
  const app = stackServerApp.urls;

  return (
    <nav className="sticky top-0 w-full border-b bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60 z-50">
      <div className="max-w-7xl mx-auto px-4">
        <div className="flex items-center justify-between h-16">
          {/* Logo */}
          <Link href="/" className="flex items-center gap-2">
            <Image src={logo} alt="Logo" width={40} height={40} />
            <span className="text-xl font-bold text-gray-800 dark:text-white">REPULSO</span>
          </Link>

          {/* Navbar Links */}
          <div className="flex flex-wrap items-center gap-3">
            <Button variant="ghost" className="flex items-center gap-2" asChild>
              <Link href="/plants">
                <Sprout className="w-4 h-4" />
                <span className=" sm:inline">Plants</span>
              </Link>
            </Button>

            <Button variant="ghost" className="flex items-center gap-2" asChild>
              <Link href="/">
                <HomeIcon className="w-4 h-4" />
                <span className=" sm:inline">Home</span>
              </Link>
            </Button>

            <ThemeToggle/>

            {user ? (
              <>
                <Button variant="outline" className="flex items-center gap-2" asChild>
                  <Link href={app.signOut || "/logout"}>
                    <LogOut className="w-4 h-4" />
                    <span className=" sm:inline">Sign Out</span>
                  </Link>
                </Button>

                <UserButton />
              </>
            ) : (
              <Button variant="ghost" className="flex items-center gap-2" asChild>
                <Link href={app.signIn || "/login"}>
                  <LogIn className="w-4 h-4" />
                  <span className=" sm:inline">Sign In</span>
                </Link>
              </Button>
            )}
          </div>
        </div>
      </div>
    </nav>
  );
}

export default Navbar;

```