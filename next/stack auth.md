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
/* eslint-disable @typescript-eslint/no-explicit-any */
'use server';

import { prisma } from '@/lib/prisma';
import { stackServerApp } from '@/stack';
import { neon } from '@neondatabase/serverless';


//  Get single user from Neon via SQL
export async function getUserDetails(userId: string | undefined) {
  if (!process.env.DATABASE_URL) {
    throw new Error('DATABASE_URL is not set');
  }

  if (!userId) {
    return null;
  }

  const sql = neon(process.env.DATABASE_URL!);
  const [user] = await sql`SELECT * FROM neon_auth.users_sync WHERE id = ${userId};`;

  // Optional: Save to Prisma
  if (user) {

    await saveUserToDb(user);
  }

  return user;
}


//  Get current user ID from stack (auth layer)
export async function getUserId() {
  const user = await stackServerApp.getUser(); // get user from auth
  const userId = user?.id;
  return userId;
}


//  Get all users from Neon (read-only)
export async function getAllUserDetails() {
  if (!process.env.DATABASE_URL) {
    throw new Error('DATABASE_URL is not set');
  }

  const sql = neon(process.env.DATABASE_URL);
  const users = await sql`SELECT * FROM neon_auth.users_sync;`;

  // ✅ Save all users to Prisma
  for (const user of users) {

    await saveUserToDb(user);
  }

  return users;
}


//  Save user to Prisma DB
export async function saveUserToDb(user: any) {
  try {
    const existing = await prisma.user.findUnique({ where: { id: user.id } });
    if (existing) {

      return { success: true, existing };
    }

    const newUser = await prisma.user.create({
      data: {
        id: user.id,
        email: user.email,
        name: user.name,
        displayName: user.display_name || user.raw_json?.display_name,
        profileImageUrl: user.profile_image_url || user.raw_json?.profile_image_url,
        createdAt: user.created_at ? new Date(user.created_at) : null,
        updatedAt: user.updated_at ? new Date(user.updated_at) : null,
        deletedAt: user.deleted_at ? new Date(user.deleted_at) : null,
        primaryEmail: user.primary_email,
        primaryEmailVerified: user.primary_email_verified,
        lastActiveAtMillis: user.last_active_at_millis,
        signedUpAtMillis: user.signed_up_at_millis,
        isAnonymous: user.is_anonymous,
        hasPassword: user.has_password,
        otpAuthEnabled: user.otp_auth_enabled,
        passkeyAuthEnabled: user.passkey_auth_enabled,
        primaryEmailAuthEnabled: user.primary_email_auth_enabled,
        requiresTotpMfa: user.requires_totp_mfa,
        authWithEmail: user.raw_json?.auth_with_email ?? false,

        rawJson: user.raw_json,
        clientMetadata: user.client_metadata,
        clientReadOnlyMetadata: user.client_read_only_metadata,
        serverMetadata: user.server_metadata,
        oauthProviders: user.oauth_providers,

        selectedTeam: user.selected_team,
        selectedTeamId: user.selected_team_id,
      },
    });


    return { success: true, newUser };
  } catch (error: any) {

    return { success: false, error: error.message };
  }
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
### page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */

import { getAllUserDetails } from "@/actions/user.action";
import Image from "next/image";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";

export default async function UsersPage() {
  const users = await getAllUserDetails();

  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">All Users</h1>

      <div className="overflow-x-auto rounded-lg border">
        <Table>
          <TableHeader>
            <TableRow>
              <TableHead className="w-10">#</TableHead>
              <TableHead>Profile</TableHead>
              <TableHead>Name</TableHead>
              <TableHead>Email</TableHead>
              <TableHead>Verified</TableHead>
              <TableHead>Created At</TableHead>
              <TableHead>Last Active</TableHead>
              <TableHead>User ID</TableHead>
            </TableRow>
          </TableHeader>
          <TableBody>
            {users.map((user: any, index: number) => {
              const profileImage =
                user.profile_image_url ||
                user.raw_json?.profile_image_url ||
                "";
              const displayName = user.display_name || user.name || user.email || "User";
              const fallbackLetter = displayName.charAt(0).toUpperCase();

              return (
                <TableRow key={user.id}>
                  <TableCell>{index + 1}</TableCell>
                  <TableCell>
                    {profileImage ? (
                      <Image
                        src={profileImage}
                        alt="Profile"
                        width={40}
                        height={40}
                        className="rounded-full"
                      />
                    ) : (
                      <div className="w-10 h-10 rounded-full bg-gray-300 text-white font-semibold flex items-center justify-center">
                        {fallbackLetter}
                      </div>
                    )}
                  </TableCell>
                  <TableCell>{displayName}</TableCell>
                  <TableCell>{user.email}</TableCell>
                  <TableCell>
                    {user.primary_email_verified ? "Yes" : "No"}
                  </TableCell>
                  <TableCell>
                    {user.created_at
                      ? new Date(user.created_at).toLocaleString()
                      : "N/A"}
                  </TableCell>
                  <TableCell>
                    {user.last_active_at_millis
                      ? new Date(user.last_active_at_millis).toLocaleString()
                      : "N/A"}
                  </TableCell>
                  <TableCell className="text-xs break-all">{user.id}</TableCell>
                </TableRow>
              );
            })}
          </TableBody>
        </Table>
      </div>
    </div>
  );
}

```
### next.config.ts
```ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  images: {
    domains: ['lh3.googleusercontent.com'],
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '**', // wildcard for any domain
      },
    ],
    dangerouslyAllowSVG: true,
    unoptimized: true, // disable Next.js image optimization completely
  },
  eslint: {
    ignoreDuringBuilds: true,
  },
  typescript: {
    ignoreBuildErrors: true,
  },
};

export default nextConfig;

```