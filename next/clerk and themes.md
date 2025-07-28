npm install @clerk/nextjs

# .env

<!--  clerk auth keys -->
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test
CLERK_SECRET_KEY=sk_test
<!--  clerk redirect urls -->
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/

# app/layout.tsx
<ClerkProvider>
      
</ClerkProvider>

# middleware.ts

import { authMiddleware } from "@clerk/nextjs";

export default authMiddleware({
  publicRoutes: ["/api/uploadthing"],
});

export const config = {
  matcher: ["/((?!.+\\.[\\w]+$|_next).*)", "/(api|trpc)(.*)"],
};


# app\(auth)\(routes)\sign-in\[[...sign-in]]/page.tsx
"use client";

import { SignIn } from "@clerk/nextjs";
import { dark } from "@clerk/themes";
import { useTheme } from "next-themes";

export default function Page() {
  const { resolvedTheme } = useTheme();

  return (
    <SignIn
      appearance={{
        baseTheme: resolvedTheme === "dark" ? dark : undefined,
      }}
    />
  );
}


# app\(auth)\(routes)\sign-up/page.tsx

"use client";

import { SignUp } from "@clerk/nextjs";
import { dark } from "@clerk/themes";
import { useTheme } from "next-themes";

export default function Page() {
  const { resolvedTheme } = useTheme();

  return (
    <SignUp
      appearance={{
        baseTheme: resolvedTheme === "dark" ? dark : undefined,
      }}
    />
  );
}


# userbutton

"use client";

import { UserButton as ClerkUserButton } from "@clerk/nextjs";
import { dark } from "@clerk/themes";
import { useTheme } from "next-themes";

export const UserButton = () => {
  const { theme } = useTheme();

  return (
    <ClerkUserButton
      appearance={{
        baseTheme: theme === "dark" ? dark : undefined,
        elements: {
          avatarBox: "h-[48px] w-[48px]",
        },
      }}
      userProfileMode="navigation"
      userProfileUrl="/account"
    />
  );
};




<!-------------------------------------------->

npm i next-themes
npx shadcn@latest add dropdowm-menu


# components/providers/theme-provider.tsx

"use client";

import * as React from "react";
import { ThemeProvider as NextThemesProvider } from "next-themes";
import { type ThemeProviderProps } from "next-themes/dist/types";

export const ThemeProvider = ({ children, ...props }: ThemeProviderProps) => {
  return <NextThemesProvider {...props}>{children}</NextThemesProvider>;
};


# app/layout.tsx

import { ClerkProvider } from "@clerk/nextjs";

import type { Metadata, Viewport } from "next";



import { ThemeProvider } from "@/components/providers/theme-provider";
import { siteConfig } from "@/config";
import { cn } from "@/lib/utils";

import "./globals.css";

const font = Open_Sans({ subsets: ["latin"] });

export const viewport: Viewport = {
  themeColor: "#5865F2",
};

export const metadata: Metadata = siteConfig;

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <ClerkProvider>
      <html lang="en" suppressHydrationWarning>
        <body className={cn(font.className, "bg-white dark:bg-[#313338]")}>
          <ThemeProvider
            attribute="class"
            defaultTheme="dark"
            enableSystem={false}
            storageKey="discord-theme"
          >
            {children}
            
          </ThemeProvider>
        </body>
      </html>
    </ClerkProvider>
  );
}


# components/mode-toggle.tsx

"use client";

import * as React from "react";
import { Moon, Sun } from "lucide-react";
import { useTheme } from "next-themes";

import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";

type ModeToggleProps = {
  align?: "center" | "start" | "end";
  side?: "top" | "right" | "bottom" | "left";
};

export const ModeToggle = ({ align, side }: ModeToggleProps) => {
  const { setTheme } = useTheme();

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button
          className="bg-transparent border-0"
          variant="outline"
          size="icon"
        >
          <Sun className="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">Toggle theme</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align={align} side={side}>
        <DropdownMenuItem onClick={() => setTheme("light")}>
          Light
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          Dark
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  );
};


<!-------------------------------------------->

initial-profile.ts

import { currentUser, redirectToSignIn } from "@clerk/nextjs";

import { db } from "./db";

export const initialProfile = async () => {
  const user = await currentUser();

  if (!user) return redirectToSignIn();

  const profile = await db.profile.findUnique({
    where: {
      userId: user.id,
    },
  });

  if (profile) return profile;

  const newProfile = await db.profile.create({
    data: {
      userId: user.id,
      name: `${user.firstName} ${user.lastName}`,
      imageUrl: user.imageUrl,
      email: user.emailAddresses[0].emailAddress,
    },
  });

  return newProfile;
};
