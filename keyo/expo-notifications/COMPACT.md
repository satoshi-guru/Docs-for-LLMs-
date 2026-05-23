# expo-notifications — Compact Reference
# Source: https://docs.expo.dev/push-notifications/  |  Indexed: 2026-05-23  |  Raw pages: 198
# Use this instead of reading raw docs. Grep LOOKUP.md for quick API lookup.
# NOTE: This folder contains the full expo.dev docs (expo-notifications, expo-router, EAS, etc.)

## Core API

```ts
// REQUIRED: set at app root before any navigation
Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldPlaySound: true,
    shouldSetBadge: true,
    shouldShowBanner: true,
    shouldShowList: true,
  }),
});

// Permission check + request
const { status } = await Notifications.getPermissionsAsync();
const { status } = await Notifications.requestPermissionsAsync();

// Push token — projectId is REQUIRED, never optional
const projectId =
  Constants?.expoConfig?.extra?.eas?.projectId ?? Constants?.easConfig?.projectId;
if (!projectId) throw new Error('No projectId — run: eas init');
const token = (await Notifications.getExpoPushTokenAsync({ projectId })).data;

// Android: create channel BEFORE getExpoPushTokenAsync (Android 13+ requirement)
await Notifications.setNotificationChannelAsync('default', {
  name: 'default',
  importance: Notifications.AndroidImportance.MAX,
  vibrationPattern: [0, 250, 250, 250],
});

// Listeners
const sub = Notifications.addNotificationReceivedListener(n => { /* foreground */ });
const sub2 = Notifications.addNotificationResponseReceivedListener(r => { /* tap */ });
sub.remove(); // cleanup in useEffect return

// Schedule local notification
await Notifications.scheduleNotificationAsync({
  content: { title: 'Title', body: 'Body', data: {} },
  trigger: { seconds: 2 },
});
```

## Key Patterns

### Full registration function (correct pattern from docs)
```ts
async function registerForPushNotificationsAsync(): Promise<string | undefined> {
  if (!Device.isDevice) return; // simulator: skip

  if (Platform.OS === 'android') {
    await Notifications.setNotificationChannelAsync('default', {
      name: 'default',
      importance: Notifications.AndroidImportance.MAX,
    });
  }

  const { status: existing } = await Notifications.getPermissionsAsync();
  let finalStatus = existing;
  if (existing !== 'granted') {
    const { status } = await Notifications.requestPermissionsAsync();
    finalStatus = status;
  }
  if (finalStatus !== 'granted') return;

  const projectId =
    Constants?.expoConfig?.extra?.eas?.projectId ?? Constants?.easConfig?.projectId;
  if (!projectId) return; // EAS not configured yet

  return (await Notifications.getExpoPushTokenAsync({ projectId })).data;
}
```

### Send via Expo push API
```ts
await fetch('https://exp.host/--/api/v2/push/send', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ to: expoPushToken, sound: 'default', title: 'Hi', body: 'Hello' }),
});
```

### app.json projectId config
```json
{
  "expo": {
    "extra": { "eas": { "projectId": "<uuid-from-eas-init>" } }
  }
}
```

## Breaking Changes / Gotchas

- ⚠ `getExpoPushTokenAsync()` with no args THROWS — projectId is required since ~0.29
- ⚠ Android 13+: `setNotificationChannelAsync` must be called BEFORE `getExpoPushTokenAsync`
- ⚠ Push tokens only work on physical devices (`Device.isDevice === true`)
- ⚠ EAS project required — run `eas init` to get projectId, then `eas build` for dev client
- ⚠ Expo Go on physical device still needs projectId — it no longer infers it from the shell

## What NOT to Do

- ✗ `getExpoPushTokenAsync()` — no args will throw ERR_NOTIFICATIONS_NO_EXPERIENCE_ID
- ✗ Test on simulator — push tokens don't work; always use physical device
- ✗ Skip channel creation on Android — notifications won't appear on Android 13+
- ✗ Hardcode projectId string — read it from Constants to stay portable
