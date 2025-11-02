# next js set up
```txt
npx create-next-app@latest

```
```
npm run dev
```
```
npm install --legacy-peer-deps
```
file structure in tree format
```
tree /f
```
# shadcn ui set up
```txt
npx shadcn@latest init

npx shadcn@latest add button
```
```txt

import { Button } from "@/components/ui/button"

export default function Home() {
  return (
    <div>
      <Button>Click me</Button>
    </div>
  )
}
```
# dark mode
```txt
npm install next-themes

```
### theme-provider.tsx
```txt
// theme-provider.tsx
"use client"

import * as React from "react"
import { ThemeProvider as NextThemesProvider } from "next-themes"

export function ThemeProvider({
  children,
  ...props
}: React.ComponentProps<typeof NextThemesProvider>) {
  return <NextThemesProvider {...props}>{children}</NextThemesProvider>
}
```
### layout.tsx
```txt
// layout.tsx
import { ThemeProvider } from "@/components/theme-provider"

export default function RootLayout({ children }: RootLayoutProps) {
  return (
    <html lang="en" suppressHydrationWarning>
      <head />
      <body>
        <ThemeProvider
          attribute="class"
          defaultTheme="system"
          enableSystem
          disableTransitionOnChange
        >
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}
```
### ThemeToggle.tsx
```txt
// ThemeToggle.tsx"use client"

import * as React from "react"
import { Moon, Sun } from "lucide-react"
import { useTheme } from "next-themes"

import { Button } from "@/components/ui/button"

export function ThemeToggle() {
  const { theme, setTheme } = useTheme()

  const toggleTheme = () => {
    setTheme(theme === "dark" ? "light" : "dark")
  }

  return (
    <Button
      onClick={toggleTheme}
      variant="ghost"
      size="icon"
      className="bg-transparent "
      aria-label="Toggle theme"
    >
      <Sun className="h-5 w-5 transition-all dark:rotate-90 dark:scale-0" />
      <Moon className="absolute h-5 w-5 rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
    </Button>
  )
}

```
### Navbar.tsx
```txt
// Navbar.tsx
import Link from "next/link";

import { HomeIcon,  Sprout } from "lucide-react";

import Image from "next/image";
import logo from "@/app/favicon.ico";
import { Button } from "../ui/button";
import { ThemeToggle } from "./ThemeToggle";

async function Navbar() {


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

          </div>
        </div>
      </div>
    </nav>
  );
}

export default Navbar;

```