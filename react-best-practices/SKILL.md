---
name: react-best-practices
description: React and Next.js best practices from Vercel Engineering. Use when writing, refactoring, or reviewing React components, hooks, state management, and performance patterns.
---

# React Best Practices

**Version 1.0.0**  
Vercel Engineering  
January 2026

> **Note:**  
> This document is mainly for agents and LLMs to follow when maintaining,  
> generating, or refactoring React and Next.js codebases. Humans  
> may also find it useful, but guidance here is optimized for automation  
> and consistency by AI-assisted workflows.

---

## Abstract

Comprehensive performance optimization guide for React and Next.js applications, designed for AI agents and LLMs. Contains 40+ rules across 8 categories, prioritized by impact from critical (eliminating waterfalls, reducing bundle size) to incremental (advanced patterns). Each rule includes detailed explanations, real-world examples comparing incorrect vs. correct implementations, and specific impact metrics to guide automated refactoring and code generation.

---

## Table of Contents

1. [Eliminating Waterfalls](#1-eliminating-waterfalls) — **CRITICAL**
   - 1.1 [Defer Await Until Needed](#11-defer-await-until-needed)
   - 1.2 [Dependency-Based Parallelization](#12-dependency-based-parallelization)
   - 1.3 [Prevent Waterfall Chains in API Routes](#13-prevent-waterfall-chains-in-api-routes)
   - 1.4 [Promise.all() for Independent Operations](#14-promiseall-for-independent-operations)
   - 1.5 [Strategic Suspense Boundaries](#15-strategic-suspense-boundaries)
2. [Bundle Size Optimization](#2-bundle-size-optimization) — **CRITICAL**
   - 2.1 [Avoid Barrel File Imports](#21-avoid-barrel-file-imports)
   - 2.2 [Conditional Module Loading](#22-conditional-module-loading)
   - 2.3 [Defer Non-Critical Third-Party Libraries](#23-defer-non-critical-third-party-libraries)
   - 2.4 [Dynamic Imports for Heavy Components](#24-dynamic-imports-for-heavy-components)
   - 2.5 [Preload Based on User Intent](#25-preload-based-on-user-intent)
3. [Server-Side Performance](#3-server-side-performance) — **HIGH**
   - 3.1 [Authenticate Server Actions Like API Routes](#31-authenticate-server-actions-like-api-routes)
   - 3.2 [Avoid Duplicate Serialization in RSC Props](#32-avoid-duplicate-serialization-in-rsc-props)
   - 3.3 [Cross-Request LRU Caching](#33-cross-request-lru-caching)
   - 3.4 [Minimize Serialization at RSC Boundaries](#34-minimize-serialization-at-rsc-boundaries)
   - 3.5 [Parallel Data Fetching with Component Composition](#35-parallel-data-fetching-with-component-composition)
   - 3.6 [Per-Request Deduplication with React.cache()](#36-per-request-deduplication-with-reactcache)
   - 3.7 [Use after() for Non-Blocking Operations](#37-use-after-for-non-blocking-operations)
4. [Client-Side Data Fetching](#4-client-side-data-fetching) — **MEDIUM-HIGH**
   - 4.1 [Deduplicate Global Event Listeners](#41-deduplicate-global-event-listeners)
   - 4.2 [Use Passive Event Listeners for Scrolling Performance](#42-use-passive-event-listeners-for-scrolling-performance)
   - 4.3 [Use SWR for Automatic Deduplication](#43-use-swr-for-automatic-deduplication)
   - 4.4 [Version and Minimize localStorage Data](#44-version-and-minimize-localstorage-data)
5. [Re-render Optimization](#5-re-render-optimization) — **MEDIUM**
   - 5.1 [Calculate Derived State During Rendering](#51-calculate-derived-state-during-rendering)
   - 5.2 [Defer State Reads to Usage Point](#52-defer-state-reads-to-usage-point)
   - 5.3 [Do not wrap a simple expression with a primitive result type in useMemo](#53-do-not-wrap-a-simple-expression-with-a-primitive-result-type-in-usememo)
   - 5.4 [Extract Default Non-primitive Parameter Value from Memoized Component to Constant](#54-extract-default-non-primitive-parameter-value-from-memoized-component-to-constant)
   - 5.5 [Extract to Memoized Components](#55-extract-to-memoized-components)
   - 5.6 [Narrow Effect Dependencies](#56-narrow-effect-dependencies)
   - 5.7 [Put Interaction Logic in Event Handlers](#57-put-interaction-logic-in-event-handlers)
   - 5.8 [Subscribe to Derived State](#58-subscribe-to-derived-state)
   - 5.9 [Use Functional setState Updates](#59-use-functional-setstate-updates)
   - 5.10 [Use Lazy State Initialization](#510-use-lazy-state-initialization)
   - 5.11 [Use Transitions for Non-Urgent Updates](#511-use-transitions-for-non-urgent-updates)
   - 5.12 [Use useRef for Transient Values](#512-use-useref-for-transient-values)
6. [Rendering Performance](#6-rendering-performance) — **MEDIUM**
   - 6.1 [Animate SVG Wrapper Instead of SVG Element](#61-animate-svg-wrapper-instead-of-svg-element)
   - 6.2 [CSS content-visibility for Long Lists](#62-css-content-visibility-for-long-lists)
   - 6.3 [Hoist Static JSX Elements](#63-hoist-static-jsx-elements)
   - 6.4 [Optimize SVG Precision](#64-optimize-svg-precision)
   - 6.5 [Prevent Hydration Mismatch Without Flickering](#65-prevent-hydration-mismatch-without-flickering)
   - 6.6 [Suppress Expected Hydration Mismatches](#66-suppress-expected-hydration-mismatches)
   - 6.7 [Use Activity Component for Show/Hide](#67-use-activity-component-for-showhide)
   - 6.8 [Use Explicit Conditional Rendering](#68-use-explicit-conditional-rendering)
   - 6.9 [Use useTransition Over Manual Loading States](#69-use-usetransition-over-manual-loading-states)
7. [JavaScript Performance](#7-javascript-performance) — **LOW-MEDIUM**
   - 7.1 [Avoid Layout Thrashing](#71-avoid-layout-thrashing)
   - 7.2 [Build Index Maps for Repeated Lookups](#72-build-index-maps-for-repeated-lookups)
   - 7.3 [Cache Property Access in Loops](#73-cache-property-access-in-loops)
   - 7.4 [Cache Repeated Function Calls](#74-cache-repeated-function-calls)
   - 7.5 [Cache Storage API Calls](#75-cache-storage-api-calls)
   - 7.6 [Combine Multiple Array Iterations](#76-combine-multiple-array-iterations)
   - 7.7 [Early Length Check for Array Comparisons](#77-early-length-check-for-array-comparisons)
   - 7.8 [Early Return from Functions](#78-early-return-from-functions)
   - 7.9 [Hoist RegExp Creation](#79-hoist-regexp-creation)
   - 7.10 [Use Loop for Min/Max Instead of Sort](#710-use-loop-for-minmax-instead-of-sort)
   - 7.11 [Use Set/Map for O(1) Lookups](#711-use-setmap-for-o1-lookups)
   - 7.12 [Use toSorted() Instead of sort() for Immutability](#712-use-tosorted-instead-of-sort-for-immutability)
8. [Advanced Patterns](#8-advanced-patterns) — **LOW**
   - 8.1 [Initialize App Once, Not Per Mount](#81-initialize-app-once-not-per-mount)
   - 8.2 [Store Event Handlers in Refs](#82-store-event-handlers-in-refs)
   - 8.3 [useEffectEvent for Stable Callback Refs](#83-useeffectevent-for-stable-callback-refs)

---

## 1. Eliminating Waterfalls

**Impact: CRITICAL**

Waterfalls are the #1 performance killer. Each sequential await adds full network latency. Eliminating them yields the largest gains.

### 1.1 Defer Await Until Needed

**Impact: HIGH (avoids blocking unused code paths)**

Move `await` operations into the branches where they're actually used to avoid blocking code paths that don't need them.

**Incorrect: blocks both branches**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  const userData = await fetchUserData(userId)
  
  if (skipProcessing) {
    // Returns immediately but still waited for userData
    return { skipped: true }
  }
  
  // Only this branch uses userData
  return processUserData(userData)
}
```

**Correct: only blocks when needed**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  if (skipProcessing) {
    // Returns immediately without waiting
    return { skipped: true }
  }
  
  // Fetch only when needed
  const userData = await fetchUserData(userId)
  return processUserData(userData)
}
```

### 1.2 Dependency-Based Parallelization

**Impact: CRITICAL (2-10× improvement)**

For operations with partial dependencies, use `better-all` to maximize parallelism.

**Incorrect: profile waits for config unnecessarily**

```typescript
const [user, config] = await Promise.all([
  fetchUser(),
  fetchConfig()
])
const profile = await fetchProfile(user.id)
```

**Correct: config and profile run in parallel**

```typescript
import { all } from 'better-all'

const { user, config, profile } = await all({
  async user() { return fetchUser() },
  async config() { return fetchConfig() },
  async profile() {
    return fetchProfile((await this.$.user).id)
  }
})
```

Reference: [https://github.com/shuding/better-all](https://github.com/shuding/better-all)

### 1.3 Prevent Waterfall Chains in API Routes

**Impact: CRITICAL (2-10× improvement)**

Start independent operations immediately, even if you don't await them yet.

**Incorrect: config waits for auth, data waits for both**

```typescript
export async function GET(request: Request) {
  const session = await auth()
  const config = await fetchConfig()
  const data = await fetchData(session.user.id)
  return Response.json({ data, config })
}
```

**Correct: auth and config start immediately**

```typescript
export async function GET(request: Request) {
  const sessionPromise = auth()
  const configPromise = fetchConfig()
  const session = await sessionPromise
  const [config, data] = await Promise.all([
    configPromise,
    fetchData(session.user.id)
  ])
  return Response.json({ data, config })
}
```

### 1.4 Promise.all() for Independent Operations

**Impact: CRITICAL (2-10× improvement)**

When async operations have no interdependencies, execute them concurrently.

**Incorrect: sequential execution, 3 round trips**

```typescript
const user = await fetchUser()
const posts = await fetchPosts()
const comments = await fetchComments()
```

**Correct: parallel execution, 1 round trip**

```typescript
const [user, posts, comments] = await Promise.all([
  fetchUser(),
  fetchPosts(),
  fetchComments()
])
```

### 1.5 Strategic Suspense Boundaries

**Impact: HIGH (faster initial paint)**

Use Suspense boundaries to show the wrapper UI faster while data loads.

**Incorrect: wrapper blocked by data fetching**

```tsx
async function Page() {
  const data = await fetchData() // Blocks entire page
  
  return (
    <div>
      <div>Sidebar</div>
      <div>Header</div>
      <div>
        <DataDisplay data={data} />
      </div>
      <div>Footer</div>
    </div>
  )
}
```

**Correct: wrapper shows immediately, data streams in**

```tsx
function Page() {
  return (
    <div>
      <div>Sidebar</div>
      <div>Header</div>
      <div>
        <Suspense fallback={<Skeleton />}>
          <DataDisplay />
        </Suspense>
      </div>
      <div>Footer</div>
    </div>
  )
}

async function DataDisplay() {
  const data = await fetchData() // Only blocks this component
  return <div>{data.content}</div>
}
```

---

## 2. Bundle Size Optimization

**Impact: CRITICAL**

Reducing initial bundle size improves Time to Interactive and Largest Contentful Paint.

### 2.1 Avoid Barrel File Imports

**Impact: CRITICAL (200-800ms import cost, slow builds)**

Import directly from source files instead of barrel files.

**Incorrect: imports entire library**

```tsx
import { Check, X, Menu } from 'lucide-react'
// Loads 1,583 modules, takes ~2.8s extra in dev

import { Button, TextField } from '@mui/material'
// Loads 2,225 modules, takes ~4.2s extra in dev
```

**Correct: imports only what you need**

```tsx
import Check from 'lucide-react/dist/esm/icons/check'
import X from 'lucide-react/dist/esm/icons/x'
import Menu from 'lucide-react/dist/esm/icons/menu'
// Loads only 3 modules (~2KB vs ~1MB)

import Button from '@mui/material/Button'
import TextField from '@mui/material/TextField'
```

**Alternative: Next.js 13.5+**

```js
// next.config.js - use optimizePackageImports
module.exports = {
  experimental: {
    optimizePackageImports: ['lucide-react', '@mui/material']
  }
}
```

Reference: [https://vercel.com/blog/how-we-optimized-package-imports-in-next-js](https://vercel.com/blog/how-we-optimized-package-imports-in-next-js)

### 2.2 Conditional Module Loading

**Impact: HIGH (loads large data only when needed)**

Load large data or modules only when a feature is activated.

```tsx
function AnimationPlayer({ enabled, setEnabled }: Props) {
  const [frames, setFrames] = useState<Frame[] | null>(null)

  useEffect(() => {
    if (enabled && !frames && typeof window !== 'undefined') {
      import('./animation-frames.js')
        .then(mod => setFrames(mod.frames))
        .catch(() => setEnabled(false))
    }
  }, [enabled, frames, setEnabled])

  if (!frames) return <Skeleton />
  return <Canvas frames={frames} />
}
```

### 2.3 Defer Non-Critical Third-Party Libraries

**Impact: MEDIUM (loads after hydration)**

Analytics, logging, and error tracking don't block user interaction. Load them after hydration.

**Incorrect: blocks initial bundle**

```tsx
import { Analytics } from '@vercel/analytics/react'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  )
}
```

**Correct: loads after hydration**

```tsx
import dynamic from 'next/dynamic'

const Analytics = dynamic(
  () => import('@vercel/analytics/react').then(m => m.Analytics),
  { ssr: false }
)

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  )
}
```

### 2.4 Dynamic Imports for Heavy Components

**Impact: CRITICAL (directly affects TTI and LCP)**

Use `next/dynamic` to lazy-load large components not needed on initial render.

**Incorrect: Monaco bundles with main chunk ~300KB**

```tsx
import { MonacoEditor } from './monaco-editor'

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />
}
```

**Correct: Monaco loads on demand**

```tsx
import dynamic from 'next/dynamic'

const MonacoEditor = dynamic(
  () => import('./monaco-editor').then(m => m.MonacoEditor),
  { ssr: false }
)

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />
}
```

### 2.5 Preload Based on User Intent

**Impact: MEDIUM (reduces perceived latency)**

Preload heavy bundles before they're needed.

```tsx
function EditorButton({ onClick }: { onClick: () => void }) {
  const preload = () => {
    if (typeof window !== 'undefined') {
      void import('./monaco-editor')
    }
  }

  return (
    <button
      onMouseEnter={preload}
      onFocus={preload}
      onClick={onClick}
    >
      Open Editor
    </button>
  )
}
```

---

## 3. Server-Side Performance

**Impact: HIGH**

Optimizing server-side rendering and data fetching eliminates server-side waterfalls and reduces response times.

### 3.1 Authenticate Server Actions Like API Routes

**Impact: CRITICAL (prevents unauthorized access to server mutations)**

Server Actions are exposed as public endpoints. Always verify authentication inside each Server Action.

**Incorrect: no authentication check**

```typescript
'use server'

export async function deleteUser(userId: string) {
  // Anyone can call this! No auth check
  await db.user.delete({ where: { id: userId } })
  return { success: true }
}
```

**Correct: authentication inside the action**

```typescript
'use server'

import { verifySession } from '@/lib/auth'
import { unauthorized } from '@/lib/errors'

export async function deleteUser(userId: string) {
  const session = await verifySession()
  
  if (!session) {
    throw unauthorized('Must be logged in')
  }
  
  if (session.user.role !== 'admin' && session.user.id !== userId) {
    throw unauthorized('Cannot delete other users')
  }
  
  await db.user.delete({ where: { id: userId } })
  return { success: true }
}
```

### 3.2 Avoid Duplicate Serialization in RSC Props

**Impact: LOW (reduces network payload)**

RSC→client serialization deduplicates by object reference, not value. Do transformations in client, not server.

**Incorrect: duplicates array**

```tsx
// RSC: sends 6 strings (2 arrays × 3 items)
<ClientList usernames={usernames} usernamesOrdered={usernames.toSorted()} />
```

**Correct: sends 3 strings**

```tsx
// RSC: send once
<ClientList usernames={usernames} />

// Client: transform there
'use client'
const sorted = useMemo(() => [...usernames].sort(), [usernames])
```

### 3.3 Cross-Request LRU Caching

**Impact: HIGH (caches across requests)**

`React.cache()` only works within one request. For data shared across sequential requests, use an LRU cache.

```typescript
import { LRUCache } from 'lru-cache'

const cache = new LRUCache<string, any>({
  max: 1000,
  ttl: 5 * 60 * 1000  // 5 minutes
})

export async function getUser(id: string) {
  const cached = cache.get(id)
  if (cached) return cached

  const user = await db.user.findUnique({ where: { id } })
  cache.set(id, user)
  return user
}
```

Reference: [https://github.com/isaacs/node-lru-cache](https://github.com/isaacs/node-lru-cache)

### 3.4 Minimize Serialization at RSC Boundaries

**Impact: HIGH (reduces data transfer size)**

Only pass fields that the client actually uses.

**Incorrect: serializes all 50 fields**

```tsx
async function Page() {
  const user = await fetchUser()  // 50 fields
  return <Profile user={user} />
}

'use client'
function Profile({ user }: { user: User }) {
  return <div>{user.name}</div>  // uses 1 field
}
```

**Correct: serializes only 1 field**

```tsx
async function Page() {
  const user = await fetchUser()
  return <Profile name={user.name} />
}

'use client'
function Profile({ name }: { name: string }) {
  return <div>{name}</div>
}
```

### 3.5 Parallel Data Fetching with Component Composition

**Impact: CRITICAL (eliminates server-side waterfalls)**

React Server Components execute sequentially within a tree. Restructure with composition to parallelize.

**Incorrect: Sidebar waits for Page's fetch**

```tsx
export default async function Page() {
  const header = await fetchHeader()
  return (
    <div>
      <div>{header}</div>
      <Sidebar />
    </div>
  )
}

async function Sidebar() {
  const items = await fetchSidebarItems()
  return <nav>{items.map(renderItem)}</nav>
}
```

**Correct: both fetch simultaneously**

```tsx
async function Header() {
  const data = await fetchHeader()
  return <div>{data}</div>
}

async function Sidebar() {
  const items = await fetchSidebarItems()
  return <nav>{items.map(renderItem)}</nav>
}

export default function Page() {
  return (
    <div>
      <Header />
      <Sidebar />
    </div>
  )
}
```

### 3.6 Per-Request Deduplication with React.cache()

**Impact: MEDIUM (deduplicates within request)**

Use `React.cache()` for server-side request deduplication.

```typescript
import { cache } from 'react'

export const getCurrentUser = cache(async () => {
  const session = await auth()
  if (!session?.user?.id) return null
  return await db.user.findUnique({
    where: { id: session.user.id }
  })
})
```

Reference: [https://react.dev/reference/react/cache](https://react.dev/reference/react/cache)

### 3.7 Use after() for Non-Blocking Operations

**Impact: MEDIUM (faster response times)**

Use Next.js's `after()` to schedule work that should execute after a response is sent.

**Incorrect: blocks response**

```tsx
export async function POST(request: Request) {
  await updateDatabase(request)
  
  // Logging blocks the response
  const userAgent = request.headers.get('user-agent') || 'unknown'
  await logUserAction({ userAgent })
  
  return new Response(JSON.stringify({ status: 'success' }))
}
```

**Correct: non-blocking**

```tsx
import { after } from 'next/server'

export async function POST(request: Request) {
  await updateDatabase(request)
  
  // Log after response is sent
  after(async () => {
    const userAgent = (await headers()).get('user-agent') || 'unknown'
    logUserAction({ userAgent })
  })
  
  return new Response(JSON.stringify({ status: 'success' }))
}
```

Reference: [https://nextjs.org/docs/app/api-reference/functions/after](https://nextjs.org/docs/app/api-reference/functions/after)

---

## 4. Client-Side Data Fetching

**Impact: MEDIUM-HIGH**

Automatic deduplication and efficient data fetching patterns reduce redundant network requests.

### 4.1 Deduplicate Global Event Listeners

**Impact: LOW (single listener for N components)**

Use `useSWRSubscription()` to share global event listeners across component instances.

### 4.2 Use Passive Event Listeners for Scrolling Performance

**Impact: MEDIUM (eliminates scroll delay)**

Add `{ passive: true }` to touch and wheel event listeners.

**Incorrect:**

```typescript
document.addEventListener('touchstart', handleTouch)
document.addEventListener('wheel', handleWheel)
```

**Correct:**

```typescript
document.addEventListener('touchstart', handleTouch, { passive: true })
document.addEventListener('wheel', handleWheel, { passive: true })
```

### 4.3 Use SWR for Automatic Deduplication

**Impact: MEDIUM-HIGH (automatic deduplication)**

SWR enables request deduplication, caching, and revalidation.

**Incorrect: no deduplication**

```tsx
function UserList() {
  const [users, setUsers] = useState([])
  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(setUsers)
  }, [])
}
```

**Correct: multiple instances share one request**

```tsx
import useSWR from 'swr'

function UserList() {
  const { data: users } = useSWR('/api/users', fetcher)
}
```

Reference: [https://swr.vercel.app](https://swr.vercel.app)

### 4.4 Version and Minimize localStorage Data

**Impact: MEDIUM (prevents schema conflicts)**

Add version prefix to keys and store only needed fields.

```typescript
const VERSION = 'v2'

function saveConfig(config: { theme: string; language: string }) {
  try {
    localStorage.setItem(`userConfig:${VERSION}`, JSON.stringify(config))
  } catch {
    // Throws in incognito/private browsing, quota exceeded, or disabled
  }
}

function loadConfig() {
  try {
    const data = localStorage.getItem(`userConfig:${VERSION}`)
    return data ? JSON.parse(data) : null
  } catch {
    return null
  }
}
```

---

## 5. Re-render Optimization

**Impact: MEDIUM**

Reducing unnecessary re-renders minimizes wasted computation and improves UI responsiveness.

### 5.1 Calculate Derived State During Rendering

**Impact: MEDIUM (avoids redundant renders)**

If a value can be computed from current props/state, derive it during render.

**Incorrect: redundant state and effect**

```tsx
function Form() {
  const [firstName, setFirstName] = useState('First')
  const [lastName, setLastName] = useState('Last')
  const [fullName, setFullName] = useState('')

  useEffect(() => {
    setFullName(firstName + ' ' + lastName)
  }, [firstName, lastName])

  return <p>{fullName}</p>
}
```

**Correct: derive during render**

```tsx
function Form() {
  const [firstName, setFirstName] = useState('First')
  const [lastName, setLastName] = useState('Last')
  const fullName = firstName + ' ' + lastName

  return <p>{fullName}</p>
}
```

Reference: [https://react.dev/learn/you-might-not-need-an-effect](https://react.dev/learn/you-might-not-need-an-effect)

### 5.2 Defer State Reads to Usage Point

**Impact: MEDIUM (avoids unnecessary subscriptions)**

Don't subscribe to dynamic state if you only read it inside callbacks.

**Incorrect: subscribes to all searchParams changes**

```tsx
function ShareButton({ chatId }: { chatId: string }) {
  const searchParams = useSearchParams()

  const handleShare = () => {
    const ref = searchParams.get('ref')
    shareChat(chatId, { ref })
  }

  return <button onClick={handleShare}>Share</button>
}
```

**Correct: reads on demand, no subscription**

```tsx
function ShareButton({ chatId }: { chatId: string }) {
  const handleShare = () => {
    const params = new URLSearchParams(window.location.search)
    const ref = params.get('ref')
    shareChat(chatId, { ref })
  }

  return <button onClick={handleShare}>Share</button>
}
```

### 5.3 Do not wrap a simple expression with a primitive result type in useMemo

**Impact: LOW-MEDIUM**

When an expression is simple and has a primitive result type, do not wrap it in `useMemo`.

**Incorrect:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = useMemo(() => {
    return user.isLoading || notifications.isLoading
  }, [user.isLoading, notifications.isLoading])

  if (isLoading) return <Skeleton />
}
```

**Correct:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = user.isLoading || notifications.isLoading

  if (isLoading) return <Skeleton />
}
```

### 5.4 Extract Default Non-primitive Parameter Value from Memoized Component to Constant

**Impact: MEDIUM (restores memoization)**

**Incorrect: `onClick` has different values on every rerender**

```tsx
const UserAvatar = memo(function UserAvatar({ onClick = () => {} }: Props) {
  // ...
})
```

**Correct: stable default value**

```tsx
const NOOP = () => {};

const UserAvatar = memo(function UserAvatar({ onClick = NOOP }: Props) {
  // ...
})
```

### 5.5 Extract to Memoized Components

**Impact: MEDIUM (enables early returns)**

Extract expensive work into memoized components.

### 5.6 Narrow Effect Dependencies

**Impact: LOW (minimizes effect re-runs)**

Specify primitive dependencies instead of objects.

**Incorrect: re-runs on any user field change**

```tsx
useEffect(() => {
  console.log(user.id)
}, [user])
```

**Correct: re-runs only when id changes**

```tsx
useEffect(() => {
  console.log(user.id)
}, [user.id])
```

### 5.7 Put Interaction Logic in Event Handlers

**Impact: MEDIUM (avoids effect re-runs)**

If a side effect is triggered by a specific user action, run it in that event handler.

### 5.8 Subscribe to Derived State

**Impact: MEDIUM (reduces re-render frequency)**

Subscribe to derived boolean state instead of continuous values.

**Incorrect: re-renders on every pixel change**

```tsx
function Sidebar() {
  const width = useWindowWidth()  // updates continuously
  const isMobile = width < 768
  return <nav className={isMobile ? 'mobile' : 'desktop'} />
}
```

**Correct: re-renders only when boolean changes**

```tsx
function Sidebar() {
  const isMobile = useMediaQuery('(max-width: 767px)')
  return <nav className={isMobile ? 'mobile' : 'desktop'} />
}
```

### 5.9 Use Functional setState Updates

**Impact: MEDIUM (prevents stale closures)**

Use the functional update form of setState when updating state based on current state.

**Incorrect: requires state as dependency**

```tsx
const addItems = useCallback((newItems: Item[]) => {
  setItems([...items, ...newItems])
}, [items])  // items dependency causes recreations
```

**Correct: stable callbacks, no stale closures**

```tsx
const addItems = useCallback((newItems: Item[]) => {
  setItems(curr => [...curr, ...newItems])
}, [])  // No dependencies needed
```

### 5.10 Use Lazy State Initialization

**Impact: MEDIUM (wasted computation on every render)**

Pass a function to `useState` for expensive initial values.

**Incorrect: runs on every render**

```tsx
const [searchIndex, setSearchIndex] = useState(buildSearchIndex(items))
```

**Correct: runs only once**

```tsx
const [searchIndex, setSearchIndex] = useState(() => buildSearchIndex(items))
```

### 5.11 Use Transitions for Non-Urgent Updates

**Impact: MEDIUM (maintains UI responsiveness)**

Mark frequent, non-urgent state updates as transitions.

```tsx
import { startTransition } from 'react'

function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0)
  useEffect(() => {
    const handler = () => {
      startTransition(() => setScrollY(window.scrollY))
    }
    window.addEventListener('scroll', handler, { passive: true })
    return () => window.removeEventListener('scroll', handler)
  }, [])
}
```

### 5.12 Use useRef for Transient Values

**Impact: MEDIUM (avoids unnecessary re-renders)**

When a value changes frequently and you don't want a re-render on every update, store it in `useRef`.

---

## 6. Rendering Performance

**Impact: MEDIUM**

Optimizing the rendering process reduces the work the browser needs to do.

### 6.1 Animate SVG Wrapper Instead of SVG Element

**Impact: LOW (enables hardware acceleration)**

Wrap SVG in a `<div>` and animate the wrapper instead.

**Incorrect: animating SVG directly**

```tsx
<svg className="animate-spin">...</svg>
```

**Correct: animating wrapper div**

```tsx
<div className="animate-spin">
  <svg>...</svg>
</div>
```

### 6.2 CSS content-visibility for Long Lists

**Impact: HIGH (faster initial render)**

Apply `content-visibility: auto` to defer off-screen rendering.

```css
.message-item {
  content-visibility: auto;
  contain-intrinsic-size: 0 80px;
}
```

### 6.3 Hoist Static JSX Elements

**Impact: LOW (avoids re-creation)**

Extract static JSX outside components.

**Incorrect: recreates element every render**

```tsx
function Container() {
  return <div>{loading && <LoadingSkeleton />}</div>
}
```

**Correct: reuses same element**

```tsx
const loadingSkeleton = <div className="animate-pulse h-20 bg-gray-200" />

function Container() {
  return <div>{loading && loadingSkeleton}</div>
}
```

### 6.4 Optimize SVG Precision

**Impact: LOW (reduces file size)**

Reduce SVG coordinate precision.

```bash
npx svgo --precision=1 --multipass icon.svg
```

### 6.5 Prevent Hydration Mismatch Without Flickering

**Impact: MEDIUM (avoids visual flicker)**

Inject a synchronous script that updates the DOM before React hydrates.

### 6.6 Suppress Expected Hydration Mismatches

**Impact: LOW-MEDIUM**

For expected mismatches (random IDs, dates), use `suppressHydrationWarning`.

```tsx
function Timestamp() {
  return (
    <span suppressHydrationWarning>
      {new Date().toLocaleString()}
    </span>
  )
}
```

### 6.7 Use Activity Component for Show/Hide

**Impact: MEDIUM (preserves state/DOM)**

Use React's `<Activity>` to preserve state/DOM for expensive components.

```tsx
import { Activity } from 'react'

function Dropdown({ isOpen }: Props) {
  return (
    <Activity mode={isOpen ? 'visible' : 'hidden'}>
      <ExpensiveMenu />
    </Activity>
  )
}
```

### 6.8 Use Explicit Conditional Rendering

**Impact: LOW (prevents rendering 0 or NaN)**

Use explicit ternary operators instead of `&&` when the condition can be `0` or `NaN`.

**Incorrect: renders "0" when count is 0**

```tsx
{count && <span className="badge">{count}</span>}
```

**Correct: renders nothing when count is 0**

```tsx
{count > 0 ? <span className="badge">{count}</span> : null}
```

### 6.9 Use useTransition Over Manual Loading States

**Impact: LOW (reduces re-renders)**

Use `useTransition` instead of manual `useState` for loading states.

Reference: [https://react.dev/reference/react/useTransition](https://react.dev/reference/react/useTransition)

---

## 7. JavaScript Performance

**Impact: LOW-MEDIUM**

Micro-optimizations for hot paths can add up to meaningful improvements.

### 7.1 Avoid Layout Thrashing

**Impact: MEDIUM**

Avoid interleaving style writes with layout reads.

**Incorrect: interleaved reads and writes force reflows**

```typescript
element.style.width = '100px'
const width = element.offsetWidth  // Forces reflow
element.style.height = '200px'
const height = element.offsetHeight  // Forces another reflow
```

**Correct: batch writes, then read once**

```typescript
element.style.width = '100px'
element.style.height = '200px'
const { width, height } = element.getBoundingClientRect()
```

### 7.2 Build Index Maps for Repeated Lookups

**Impact: LOW-MEDIUM (1M ops to 2K ops)**

Multiple `.find()` calls by the same key should use a Map.

**Incorrect (O(n) per lookup):**

```typescript
return orders.map(order => ({
  ...order,
  user: users.find(u => u.id === order.userId)
}))
```

**Correct (O(1) per lookup):**

```typescript
const userById = new Map(users.map(u => [u.id, u]))

return orders.map(order => ({
  ...order,
  user: userById.get(order.userId)
}))
```

### 7.3 Cache Property Access in Loops

**Impact: LOW-MEDIUM**

Cache object property lookups in hot paths.

### 7.4 Cache Repeated Function Calls

**Impact: MEDIUM**

Use a module-level Map to cache function results.

### 7.5 Cache Storage API Calls

**Impact: LOW-MEDIUM**

`localStorage`, `sessionStorage`, and `document.cookie` are synchronous and expensive. Cache reads in memory.

### 7.6 Combine Multiple Array Iterations

**Impact: LOW-MEDIUM**

Multiple `.filter()` or `.map()` calls iterate the array multiple times. Combine into one loop.

### 7.7 Early Length Check for Array Comparisons

**Impact: MEDIUM-HIGH**

When comparing arrays with expensive operations, check lengths first.

### 7.8 Early Return from Functions

**Impact: LOW-MEDIUM**

Return early when result is determined.

### 7.9 Hoist RegExp Creation

**Impact: LOW-MEDIUM**

Don't create RegExp inside render. Hoist to module scope or memoize.

### 7.10 Use Loop for Min/Max Instead of Sort

**Impact: LOW (O(n) instead of O(n log n))**

Finding the smallest or largest element only requires a single pass.

### 7.11 Use Set/Map for O(1) Lookups

**Impact: LOW-MEDIUM**

Convert arrays to Set/Map for repeated membership checks.

**Incorrect (O(n) per check):**

```typescript
items.filter(item => allowedIds.includes(item.id))
```

**Correct (O(1) per check):**

```typescript
const allowedIds = new Set(['a', 'b', 'c'])
items.filter(item => allowedIds.has(item.id))
```

### 7.12 Use toSorted() Instead of sort() for Immutability

**Impact: MEDIUM-HIGH (prevents mutation bugs)**

`.sort()` mutates the array in place. Use `.toSorted()` to create a new sorted array.

**Incorrect: mutates original array**

```typescript
const sorted = users.sort((a, b) => a.name.localeCompare(b.name))
```

**Correct: creates new array**

```typescript
const sorted = users.toSorted((a, b) => a.name.localeCompare(b.name))
```

---

## 8. Advanced Patterns

**Impact: LOW**

Advanced patterns for specific cases that require careful implementation.

### 8.1 Initialize App Once, Not Per Mount

**Impact: LOW-MEDIUM**

Do not put app-wide initialization inside `useEffect([])`. Use a module-level guard.

**Incorrect: runs twice in dev, re-runs on remount**

```tsx
function Comp() {
  useEffect(() => {
    loadFromStorage()
    checkAuthToken()
  }, [])
}
```

**Correct: once per app load**

```tsx
let didInit = false

function Comp() {
  useEffect(() => {
    if (didInit) return
    didInit = true
    loadFromStorage()
    checkAuthToken()
  }, [])
}
```

Reference: [https://react.dev/learn/you-might-not-need-an-effect#initializing-the-application](https://react.dev/learn/you-might-not-need-an-effect#initializing-the-application)

### 8.2 Store Event Handlers in Refs

**Impact: LOW (stable subscriptions)**

Store callbacks in refs when used in effects that shouldn't re-subscribe on callback changes.

### 8.3 useEffectEvent for Stable Callback Refs

**Impact: LOW (prevents effect re-runs)**

Access latest values in callbacks without adding them to dependency arrays.

```tsx
import { useEffectEvent } from 'react';

function SearchInput({ onSearch }: { onSearch: (q: string) => void }) {
  const [query, setQuery] = useState('')
  const onSearchEvent = useEffectEvent(onSearch)

  useEffect(() => {
    const timeout = setTimeout(() => onSearchEvent(query), 300)
    return () => clearTimeout(timeout)
  }, [query])
}
```

---

## References

1. [https://react.dev](https://react.dev)
2. [https://nextjs.org](https://nextjs.org)
3. [https://swr.vercel.app](https://swr.vercel.app)
4. [https://github.com/shuding/better-all](https://github.com/shuding/better-all)
5. [https://github.com/isaacs/node-lru-cache](https://github.com/isaacs/node-lru-cache)
6. [https://vercel.com/blog/how-we-optimized-package-imports-in-next-js](https://vercel.com/blog/how-we-optimized-package-imports-in-next-js)
7. [https://vercel.com/blog/how-we-made-the-vercel-dashboard-twice-as-fast](https://vercel.com/blog/how-we-made-the-vercel-dashboard-twice-as-fast)
