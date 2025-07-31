# cruds
```ts
npm install lucide-react
npx shadcn@latest add label switch alert-dialog dialog button
```

### app/api/item/route.ts
```ts
/* eslint-disable @typescript-eslint/no-unused-vars */
// app/api/item/route.ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import { getUserId } from '@/actions/user.action';


export async function POST(req: Request) {
  const userId = await getUserId();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  const body = await req.json();
  const { name, quantity, price, inStock, status } = body;

  try {
    const item = await prisma.item.create({
      data: {
        name,
        quantity,
        price,
        inStock,
        status,
        userId,
      },
    });
    return NextResponse.json(item);
  } catch (error) {
    return NextResponse.json({ error: 'Error creating item' }, { status: 500 });
  }
}

export async function GET() {
  // const userId = await getUserId();
  // if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  const items = await prisma.item.findMany({
    // where: { userId },
    orderBy: { dateAdded: 'desc' },
  });

  return NextResponse.json(items);
}

```
### app\api\item\[id]\route.ts
```ts
/* eslint-disable @typescript-eslint/no-unused-vars */
// app/api/item/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import { getUserId } from '@/actions/user.action';


export async function GET(req: NextRequest, { params }: { params: { id: string } }) {
  const userId = await getUserId();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  const item = await prisma.item.findFirst({
    where: { id: params.id, userId },
  });

  if (!item) return NextResponse.json({ error: 'Not found' }, { status: 404 });
  return NextResponse.json(item);
}

export async function PUT(req: NextRequest, { params }: { params: { id: string } }) {
  const userId = await getUserId();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  const body = await req.json();

  try {
    const updated = await prisma.item.updateMany({
      where: { id: params.id, userId },
      data: {
        ...body,
        ...(body.price && { price: body.price }),
      },
    });

    return NextResponse.json(updated);
  } catch (error) {
    return NextResponse.json({ error: 'Update failed' }, { status: 500 });
  }
}

export async function DELETE(_: NextRequest, { params }: { params: { id: string } }) {
  const userId = await getUserId();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

  try {
    await prisma.item.deleteMany({
      where: { id: params.id, userId },
    });
    return NextResponse.json({ success: true });
  } catch (error) {
    return NextResponse.json({ error: 'Delete failed' }, { status: 500 });
  }
}
```
### page.tsx
```tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
'use client'

import { useEffect, useMemo, useState } from 'react'
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
import { ArrowUp, ArrowDown } from 'lucide-react'
import { Label } from '@/components/ui/label'
import { Switch } from '@/components/ui/switch'
import { toast } from 'sonner'

interface Item {
  id: string
  name: string
  quantity: number
  price: string
  dateAdded: string
  inStock: boolean
  status: 'ACTIVE' | 'INACTIVE' | 'PENDING'
  userId: string
}

const defaultForm = {
  name: '',
  quantity: 0,
  price: '0.00',
  dateAdded: new Date().toISOString(),
  inStock: false,
  status: 'ACTIVE' as 'ACTIVE' | 'INACTIVE' | 'PENDING',
}

const Page = () => {
  const [form, setForm] = useState(defaultForm)
  const [data, setData] = useState<Item[]>([])
  const [editingId, setEditingId] = useState<string | null>(null)
  const [isDialogOpen, setIsDialogOpen] = useState(false)
  const [pendingDeleteId, setPendingDeleteId] = useState<string | null>(null)
  const [selectedItem, setSelectedItem] = useState<Item | null>(null)
  const [search, setSearch] = useState('')
  const [sortKey, setSortKey] = useState<'name' | 'quantity' | 'price' | 'dateAdded'>('name')
  const [sortOrder, setSortOrder] = useState<'asc' | 'desc'>('asc')
  const [loading, setLoading] = useState(false)

  const fetchData = async () => {
    try {
      setLoading(true)
      const res = await fetch('/api/item')
      const json = await res.json()
  
      console.log('Fetched items:', json)
  
      // Handle response shape
      const items = Array.isArray(json) ? json : Array.isArray(json.data) ? json.data : []
  
      setData(items)
    } catch (error) {
      toast.error('Failed to fetch items')
      console.error('Fetch error:', error)
    } finally {
      setLoading(false)
    }
  }
  

  useEffect(() => {
    fetchData()
  }, [])

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    const method = editingId ? 'PUT' : 'POST'
    const url = editingId ? `/api/item/${editingId}` : '/api/item'

    const res = await fetch(url, {
      method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form),
    })

    if (res.ok) {
      toast.success(editingId ? 'Item updated' : 'Item created')
      setForm(defaultForm)
      setEditingId(null)
      setIsDialogOpen(false)
      fetchData()
    } else {
      const err = await res.json()
      toast.error(`Error: ${err.error}`)
    }
  }

  const confirmDelete = async () => {
    if (!pendingDeleteId) return

    const res = await fetch(`/api/item/${pendingDeleteId}`, {
      method: 'DELETE',
    })

    if (res.ok) {
      toast.success('Item deleted successfully')
      fetchData()
    } else {
      toast.error('Delete failed')
    }

    setPendingDeleteId(null)
  }

  const handleEdit = (item: Item) => {
    setForm({
      name: item.name,
      quantity: item.quantity,
      price: item.price,
      dateAdded: item.dateAdded,
      inStock: item.inStock,
      status: item.status,
    })
    setEditingId(item.id)
    setIsDialogOpen(true)
  }

  const sortedAndFilteredData = useMemo(() => {
    if (!Array.isArray(data)) return []
  
    const filtered = data.filter(item =>
      item.name.toLowerCase().includes(search.toLowerCase()) ||
      item.price.includes(search) ||
      item.status.toLowerCase().includes(search.toLowerCase())
    )
  
    const sorted = [...filtered].sort((a, b) => {
      let aVal: any = a[sortKey]
      let bVal: any = b[sortKey]
  
      if (sortKey === 'price') {
        aVal = parseFloat(aVal)
        bVal = parseFloat(bVal)
      }
  
      if (sortKey === 'dateAdded') {
        aVal = new Date(aVal).getTime()
        bVal = new Date(bVal).getTime()
      }
  
      if (aVal < bVal) return sortOrder === 'asc' ? -1 : 1
      if (aVal > bVal) return sortOrder === 'asc' ? 1 : -1
      return 0
    })
  
    return sorted
  }, [data, search, sortKey, sortOrder])
  

  return (
    <div className="p-6 max-w-6xl mx-auto space-y-6">
      <Dialog open={isDialogOpen} onOpenChange={setIsDialogOpen}>
        <div className="flex justify-between items-center mb-4">
          <div className="flex items-center gap-3">
            <input
              type="text"
              placeholder="Search..."
              value={search}
              onChange={(e) => setSearch(e.target.value)}
              className="border border-gray-300 p-2 rounded-md w-[160px]"
            />
            <select
              value={sortKey}
              onChange={(e) => setSortKey(e.target.value as any)}
              className="border border-gray-300 p-2 rounded-md"
            >
              <option value="name">Name</option>
              <option value="quantity">Quantity</option>
              <option value="price">Price</option>
              <option value="dateAdded">Date</option>
            </select>
            <button
              onClick={() => setSortOrder(prev => (prev === 'asc' ? 'desc' : 'asc'))}
              className="p-2 rounded-md border border-gray-300 bg-gray-100 hover:bg-gray-200"
            >
              {sortOrder === 'asc' ? <ArrowUp className="w-4 h-4" /> : <ArrowDown className="w-4 h-4" />}
            </button>
          </div>

          <DialogTrigger asChild>
            <Button onClick={() => {
              setForm(defaultForm)
              setEditingId(null)
              setIsDialogOpen(true)
            }}>
              Create New Item
            </Button>
          </DialogTrigger>
        </div>

        <DialogContent className="max-w-xl">
          <DialogHeader>
            <DialogTitle>{editingId ? 'Edit Item' : 'Create Item'}</DialogTitle>
          </DialogHeader>

          <form onSubmit={handleSubmit} className="grid gap-4 mt-4">
            <Label htmlFor="name">Name</Label>
            <input
              placeholder="Name"
              value={form.name}
              onChange={(e) => setForm({ ...form, name: e.target.value })}
              required
              className="border p-2 w-full"
            />
            <Label htmlFor="quantity">Quantity</Label>
            <input
              type="number"
              placeholder="Quantity"
              value={form.quantity}
              onChange={(e) => setForm({ ...form, quantity: Number(e.target.value) })}
              required
              className="border p-2 w-full"
            />
            <Label htmlFor="price">Price</Label>
            <input
              type="text"
              placeholder="Price"
              value={form.price}
              onChange={(e) => setForm({ ...form, price: e.target.value })}
              required
              className="border p-2 w-full"
            />
            <Label htmlFor="date">Date</Label>
            <input
              type="datetime-local"
              value={form.dateAdded.slice(0, 16)}
              onChange={(e) => setForm({ ...form, dateAdded: e.target.value })}
              className="border p-2 w-full"
            />
            <div className="flex items-center gap-2">
              <Switch
                id="inStock"
                checked={form.inStock}
                onCheckedChange={(checked) => setForm({ ...form, inStock: checked })}
              />
              <Label htmlFor="inStock">In Stock</Label>
            </div>
            <Label htmlFor="status">Status</Label>
            <select
              value={form.status}
              onChange={(e) => setForm({ ...form, status: e.target.value as Item['status'] })}
              className="border p-2 w-full"
            >
              <option value="ACTIVE">ACTIVE</option>
              <option value="INACTIVE">INACTIVE</option>
              <option value="PENDING">PENDING</option>
            </select>

            <button
              type="submit"
              className="bg-primary text-white py-2 rounded hover:bg-primary-700"
            >
              {editingId ? 'Update' : 'Create'}
            </button>
          </form>
        </DialogContent>
      </Dialog>

      <Dialog open={!!selectedItem} onOpenChange={() => setSelectedItem(null)}>
        <DialogContent className="max-w-xl">
          <DialogHeader>
            <DialogTitle>Item Details</DialogTitle>
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

      <h2 className="text-xl font-semibold mt-8">All Items</h2>
      {loading ? (
        <p className="text-gray-500">Loading items...</p>
      ) : sortedAndFilteredData.length === 0 ? (
        <p className="text-gray-500">No items found.</p>
      ) : (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          {sortedAndFilteredData.map((item) => (
            <div key={item.id} className="p-4 border shadow rounded space-y-2">
              <div className="font-bold text-lg">{item.name}</div>
              <div className="text-sm text-gray-600">ID: {item.id}</div>
              <div className="text-sm">Qty: {item.quantity} | Price: ${item.price}</div>
              <div className="text-sm">Status: {item.status}</div>
              <div className="text-sm">In Stock: {item.inStock ? 'Yes' : 'No'}</div>
              <div className="space-x-2 flex flex-wrap">
                <Button variant="outline" onClick={() => setSelectedItem(item)}>View</Button>
                <Button variant="secondary" onClick={() => handleEdit(item)}>Edit</Button>
                <AlertDialog>
                  <AlertDialogTrigger asChild>
                    <Button variant="destructive" onClick={() => setPendingDeleteId(item.id)}>
                      Delete
                    </Button>
                  </AlertDialogTrigger>
                  <AlertDialogContent>
                    <AlertDialogHeader>
                      <AlertDialogTitle>Are you sure?</AlertDialogTitle>
                      <AlertDialogDescription>
                        This will permanently delete the item.
                      </AlertDialogDescription>
                    </AlertDialogHeader>
                    <AlertDialogFooter>
                      <AlertDialogCancel>Cancel</AlertDialogCancel>
                      <AlertDialogAction onClick={confirmDelete}>Confirm</AlertDialogAction>
                    </AlertDialogFooter>
                  </AlertDialogContent>
                </AlertDialog>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  )
}

export default Page

```