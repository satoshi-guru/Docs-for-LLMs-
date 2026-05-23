# LOOKUP.md — API grep table across all indexed libs
# Format: library | signature | note
# Usage: grep "supabase-js" docs/LOOKUP.md

expo-notifications | Notifications.setNotificationHandler({ handleNotification }) | must be called at app root before navigation
expo-notifications | Notifications.getPermissionsAsync() | returns { status: 'granted'|'denied'|'undetermined' }
expo-notifications | Notifications.requestPermissionsAsync() | request push permission from user
expo-notifications | Notifications.getExpoPushTokenAsync({ projectId }) | projectId is REQUIRED — throws without it
expo-notifications | Constants?.expoConfig?.extra?.eas?.projectId ?? Constants?.easConfig?.projectId | correct projectId source
expo-notifications | Notifications.setNotificationChannelAsync(id, options) | Android: call BEFORE getExpoPushTokenAsync
expo-notifications | Notifications.AndroidImportance.MAX | highest importance for Android channels
expo-notifications | Notifications.addNotificationReceivedListener(cb) | foreground notification listener — call .remove() on cleanup
expo-notifications | Notifications.addNotificationResponseReceivedListener(cb) | user tapped notification — call .remove() on cleanup
expo-notifications | Notifications.scheduleNotificationAsync({ content, trigger }) | schedule local notification
expo-notifications | Device.isDevice | false on simulator/emulator — guard push token registration with this

supabase-js | createClient(url, key, options?) | create single client instance — export from lib/supabase.ts
supabase-js | createClient(url, key, { auth: { storage: AsyncStorage, persistSession: true } }) | React Native pattern
supabase-js | supabase.from('table').select(cols?) | SELECT query — cols default '*'
supabase-js | supabase.from('table').select('*, rel(col)') | join / nested select
supabase-js | supabase.from('table').select().eq(col, val).single() | fetch one row — throws if 0 rows
supabase-js | supabase.from('table').select().eq(col, val).maybeSingle() | fetch one row — null if not found
supabase-js | supabase.from('table').select().order(col, { ascending }).limit(n) | use instead of maybeSingle when multiple rows possible
supabase-js | supabase.from('table').insert({ col: val }).select() | INSERT — chain .select() to return row
supabase-js | supabase.from('table').update({ col: val }).eq(col, val).select() | UPDATE — always chain filter
supabase-js | supabase.from('table').upsert({ col: val }, { onConflict: col }) | UPSERT on conflict column
supabase-js | supabase.from('table').delete().eq(col, val) | DELETE — always chain filter
supabase-js | supabase.rpc('fn_name', { param: val }) | call Postgres function / RPC
supabase-js | supabase.functions.invoke('fn-name', { body: {} }) | invoke Edge Function
supabase-js | supabase.auth.signInWithPassword({ email, password }) | sign in
supabase-js | supabase.auth.signUp({ email, password }) | sign up
supabase-js | supabase.auth.signOut() | sign out
supabase-js | supabase.auth.getSession() | get session from cache
supabase-js | supabase.auth.getUser() | get user — server-verified, use for security flows
supabase-js | supabase.auth.onAuthStateChange((event, session) => {}) | auth state listener
supabase-js | supabase.channel('name').on('postgres_changes', { event, schema, table }, cb).subscribe() | realtime subscription
supabase-js | supabase.removeChannel(channel) | cleanup realtime subscription
supabase-js | supabase.storage.from('bucket').upload(path, file, { upsert: true }) | upload file
supabase-js | supabase.storage.from('bucket').getPublicUrl(path) | get public URL for uploaded file
supabase-js | .eq .neq .gt .gte .lt .lte .in .is .ilike .contains .order .limit .range | filter/modifier chain methods
