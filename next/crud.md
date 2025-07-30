# crud
```
npm install sonner
npx shadcn@latest add alert-dialog
npx shadcn@latest add dialog
```
### prisma\schema.prisma
```ts
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

// Looking for ways to speed up your queries, or scale easily with your serverless or edge functions?
// Try Prisma Accelerate: https://pris.ly/cli/accelerate-init

generator client {
  provider = "prisma-client-js"
  // output   = "../lib/generated/prisma"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model SampleModel {
  id            String   @id @default(uuid())             // UUID primary key
  stringField   String                                      // Regular string
  intField      Int                                         // Integer
  floatField    Float                                       // Float
  decimalField  Decimal   @db.Decimal(10, 2)                // PostgreSQL Decimal
  booleanField  Boolean                                     // Boolean
  dateTimeField DateTime                                    // Date/time
  jsonField     Json                                        // JSON
  bytesField    Bytes                                       // Binary data
  enumField     Role                                        // Enum
  bigIntField   BigInt    @db.BigInt                        // PostgreSQL BigInt
  textField     String    @db.Text                          // PostgreSQL TEXT
  createdAt     DateTime  @default(now())                   // Auto timestamp
  updatedAt     DateTime  @updatedAt                        // Auto update
  optionalField String?                                     // Optional field
  uniqueField   String   @unique                           // Unique constraint
  defaultString String   @default("default value")          // Default string
  isDeleted     Boolean  @default(false)                    // Soft delete pattern
}

enum Role {
  USER
  ADMIN
  GUEST
}
```
```json
{
  "stringField": "Example",
  "intField": 42,
  "floatField": 123.45,
  "decimalField": "9999.99",
  "booleanField": true,
  "dateTimeField": "2025-07-30T12:00:00.000Z",
  "jsonField": { "key": "value" },
  "bytesField": "SGVsbG8gd29ybGQ=", // "Hello world" in base64
  "enumField": "USER",
  "bigIntField": "9007199254740991",
  "textField": "This is long text.",
  "optionalField": "Optional info",
  "uniqueField": "unique_0020"
}

```
### app\api\sample\route.ts
```ts
/* eslint-disable @typescript-eslint/no-explicit-any */
// app/api/sample/route.ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';

function safeBigInt(obj: any) {
    return JSON.parse(
      JSON.stringify(obj, (_, value) =>
        typeof value === 'bigint' ? value.toString() : value
      )
    );
  }
// CREATE
export async function POST(req: Request) {
  try {
    const body = await req.json();

    const created = await prisma.sampleModel.create({
      data: {
        ...body,
        bigIntField: BigInt(body.bigIntField), // Convert to BigInt
        bytesField: Buffer.from(body.bytesField, 'base64'), // Convert base64 to bytes
      },
    });

    return NextResponse.json(safeBigInt(created));
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Create failed', details: error.message || error },
      { status: 500 }
    );
  }
}

// READ
export async function GET() {
  try {
    const all = await prisma.sampleModel.findMany();
    return NextResponse.json(safeBigInt(all));
  } catch (error: any) {
    return NextResponse.json({ error: 'Read failed', details: error.message });
  }
}
```
### app\api\sample\[id]\route.ts
```ts
/* eslint-disable @typescript-eslint/no-explicit-any */
// app/api/sample/[id]/route.ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
function safeBigInt(obj: any) {
    return JSON.parse(
      JSON.stringify(obj, (_, value) =>
        typeof value === 'bigint' ? value.toString() : value
      )
    );
  }



// GET by ID
export async function GET(
  req: Request,
  { params }: { params: { id: string } }
) {
  try {
    const { id } = params;

    const found = await prisma.sampleModel.findUnique({
      where: { id },
    });

    if (!found) {
      return NextResponse.json({ error: 'Not found' }, { status: 404 });
    }

    return NextResponse.json(safeBigInt(found));
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Get by ID failed', details: error.message },
      { status: 500 }
    );
  }
}

// UPDATE
export async function PUT(req: Request, { params }: { params: { id: string } }) {
  try {
    const body = await req.json();

    const updated = await prisma.sampleModel.update({
      where: { id: params.id },
      data: {
        ...body,
        bigIntField: BigInt(body.bigIntField),
        bytesField: Buffer.from(body.bytesField, 'base64'),
      },
    });

    return NextResponse.json(safeBigInt(updated));
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Update failed', details: error.message },
      { status: 500 }
    );
  }
}

// DELETE
export async function DELETE(_req: Request, { params }: { params: { id: string } }) {
  try {
    await prisma.sampleModel.delete({
      where: { id: params.id },
    });

    return NextResponse.json({ message: 'Deleted successfully' });
  } catch (error: any) {
    return NextResponse.json(
      { error: 'Delete failed', details: error.message },
      { status: 500 }
    );
  }
}
```
### page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
'use client'

import { useEffect, useState } from 'react'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from '@/components/ui/dialog'
import { Button } from '@/components/ui/button'
import {
  AlertDialog,
  AlertDialogTrigger,
  AlertDialogContent,
  AlertDialogHeader,
  AlertDialogFooter,
  AlertDialogTitle,
  AlertDialogDescription,
  AlertDialogCancel,
  AlertDialogAction,
} from '@/components/ui/alert-dialog'
import { toast } from 'sonner'

interface SampleModel {
  id: string
  stringField: string
  intField: number
  floatField: number
  decimalField: string
  booleanField: boolean
  dateTimeField: string
  jsonField: any
  bytesField: string
  enumField: 'USER' | 'ADMIN' | 'GUEST'
  bigIntField: string
  textField: string
  optionalField?: string
  uniqueField: string
  defaultString: string
  createdAt: string
  updatedAt: string
  isDeleted: boolean
}

const defaultForm = {
  stringField: '',
  intField: 0,
  floatField: 0,
  decimalField: '0.00',
  booleanField: false,
  dateTimeField: new Date().toISOString(),
  jsonField: {},
  bytesField: '',
  enumField: 'USER' as 'USER' | 'ADMIN' | 'GUEST',
  bigIntField: '0',
  textField: '',
  optionalField: '',
  uniqueField: '',
}

const Page = () => {
  const [form, setForm] = useState(defaultForm)
  const [jsonInput, setJsonInput] = useState(JSON.stringify(defaultForm.jsonField, null, 2))
  const [isJsonValid, setIsJsonValid] = useState(true)

  const [data, setData] = useState<SampleModel[]>([])
  const [editingId, setEditingId] = useState<string | null>(null)
  const [isDialogOpen, setIsDialogOpen] = useState(false)
  const [pendingDeleteId, setPendingDeleteId] = useState<string | null>(null)
  const [selectedItem, setSelectedItem] = useState<SampleModel | null>(null)

  const fetchData = async () => {
    const res = await fetch('/api/sample')
    const json = await res.json()
    setData(json)
  }

  useEffect(() => {
    fetchData()
  }, [])

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    if (!isJsonValid) {
      toast.error("Fix the JSON before submitting.")
      return
    }

    const method = editingId ? 'PUT' : 'POST'
    const url = editingId ? `/api/sample/${editingId}` : '/api/sample'

    const payload = {
      ...form,
      jsonField: JSON.stringify(form.jsonField),
      bytesField: btoa(form.bytesField || ''),
      bigIntField: form.bigIntField.toString(),
    }

    const res = await fetch(url, {
      method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    })

    if (res.ok) {
      setForm(defaultForm)
      setEditingId(null)
      setIsDialogOpen(false)
      fetchData()
    } else {
      const err = await res.json()
      console.error('Submit failed:', err)
      toast.error(`Error: ${err.error}`)
    }
  }

  const confirmDelete = async () => {
    if (!pendingDeleteId) return

    const res = await fetch(`/api/sample/${pendingDeleteId}`, {
      method: 'DELETE',
    })

    if (res.ok) {
      toast.success('Record deleted successfully')
      fetchData()
    } else {
      toast.error('Delete failed')
    }

    setPendingDeleteId(null)
  }

  const handleEdit = (item: SampleModel) => {
    let decodedBytes = ''
    try {
      decodedBytes = atob(item.bytesField)
    } catch {
      console.warn('Invalid base64 in bytesField:', item.bytesField)
    }

    setForm({
      stringField: item.stringField,
      intField: item.intField,
      floatField: item.floatField,
      decimalField: item.decimalField,
      booleanField: item.booleanField,
      dateTimeField: item.dateTimeField,
      jsonField: item.jsonField,
      bytesField: decodedBytes,
      enumField: item.enumField,
      bigIntField: item.bigIntField.toString(),
      textField: item.textField,
      optionalField: item.optionalField ?? '',
      uniqueField: item.uniqueField,
    })
    setJsonInput(JSON.stringify(item.jsonField, null, 2))
    setIsJsonValid(true)
    setEditingId(item.id)
    setIsDialogOpen(true)
  }

  return (
    <div className="p-6 max-w-6xl mx-auto space-y-6">
      <Dialog open={isDialogOpen} onOpenChange={setIsDialogOpen}>
        <div className="flex justify-end mb-4">
          <DialogTrigger asChild>
            <Button onClick={() => {
              setForm(defaultForm)
              setJsonInput(JSON.stringify(defaultForm.jsonField, null, 2))
              setIsJsonValid(true)
            }}>
              Create New
            </Button>
          </DialogTrigger>
        </div>

        <DialogContent className="max-w-3xl">
          <DialogHeader>
            <DialogTitle>{editingId ? 'Update Record' : 'Create Record'}</DialogTitle>
          </DialogHeader>
          <form onSubmit={handleSubmit} className="grid grid-cols-2 gap-4 mt-4">
            {Object.entries({
              stringField: 'String',
              intField: 'Integer',
              floatField: 'Float',
              decimalField: 'Decimal',
              bigIntField: 'BigInt',
              textField: 'Text',
              optionalField: 'Optional',
              uniqueField: 'Unique',
            }).map(([key, label]) => (
              <div key={key}>
                <label className="block text-sm font-medium text-gray-700 mb-1">{label}</label>
                <input
                  value={(form as any)[key]}
                  onChange={(e) => setForm({ ...form, [key]: e.target.value })}
                  className="border p-2 w-full"
                  required={key === 'stringField' || key === 'uniqueField'}
                />
              </div>
            ))}

            <div>
              <label className="block text-sm font-medium mb-1">Boolean</label>
              <input
                type="checkbox"
                checked={form.booleanField}
                onChange={(e) => setForm({ ...form, booleanField: e.target.checked })}
              />
            </div>

            <div>
              <label className="block text-sm font-medium mb-1">DateTime</label>
              <input
                type="datetime-local"
                value={form.dateTimeField.slice(0, 16)}
                onChange={(e) => setForm({ ...form, dateTimeField: e.target.value })}
                className="border p-2 w-full"
              />
            </div>

            <div className="col-span-2">
              <label className="block text-sm font-medium mb-1">JSON</label>
              <textarea
                value={jsonInput}
                onChange={(e) => {
                  setJsonInput(e.target.value)
                  try {
                    const parsed = JSON.parse(e.target.value)
                    setForm({ ...form, jsonField: parsed })
                    setIsJsonValid(true)
                  } catch {
                    setIsJsonValid(false)
                  }
                }}
                rows={4}
                className={`border p-2 w-full ${isJsonValid ? '' : 'border-red-500'}`}
              />
              {!isJsonValid && (
                <p className="text-red-500 text-sm mt-1">Invalid JSON format</p>
              )}
            </div>

            <div>
              <label className="block text-sm font-medium mb-1">Bytes</label>
              <input
                value={form.bytesField}
                onChange={(e) => setForm({ ...form, bytesField: e.target.value })}
                className="border p-2 w-full"
              />
            </div>

            <div>
              <label className="block text-sm font-medium mb-1">Enum</label>
              <select
                value={form.enumField}
                onChange={(e) => setForm({ ...form, enumField: e.target.value as any })}
                className="border p-2 w-full"
              >
                <option value="USER">USER</option>
                <option value="ADMIN">ADMIN</option>
                <option value="GUEST">GUEST</option>
              </select>
            </div>

            <button
              type="submit"
              className="col-span-2 bg-primary text-white py-2 rounded hover:bg-primary-700 disabled:opacity-50"
              disabled={!isJsonValid}
            >
              {editingId ? 'Update' : 'Create'}
            </button>
          </form>
        </DialogContent>
      </Dialog>

      {/* View Dialog */}
      <Dialog open={!!selectedItem} onOpenChange={() => setSelectedItem(null)}>
        <DialogContent className="max-w-xl">
          <DialogHeader>
            <DialogTitle>Record Details</DialogTitle>
          </DialogHeader>
          {selectedItem && (
            <div className="space-y-2 max-h-[60vh] overflow-auto text-sm">
              {Object.entries(selectedItem).map(([key, value]) => (
                <div key={key}>
                  <strong className="capitalize">{key}:</strong>{' '}
                  <span className="break-words">{typeof value === 'object' ? JSON.stringify(value) : String(value)}</span>
                </div>
              ))}
            </div>
          )}
        </DialogContent>
      </Dialog>

      <h2 className="text-xl font-semibold mt-8">All Records</h2>
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        {data.map((item) => (
          <div key={item.id} className="p-4 border shadow rounded space-y-2">
            <div className="font-bold">{item.stringField}</div>
            <div className="text-sm text-gray-600">ID: {item.id}</div>
            <div className="text-sm">BigInt: {item.bigIntField}</div>
            <div className="space-x-2 flex flex-wrap">
              <Button variant="outline" onClick={() => setSelectedItem(item)}>
                View
              </Button>
              <Button variant="secondary" onClick={() => handleEdit(item)}>
                Edit
              </Button>
              <AlertDialog>
                <AlertDialogTrigger asChild>
                  <Button
                    variant="destructive"
                    onClick={() => setPendingDeleteId(item.id)}
                  >
                    Delete
                  </Button>
                </AlertDialogTrigger>
                <AlertDialogContent>
                  <AlertDialogHeader>
                    <AlertDialogTitle>Are you sure?</AlertDialogTitle>
                    <AlertDialogDescription>
                      This will permanently delete the record.
                    </AlertDialogDescription>
                  </AlertDialogHeader>
                  <AlertDialogFooter>
                    <AlertDialogCancel>Cancel</AlertDialogCancel>
                    <AlertDialogAction onClick={confirmDelete}>
                      Confirm
                    </AlertDialogAction>
                  </AlertDialogFooter>
                </AlertDialogContent>
              </AlertDialog>
            </div>
          </div>
        ))}
      </div>
    </div>
  )
}

export default Page

```