# Uploadthing
```txt
npm install uploadthing @uploadthing/react react-dropzone
```
 
### .env
```txt
UPLOADTHING_SECRET='sk_live'
UPLOADTHING_APP_ID=''
NEXT_PUBLIC_BASE_URL=http://localhost:3000
```

### debug package.json
 if GET http://localhost:3001/api/uploadthing 500 (Internal Server Error)
useFetch.useEffect.fetchData @ useFetch.ts:67
```ts
npm ls effect


{
  "dependencies": {
    "effect": "3.16.12"
  },
  "overrides": {
    "effect": "3.16.12"
  }
}

```

### app\api\uploadthing\core.ts
```js
/* eslint-disable @typescript-eslint/no-unused-vars */
import { stackServerApp } from "@/stack";
import { createUploadthing, type FileRouter } from "uploadthing/next";


const f = createUploadthing();

// FileRouter for your app, can contain multiple FileRoutes
export const ourFileRouter = {
  // Define as many FileRoutes as you like, each with a unique routeSlug
  postImage: f({
    image: {
      maxFileSize: "4MB",
      maxFileCount: 6,
    },
  })
    // Set permissions and file types for this FileRoute
    .middleware(async ({ req }) => {
      // This code runs on your server before upload
      const user = await stackServerApp.getUser();
      // If you throw, the user will not be able to upload
      if (!user) throw new Error("Unauthorized");
      // Whatever is returned here is accessible in onUploadComplete as `metadata`
      return { userId: user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      try{
        console.log("Upload complete for userId:", metadata.userId);
        console.log("file url", file.ufsUrl);
        return { fileUrl: file.ufsUrl }; 
      }catch (error) {
        console.error("Error in onUploadComplete:", error);
        throw error;
      }
    }),
} satisfies FileRouter;
export type OurFileRouter = typeof ourFileRouter;
```
### app\api\uploadthing\route.ts
```js
import { createRouteHandler } from "uploadthing/next";
import { ourFileRouter } from "./core";
// Export routes for Next App Router
export const { GET, POST } = createRouteHandler({
  router: ourFileRouter,
  // Apply an (optional) custom config:
  // config: { ... },
});
```
### lib\uploadthing.ts
```js
import { OurFileRouter } from "@/app/api/uploadthing/core";
import {
    generateUploadButton,
    generateUploadDropzone,
  } from "@uploadthing/react";

  export const UploadButton = generateUploadButton<OurFileRouter>();
  export const UploadDropzone = generateUploadDropzone<OurFileRouter>();
```
### middleware.ts
```js
import { NextRequest, NextResponse } from "next/server";

const PUBLIC_ROUTES = ["/", "/login", "/register", "/api/uploadthing"];

export function middleware(req: NextRequest) {
  const { pathname } = req.nextUrl;

  // Allow public routes
  if (PUBLIC_ROUTES.includes(pathname)) {
    return NextResponse.next();
  }

 

  return NextResponse.next();
}


export const config = {
  matcher: ["/((?!.+\\.[\\w]+$|_next).*)", "/(api|trpc)(.*)"],
};

```
### components\ImageUpload.tsx
```js
"use client";

import { FileIcon, X } from "lucide-react";
import Image from "next/image";

import { UploadDropzone } from "@/lib/uploadthing";

import "@uploadthing/react/styles.css";

interface ImageUploadProps {
  onChange: (url: string) => void;
  value: string;
  endpoint: "postImage";
}

function ImageUpload({ endpoint, onChange, value }: ImageUploadProps) {
  const fileType = value.split(".").pop();

  if (value && fileType !== "pdf" ) {
    return (
      <div className="relative">
        <Image src={value} alt="uploaded image" height={400} width={400} />
        <button
          onClick={() => onChange("")}
          className="bg-rose-500 text-white p-1 rounded-full absolute -top-2 -right-2 shadow-sm"
          type="button"
        >
          <X className="h-4 w-4" />
        </button>
      </div>
    );
  }

  if (value && fileType !== "pdf") {
    return (
      <div className="relative h-20 w-20">
        <Image src={value} alt="uploaded image" className="rounded-full" fill />
        <button
          onClick={() => onChange("")}
          className="bg-rose-500 text-white p-1 rounded-full absolute top-0 right-0 shadow-sm"
          type="button"
        >
          <X className="h-4 w-4" />
        </button>
      </div>
    );
  }

  if (value && fileType === "pdf") {
    return (
      <div className="relative flex items-center p-2 mt-2 rounded-md bg-zinc-100 dark:bg-zinc-900">
        <FileIcon className="h-10 w-10 fill-indigo-200 stroke-indigo-400" />
        <a
          href={value}
          target="_blank"
          rel="noreferrer noopener"
          className="ml-2 text-sm text-indigo-500 dark:text-indigo-400 hover:underline"
        >
          {value}
        </a>

        <button
          onClick={() => onChange("")}
          className="bg-rose-500 text-white p-1 rounded-full absolute -top-2 -right-2 shadow-sm"
          type="button"
        >
          <X className="h-4 w-4" />
        </button>
      </div>
    );
  }

  return (
    <div className="w-20 flex items-center">
      <UploadDropzone
        className="border-zinc-500 ut-button:bg-indigo-500 ut-button:ut-uploading:bg-indigo-500/70 after:ut-button:ut-uploading:bg-indigo-500 ut-label:text-indigo-500 hover:ut-label:text-indigo-500/70"
        endpoint={endpoint}
        onClientUploadComplete={(res) => {
          onChange(res?.[0].url);
        }}
        onUploadError={(error) => {
          console.error("File Upload Error: ", error);
        }}
      />
    </div>
  );
}

export default ImageUpload;

```
### page.tsx
```tsx
<ImageUpload
            endpoint="postImage"
            value={formData.imageUrl}
            onChange={(url) => handleChange('imageUrl', url)}
          />
```