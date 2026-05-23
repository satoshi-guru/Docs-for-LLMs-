# supabase-js — Compact Reference
# Source: https://supabase.com/docs/reference/javascript/  |  Indexed: 2026-05-23  |  Raw pages: 192
# Use this instead of reading raw docs. Grep LOOKUP.md for quick API lookup.

## Core API

```ts
// Init — single client instance
import { createClient } from '@supabase/supabase-js'
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY)

// React Native with AsyncStorage (required for session persistence)
const supabase = createClient(url, key, {
  auth: { storage: AsyncStorage, autoRefreshToken: true, persistSession: true, detectSessionInUrl: false }
})

// SELECT
const { data, error } = await supabase.from('table').select('col1, col2')
const { data } = await supabase.from('table').select('*, relation(col1, col2)')  // join
const { data } = await supabase.from('table').select().eq('id', id).single()     // one row
const { data } = await supabase.from('table').select().order('created_at', { ascending: false }).limit(1)

// INSERT — chain .select() to get inserted row back
const { data, error } = await supabase.from('table').insert({ col: val }).select()

// UPDATE — always chain a filter
const { error } = await supabase.from('table').update({ col: val }).eq('id', id)
const { data, error } = await supabase.from('table').update({ col: val }).eq('id', id).select()

// UPSERT — primary key must be in values
const { data, error } = await supabase.from('table').upsert({ id, col: val }).select()
const { error } = await supabase.from('table').upsert({ col: val }, { onConflict: 'unique_col' })

// DELETE — always chain a filter
const { error } = await supabase.from('table').delete().eq('id', id)

// RPC (Postgres function)
const { data, error } = await supabase.rpc('function_name', { param1: val1 })

// Edge Function
const { data, error } = await supabase.functions.invoke('function-name', { body: { key: val } })

// Auth
const { data, error } = await supabase.auth.signInWithPassword({ email, password })
const { data, error } = await supabase.auth.signUp({ email, password })
await supabase.auth.signOut()
const { data: { session } } = await supabase.auth.getSession()
const { data: { user } } = await supabase.auth.getUser()  // preferred — verified server-side
supabase.auth.onAuthStateChange((event, session) => { ... })
```

## Key Patterns

### Filters
```ts
.eq('col', val)       // =
.neq('col', val)      // !=
.gt('col', val)       // >
.gte('col', val)      // >=
.lt('col', val)       // <
.lte('col', val)      // <=
.in('col', [v1, v2])  // IN
.is('col', null)      // IS NULL
.ilike('col', '%val%') // case-insensitive LIKE
.contains('col', ['a']) // array contains
.order('col', { ascending: false })
.limit(10)
.range(0, 9)          // pagination: rows 0–9
```

### Realtime subscription
```ts
const channel = supabase
  .channel('room-1')
  .on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'messages' }, payload => {
    console.log(payload.new)
  })
  .subscribe()

// Cleanup
supabase.removeChannel(channel)
```

### Storage upload
```ts
const { data, error } = await supabase.storage
  .from('bucket-name')
  .upload(`${folder}/${filename}`, file, { contentType: 'image/jpeg', upsert: true })
const { data: { publicUrl } } = supabase.storage.from('bucket').getPublicUrl(path)
```

## Breaking Changes / Gotchas

- ⚠ Default max 1000 rows per query — use `.range()` for pagination over large tables
- ⚠ `.single()` throws if 0 rows — use `.maybeSingle()` when row may not exist, or `.order().limit(1)` when multiple rows possible
- ⚠ `.update()` and `.delete()` without a filter update/delete ALL rows in the table
- ⚠ `getSession()` reads from local cache — use `getUser()` for server-verified identity in security-sensitive flows
- ⚠ Filters must come AFTER `.select()` / `.update()` / `.delete()`, not before

## What NOT to Do

- ✗ `.from('t').eq('col', val).select()` — filter before select is incorrect; use `.select().eq(...)`
- ✗ `.single()` when result may be 0 rows — it will throw; use `.maybeSingle()`
- ✗ `.maybeSingle()` when multiple rows may exist — use `.order(...).limit(1)` instead
- ✗ Multiple supabase client instances — create once and export from `lib/supabase.ts`
- ✗ `service_role` key in client-side code — use `anon` key only; service_role bypasses RLS
