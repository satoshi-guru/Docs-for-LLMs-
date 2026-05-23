---
title: "ExpoCalendar"
url: https://docs.expo.dev/versions/latest/sdk/calendar
---

# ExpoCalendar

# Expo Calendar

A library that provides an API for interacting with the device's system calendars, events, reminders, and associated records.

Android (device only)

iOS (device only)

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-calendar/src)[npm](https://www.npmjs.com/package/expo-calendar)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-calendar/src/CHANGELOG.md)

Bundled version:

~56.0.8

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

> To provide quicker updates, `expo-calendar` is currently unsupported in Expo Go and Snack. To use it, create a [development build](/develop/development-builds/create-a-build).

`expo-calendar` provides an API for interacting with the device's system calendars, events, reminders, and associated records.

Additionally, it provides methods to launch the system-provided calendar UI to allow the user to view or edit events. On iOS, they present either [`EKEventViewController`](https://developer.apple.com/documentation/eventkitui/ekeventviewcontroller) or [`EKEventEditViewController`](https://developer.apple.com/documentation/eventkitui/ekeventeditviewcontroller) as a modal.

## Installation

Terminal

Copy

`- ``npx expo install expo-calendar`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-calendar` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-calendar",
            {
              "calendarPermission": "The app needs to access your calendar."
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`calendarPermission`| `"Allow $(PRODUCT_NAME) to access your calendar"`| Only for: iOS
A string to set the `NSCalendarsUsageDescription` permission message.
`remindersPermission`| `"Allow $(PRODUCT_NAME) to access your reminders"`| Only for: iOS
A string to set the `NSRemindersUsageDescription` permission message.
`writeOnlyCalendarPermission`| `"Allow $(PRODUCT_NAME) to add events to your calendars"`| Only for: iOS
A string to set the `NSCalendarsWriteOnlyAccessUsageDescription` permission message, shown when requesting write-only calendar access (iOS 17+). Only used when `writeOnlyAccess` is `true`.
`writeOnlyAccess`| `false`| Only for: iOS
When `true`, requests write-only calendar access (iOS 17+). Sets `NSCalendarsWriteOnlyAccessUsageDescription` and omits `NSCalendarsFullAccessUsageDescription`.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) (you're using native ios project manually), then you need to configure following permissions in your native project:

  * For iOS, add `NSCalendarsUsageDescription`, `NSCalendarsFullAccessUsageDescription`, and `NSRemindersUsageDescription` to your project's ios/[app]/Info.plist:

        <key>NSCalendarsUsageDescription</key>
        <string>Allow $(PRODUCT_NAME) to access your calendar</string>
        <key>NSCalendarsFullAccessUsageDescription</key>
        <string>Allow $(PRODUCT_NAME) to access your calendar</string>
        <key>NSRemindersUsageDescription</key>
        <string>Allow $(PRODUCT_NAME) to access your reminders</string>


When requesting write-only calendar access on iOS 17+, add `NSCalendarsWriteOnlyAccessUsageDescription` instead of `NSCalendarsFullAccessUsageDescription`:

        <key>NSCalendarsWriteOnlyAccessUsageDescription</key>
        <string>Allow $(PRODUCT_NAME) to add events to your calendars</string>



## Usage


    import * as Calendar from 'expo-calendar';
    import { useEffect } from 'react';
    import { StyleSheet, View, Text, Button } from 'react-native';

    const BasicUsage = () => {
      useEffect(() => {
        (async () => {
          const { status } = await Calendar.requestCalendarPermissions();
          if (status === 'granted') {
            const calendars = Calendar.getCalendars(Calendar.EntityTypes.EVENT);
            console.log('Here are all your calendars:');
            console.log(JSON.stringify(calendars));
          }
        })();
      }, []);

      return (
        <View style={styles.container}>
          <Text>Calendar Module Example</Text>
          <Button title="Create a new calendar" onPress={createCalendar} />
        </View>
      );
    };

    async function createCalendar() {
      const newCalendar = await Calendar.createCalendar({
        title: 'Expo Calendar',
        color: 'blue',
        entityType: Calendar.EntityTypes.EVENT,
      });
      console.log(`Your new calendar: ${JSON.stringify(newCalendar)}`);
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'space-around',
      },
    });


## API


    import * as Calendar from 'expo-calendar';


Unless specified otherwise, all dates are returned in the ISO 8601 format.

## Hooks

### `useCalendarPermissions(options)`

Android

iOS

Parameter| Type
---|---
options(optional)| `PermissionHookOptions<{  writeOnly: boolean }>`




Check or request permissions to access the user's calendars. This uses both `getCalendarPermissions` and `requestCalendarPermissions` to interact with the permissions. On iOS, `writeOnly` requests permission to create calendar events without reading existing calendars or events. It does not grant permission to create, update, or delete calendars.

Returns:

`[PermissionResponse | null, RequestPermissionMethod<PermissionResponse>, GetPermissionMethod<PermissionResponse>]`

Example


    const [status, requestPermission] = Calendar.useCalendarPermissions();


### `useRemindersPermissions(options)`

Android

iOS

Parameter| Type
---|---
options(optional)| `PermissionHookOptions<object>`




Check or request permissions to access the user's reminders. This uses both `getRemindersPermissions` and `requestRemindersPermissions` to interact with the permissions.

Returns:

`[PermissionResponse | null, RequestPermissionMethod<PermissionResponse>, GetPermissionMethod<PermissionResponse>]`

Example


    const [status, requestPermission] = Calendar.useRemindersPermissions();


## Classes

### `ExpoCalendar`

Android

iOS

Type: Class extends `ExpoCalendar`

Represents a calendar object that can be accessed and modified using the Expo Calendar Next API.

This class provides properties and methods for interacting with a specific calendar on the device, such as retrieving its events, updating its details, and accessing its metadata.

ExpoCalendar Properties

### `accessLevel`

Android

Optional • Type: `CalendarAccessLevel`

Level of access that the user has for the calendar.

### `allowedAttendeeTypes`

Android

Optional • Type: `AttendeeType[]`

Attendee types that this calendar supports.

### `allowedAvailabilities`

Android

iOS

Type: `Availability[]`

Availability types that this calendar supports.

### `allowedReminders`

Android

Optional • Type: `AlarmMethod[]`

Alarm methods that this calendar supports.

### `allowsModifications`

Android

iOS

Type: `boolean`

Boolean value that determines whether this calendar can be modified.

### `color`

Android

iOS

Optional • Type: `string`

Color used to display this calendar's events.

### `entityType`

iOS

Optional • Type: `EntityTypes`

Whether the calendar is used in the Calendar or Reminders OS app.

### `id`

Android

iOS

Type: `string`

Internal ID that represents this calendar on the device.

### `isPrimary`

Android

Optional • Type: `boolean`

Boolean value indicating whether this is the device's primary calendar.

### `isSynced`

Android

Optional • Type: `boolean`

Indicates whether this calendar is synced and its events stored on the device. Unexpected behavior may occur if this is not set to `true`.

### `isVisible`

Android

Optional • Type: `boolean`

Indicates whether the OS displays events on this calendar.

### `name`

Android

Optional • Literal type: `union`

Internal system name of the calendar.

Acceptable values are: `string` | `null`

### `ownerAccount`

Android

Optional • Type: `string`

Name for the account that owns this calendar.

### `source`

Android

iOS

Type: `Source`

Object representing the source to be used for the calendar.

### `sourceId`

iOS

Optional • Type: `string`

ID of the source to be used for the calendar. Likely the same as the source for any other locally stored calendars.

### `timeZone`

Android

Optional • Type: `string`

Time zone for the calendar.

### `title`

Android

iOS

Type: `string`

Visible name of the calendar.

### `type`

iOS

Optional • Type: `CalendarType`

Type of calendar this object represents.

ExpoCalendar Methods

### `addEventWithForm(options)`

Android

iOS

Parameter| Type
---|---
options(optional)| `AddEventWithFormOptions`




Presents the system-provided dialog to create a new event in this calendar, pre-filled with the provided data. Requires at minimum write-only calendar permission.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<DialogEventResult>`

### `createEvent(details)`

Android

iOS

Parameter| Type
---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<Event, 'creationDate' | 'lastModifiedDate' | 'originalStartDate' | 'isDetached' | 'status' | 'organizer'>>`




Creates a new event in the calendar.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarEvent>`

An instance of the created event.

### `createReminder(details)`

Android

iOS

Parameter| Type
---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Reminder>`




Creates a new reminder in the calendar.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarReminder>`

An instance of the created reminder.

### `delete()`

Android

iOS

Deletes the calendar.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `get(calendarId)`

Android

iOS

Parameter| Type| Description
---|---|---
calendarId| `string`| The ID of the calendar to get.




Gets a calendar by its ID. Throws an error if the calendar with the given ID does not exist.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendar>`

An `ExpoCalendar` object representing the calendar.

### `listEvents(startDate, endDate)`

Android

iOS

Parameter| Type
---|---
startDate| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`
endDate| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`




Returns a calendar event list for the given date range.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarEvent[]>`

### `listReminders(startDate, endDate, status)`

Android

iOS

Parameter| Type| Description
---|---|---
startDate(optional)| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) | null`| Beginning of time period to search for reminders in, or `null` for all completed reminders before `endDate`.Default:`null`
endDate(optional)| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) | null`| End of time period to search for reminders in, or `null` for all completed reminders after `startDate`.Default:`null`
status(optional)| `ReminderStatus | null`| One of `Calendar.ReminderStatus.COMPLETED` or `Calendar.ReminderStatus.INCOMPLETE`. If not defined, both completed and incomplete reminders will be returned.Default:`null`




Returns a list of reminders matching the provided criteria. If `startDate` and `endDate` are defined, returns all reminders that overlap at all with the `[startDate, endDate]` interval, that is, all reminders that end after the `startDate` or begin before the `endDate`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarReminder[]>`

An array of `ExpoCalendarReminder` objects matching the search criteria.

### `update(details)`

Android

iOS

Parameter| Type| Description
---|---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<ModifiableCalendarProperties>`| A map of properties to be updated.




Updates the provided details of an existing calendar stored on the device. To remove a property, explicitly set it to `null` in `details`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `ExpoCalendarAttendee`

Android

iOS

Type: Class extends `ExpoCalendarAttendee`

Represents a calendar attendee object.

ExpoCalendarAttendee Properties

### `email`

Android

Type: `string`

Email of the attendee.

### `id`

Android

Optional • Type: `string`

Internal ID that represents this attendee on the device.

### `isCurrentUser`

iOS

Optional • Type: `boolean`

Indicates whether or not this attendee is the current OS user.

### `name`

Android

iOS

Type: `string`

Displayed name of the attendee.

### `role`

Android

iOS

Type: `AttendeeRole`

Role of the attendee at the event.

### `status`

Android

iOS

Type: `AttendeeStatus`

Status of the attendee in relation to the event.

### `type`

Android

iOS

Type: `AttendeeType`

Type of the attendee.

### `url`

iOS

Optional • Type: `string`

URL for the attendee.

ExpoCalendarAttendee Methods

### `delete()`

Android

Deletes the attendee.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `update(details)`

Android

Parameter| Type
---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<ModifiableAttendeeProperties>`




Updates the attendee.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `ExpoCalendarEvent`

Android

iOS

Type: Class extends `ExpoCalendarEvent`

Represents a calendar event object that can be accessed and modified using the Expo Calendar Next API.

ExpoCalendarEvent Properties

### `accessLevel`

Android

Optional • Type: `EventAccessLevel`

User's access level for the event.

### `alarms`

Android

iOS

Type: `Alarm[]`

Array of Alarm objects which control automated reminders to the user.

### `allDay`

Android

iOS

Type: `boolean`

Whether the event is displayed as an all-day event on the calendar

### `availability`

Android

iOS

Type: `Availability`

The availability setting for the event.

### `calendarId`

Android

iOS

Type: `string`

ID of the calendar that contains this event.

### `creationDate`

iOS

Optional • Literal type: `union`

Date when the event record was created.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `endDate`

Android

iOS

Literal type: `union`

Date object or string representing the time when the event ends.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `endTimeZone`

Android

Optional • Type: `string`

Time zone for the end date of the event.

### `guestsCanInviteOthers`

Android

Optional • Type: `boolean`

Whether invited guests can invite other guests.

### `guestsCanModify`

Android

Optional • Type: `boolean`

Whether invited guests can modify the details of the event.

### `guestsCanSeeGuests`

Android

Optional • Type: `boolean`

Whether invited guests can see other guests.

### `id`

Android

iOS

Type: `string`

Internal ID that represents this event on the device.

### `instanceId`

Android

Optional • Type: `string`

For instances of recurring events, volatile ID representing this instance. Not guaranteed to always refer to the same instance.

### `isDetached`

iOS

Optional • Type: `boolean`

Boolean value indicating whether or not the event is a detached (modified) instance of a recurring event.

### `lastModifiedDate`

iOS

Optional • Literal type: `union`

Date when the event record was last modified.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `location`

Android

iOS

Literal type: `union`

Location field of the event.

Acceptable values are: `string` | `null`

### `notes`

Android

iOS

Type: `string`

Description or notes saved with the event.

### `organizer`

iOS

Optional • Type: `Organizer`

Organizer of the event. This property is only available on events associated with calendars that are managed by a service such as Google Calendar or iCloud. The organizer is read-only and cannot be set.

### `organizerEmail`

Android

Optional • Type: `string`

Email address of the organizer of the event.

### `originalId`

Android

Optional • Type: `string`

For detached (modified) instances of recurring events, the ID of the original recurring event.

### `originalStartDate`

iOS

Optional • Literal type: `union`

For recurring events, the start date for the first (original) instance of the event.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `recurrenceRule`

Android

iOS

Literal type: `union`

Object representing rules for recurring or repeating events. Set to `null` for one-time events. It is either `endDate` or `occurrence` based.

Acceptable values are: `RecurrenceRule` | `null`

### `startDate`

Android

iOS

Literal type: `union`

Date object or string representing the time when the event starts.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `status`

Android

iOS

Type: `EventStatus`

Status of the event.

### `timeZone`

Android

iOS

Type: `string`

Time zone the event is scheduled in. When set to `null`, the event is scheduled to the device's time zone.

### `title`

Android

iOS

Type: `string`

Visible name of the event.

### `url`

iOS

Optional • Type: `string`

URL for the event.

ExpoCalendarEvent Methods

### `createAttendee(attendee)`

Android

iOS

Parameter| Type
---|---
attendee| `Attendee`




Creates a new attendee and adds it to this event.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarAttendee>`

### `delete()`

Android

iOS

Deletes the event.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `editInCalendar(params)`

Android

iOS

Parameter| Type
---|---
params(optional)| `CalendarDialogParamsNext | null`




Launches the calendar UI provided by the OS to edit or delete an event.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<DialogEventResult>`

A promise which resolves with information about the dialog result.

### `get(eventId)`

Android

iOS

Parameter| Type| Description
---|---|---
eventId| `string`| The ID of the event to get.




Gets an event by its ID. Throws an error if the event with the given ID does not exist.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarEvent>`

An `ExpoCalendarEvent` object representing the event.

### `getAttendees()`

Android

iOS

Gets all attendees for a given event (or instance of a recurring event).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarAttendee[]>`

An array of `Attendee` associated with the specified event.

### `getOccurrenceSync(recurringEventOptions)`

iOS

Parameter| Type| Description
---|---|---
recurringEventOptions(optional)| `RecurringEventOptions`| A map of options for recurring events.Default:`{}`




Returns an event instance for a given event (or instance of a recurring event).

Returns:

`ExpoCalendarEvent`

An event instance.

### `openInCalendar(params)`

Android

iOS

Parameter| Type
---|---
params(optional)| `CalendarDialogOpenParamsNext | null`




Launches the calendar UI provided by the OS to preview an event.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<OpenEventDialogResult>`

A promise which resolves with information about the dialog result.

### `update(details)`

Android

iOS

Parameter| Type| Description
---|---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<ModifiableEventProperties>`| A map of properties to be updated.




Updates the provided details of an existing calendar stored on the device. To remove a property, explicitly set it to `null` in `details`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `ExpoCalendarReminder`

Android

iOS

Type: Class extends `ExpoCalendarReminder`

Represents a calendar reminder object that can be accessed and modified using the Expo Calendar Next API.

ExpoCalendarReminder Properties

### `alarms`

Android

iOS

Optional • Type: `Alarm[]`

Array of Alarm objects which control automated alarms to the user about the task.

### `allDay`

Android

iOS

Optional • Type: `boolean`

Whether the reminder is an all-day reminder.

### `calendarId`

Android

iOS

Optional • Type: `string`

ID of the calendar that contains this reminder.

### `completed`

Android

iOS

Optional • Type: `boolean`

Indicates whether or not the task has been completed.

### `completionDate`

Android

iOS

Optional • Literal type: `union`

Date object or string representing the date of completion, if `completed` is `true`. Setting this property of a nonnull `Date` will automatically set the reminder's `completed` value to `true`.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `creationDate`

Android

iOS

Optional • Literal type: `union`

Date when the reminder record was created.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `dueDate`

Android

iOS

Optional • Literal type: `union`

Date object or string representing the time when the reminder task is due.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `id`

Android

iOS

Optional • Type: `string`

Internal ID that represents this reminder on the device.

### `lastModifiedDate`

Android

iOS

Optional • Literal type: `union`

Date when the reminder record was last modified.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `location`

Android

iOS

Optional • Type: `string`

Location field of the reminder

### `notes`

Android

iOS

Optional • Type: `string`

Description or notes saved with the reminder.

### `recurrenceRule`

Android

iOS

Optional • Literal type: `union`

Object representing rules for recurring or repeated reminders. `null` for one-time tasks.

Acceptable values are: `RecurrenceRule` | `null`

### `startDate`

Android

iOS

Optional • Literal type: `union`

Date object or string representing the start date of the reminder task.

Acceptable values are: `string` | `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`

### `timeZone`

Android

iOS

Optional • Type: `string`

Time zone the reminder is scheduled in.

### `title`

Android

iOS

Optional • Type: `string`

Visible name of the reminder.

### `url`

Android

iOS

Optional • Type: `string`

URL for the reminder.

ExpoCalendarReminder Methods

### `delete()`

Android

iOS

Deletes the reminder.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `get(reminderId)`

Android

iOS

Parameter| Type| Description
---|---|---
reminderId| `string`| The ID of the reminder to get.




Gets a reminder by its ID. Throws an error if the reminder with the given ID does not exist.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarReminder>`

An `ExpoCalendarReminder` object representing the reminder.

### `update(details)`

Android

iOS

Parameter| Type
---|---
details| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<ModifiableReminderProperties>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

## Methods

> Deprecated: Use `event.createAttendee()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.createAttendeeAsync(eventId, details)`

Android

iOS

Parameter| Type
---|---
eventId| `string`
details| `Attendee`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

### `Calendar.createCalendar(details)`

Android

iOS

Parameter| Type| Description
---|---|---
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Calendar>`| A map of details for the calendar to be created.Default:`{}`




Creates a new calendar on the device, allowing events to be added later and displayed in the OS Calendar app.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendar>`

An `ExpoCalendar` object representing the newly created calendar.

> Deprecated: Use `createCalendar()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.createCalendarAsync(details)`

Android

iOS

Parameter| Type
---|---
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Calendar>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `calendar.createEvent()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.createEventAsync(calendarId, details)`

Android

iOS

Parameter| Type
---|---
calendarId| `string`
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Event>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `calendar.addEventWithForm()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.createEventInCalendarAsync(eventData, presentationOptions)`

Android

iOS

Parameter| Type
---|---
eventData(optional)| `[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Event>, 'id'>`
presentationOptions(optional)| `PresentationOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<DialogEventResult>`

> Deprecated: Use `calendar.createReminder()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.createReminderAsync(calendarId, details)`

Android

iOS

Parameter| Type
---|---
calendarId| `string`
details(optional)| `Reminder`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `attendee.delete()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.deleteAttendeeAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `calendar.delete()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.deleteCalendarAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `event.delete()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.deleteEventAsync(id, recurringEventOptions)`

Android

iOS

Parameter| Type
---|---
id| `string`
recurringEventOptions(optional)| `RecurringEventOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `reminder.delete()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.deleteReminderAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `event.editInCalendar()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.editEventInCalendarAsync(params, presentationOptions)`

Android

iOS

Parameter| Type
---|---
params| `CalendarDialogParams`
presentationOptions(optional)| `PresentationOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<DialogEventResult>`

> Deprecated: Use `event.getAttendees()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getAttendeesForEventAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Attendee[]>`

### `Calendar.getCalendarPermissions(writeOnly)`

Android

iOS

Parameter| Type| Description
---|---|---
writeOnly(optional)| `boolean`| On iOS, whether to check write-only access, which allows creating calendar events without reading existing calendars or events. This does not grant permission to create, update, or delete calendars.




Checks user's permissions for accessing user's calendars.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `getCalendarPermissions()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getCalendarPermissionsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

### `Calendar.getCalendars(entityType)`

Android

iOS

Parameter| Type| Description
---|---|---
entityType(optional)| `EntityTypes`| iOS Only. Not required, but if defined, filters the returned calendars to a specific entity type. Possible values are `Calendar.EntityTypes.EVENT` (for calendars shown in the Calendar app) and `Calendar.EntityTypes.REMINDER` (for the Reminders app).

> Note: If not defined, you will need both permissions: CALENDAR and REMINDERS.




Gets an array of `ExpoCalendar` shared objects with details about the different calendars stored on the device.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendar[]>`

An array of `ExpoCalendar` shared objects matching the provided entity type (if provided).

> Deprecated: Use `getCalendars()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getCalendarsAsync(entityType)`

Android

iOS

Parameter| Type
---|---
entityType(optional)| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Calendar[]>`

> Deprecated: Use `getDefaultCalendarSync()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getDefaultCalendarAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Calendar>`

### `Calendar.getDefaultCalendarSync()`

Android

iOS

Gets an instance of the default calendar object.

Returns:

`ExpoCalendar`

An `ExpoCalendar` object that is the user's default calendar.

> Deprecated: Use `ExpoCalendarEvent.get()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getEventAsync(id, recurringEventOptions)`

Android

iOS

Parameter| Type
---|---
id| `string`
recurringEventOptions(optional)| `RecurringEventOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Event>`

> Deprecated: Use `listEvents()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getEventsAsync(calendarIds, startDate, endDate)`

Android

iOS

Parameter| Type
---|---
calendarIds| `string[]`
startDate| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`
endDate| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Event[]>`

> Deprecated: Use `ExpoCalendarReminder.get()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getReminderAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Reminder>`

> Deprecated: Use `calendar.listReminders()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getRemindersAsync(calendarIds, status, startDate, endDate)`

Android

iOS

Parameter| Type
---|---
calendarIds| `string[]`
status| `ReminderStatus`
startDate(optional)| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`
endDate(optional)| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Reminder[]>`

### `Calendar.getRemindersPermissions()`

Android

iOS

Checks user's permissions for accessing user's reminders.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `getRemindersPermissions()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getRemindersPermissionsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getSourceAsync(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Source>`

> Deprecated: Use `getSourcesSync()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.getSourcesAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Source[]>`

### `Calendar.getSourcesSync()`

Android

iOS

Gets an array of Source objects with details about the different sources stored on the device.

Returns:

`Source[]`

> Deprecated: Import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.isAvailableAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

### `Calendar.listEvents(calendars, startDate, endDate)`

Android

iOS

Parameter| Type| Description
---|---|---
calendars| `(string | ExpoCalendar)[]`| An array of calendar IDs (`string[]`) or `ExpoCalendar` objects to search for events.
startDate| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`| The start date of the time range to search for events.
endDate| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`| The end date of the time range to search for events.




Lists events from the device's calendar. It can be used to search events in multiple calendars.

> Note: If you want to search events in a single calendar, you can use `ExpoCalendar.listEvents` instead.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendarEvent[]>`

An array of `ExpoCalendarEvent` objects representing the events found.

> Deprecated: Use `event.openInCalendar()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.openEventInCalendar(id)`

Android

iOS

Parameter| Type
---|---
id| `string`




Returns:

`void`

> Deprecated: Use `event.openInCalendar()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.openEventInCalendarAsync(params, presentationOptions)`

Android

iOS

Parameter| Type
---|---
params| `CalendarDialogParams`
presentationOptions(optional)| `OpenEventPresentationOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<OpenEventDialogResult>`

### `Calendar.presentPicker()`

iOS

Presents the OS calendar picker and returns the selected calendar.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExpoCalendar | null>`

An `ExpoCalendar` object or `null` when the picker is cancelled.

### `Calendar.requestCalendarPermissions(writeOnly)`

Android

iOS

Parameter| Type| Description
---|---|---
writeOnly(optional)| `boolean`| On iOS, whether to request write-only access, which allows creating calendar events without reading existing calendars or events. This does not grant permission to create, update, or delete calendars.




Asks the user to grant permissions for accessing user's calendars.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `requestCalendarPermissions()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.requestCalendarPermissionsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `requestCalendarPermissions()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.requestPermissionsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

### `Calendar.requestRemindersPermissions()`

Android

iOS

Asks the user to grant permissions for accessing user's reminders.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `requestRemindersPermissions()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.requestRemindersPermissionsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

> Deprecated: Use `attendee.update()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.updateAttendeeAsync(id, details)`

Android

iOS

Parameter| Type
---|---
id| `string`
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Attendee>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `calendar.update()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.updateCalendarAsync(id, details)`

Android

iOS

Parameter| Type
---|---
id| `string`
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Calendar>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `event.update()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.updateEventAsync(id, details, recurringEventOptions)`

Android

iOS

Parameter| Type
---|---
id| `string`
details(optional)| `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<Event>`
recurringEventOptions(optional)| `RecurringEventOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `reminder.update()` or import this method from `expo-calendar/legacy`. This method will throw in runtime.

### `Calendar.updateReminderAsync(id, details)`

Android

iOS

Parameter| Type
---|---
id| `string`
details(optional)| `Reminder`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

## Types

### `AddEventWithFormOptions`

Android

iOS

Type: `PresentationOptions` extended by:

Property| Type| Description
---|---|---
alarms(optional)| `Alarm[]`| -
allDay(optional)| `boolean`| -
endDate(optional)| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) | string`| -
location(optional)| `string`| -
notes(optional)| `string`| -
recurrenceRule(optional)| `RecurrenceRule`| -
startDate(optional)| `[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) | string`| -
title(optional)| `string`| -
url(optional)| `string`| -

### `Alarm`

Android

iOS

A method for having the OS automatically remind the user about a calendar item.

Property| Type| Description
---|---|---
absoluteDate(optional)| `string`| Only for: iOS
Date object or string representing an absolute time the alarm should occur. Overrides `relativeOffset` and `structuredLocation` if specified alongside either.
method(optional)| `AlarmMethod`| Only for: Android
Method of alerting the user that this alarm should use. On iOS this is always a notification.
relativeOffset(optional)| `number`| Number of minutes from the `startDate` of the calendar item that the alarm should occur. Use negative values to have the alarm occur before the `startDate`.
structuredLocation(optional)| `AlarmLocation`| -

### `AlarmLocation`

Android

iOS

Property| Type| Description
---|---|---
coords(optional)| `{  latitude: number,  longitude: number }`| -
proximity(optional)| `string`| -
radius(optional)| `number`| -
title(optional)| `string`| Only for: iOS
-

### `CalendarDialogParams`

Android

iOS

Property| Type| Description
---|---|---
id| `string`| ID of the event to be presented in the calendar UI.
instanceStartDate(optional)| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`| Only for: iOS
Date object representing the start time of the desired instance, if looking for a single instance of a recurring event. If this is not provided and id represents a recurring event, the first instance of that event will be returned by default.

### `DaysOfTheWeek`

iOS

Property| Type| Description
---|---|---
dayOfTheWeek| `DayOfTheWeek`| Sunday to Saturday - `DayOfTheWeek` enum.
weekNumber(optional)| `number`| `-53` to `53` (`0` ignores this field, and a negative indicates a value from the end of the range).

### `DialogEventResult`

Android

iOS

The result of presenting a calendar dialog for creating or editing an event.

Property| Type| Description
---|---|---
action| `[Extract](https://www.typescriptlang.org/docs/handbook/utility-types.html#extracttype-union)<CalendarDialogResultActions, 'done' | 'saved' | 'canceled' | 'deleted'>`| How user responded to the dialog. On Android, this is always `done` (Android doesn't provide enough information to determine the user's action - the user may have canceled the dialog, saved or deleted the event). On iOS, it can be `saved`, `canceled` or `deleted`.
id| `string | null`| The ID of the event that was created or edited. On Android, this is always `null`. On iOS, this is a string when permissions are granted and user confirms the creation or editing of an event. Otherwise, it's `null`.

### `ModifiableCalendarProperties`

Android

iOS

Type: `[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<ExpoCalendar, 'color' | 'title'>`

### `ModifiableEventProperties`

Android

iOS

Type: `[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<ExpoCalendarEvent, 'title' | 'location' | 'timeZone' | 'url' | 'notes' | 'alarms' | 'recurrenceRule' | 'availability' | 'startDate' | 'endDate' | 'allDay'>`

### `ModifiableReminderProperties`

Android

iOS

Type: `[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<ExpoCalendarReminder, 'title' | 'location' | 'timeZone' | 'url' | 'notes' | 'alarms' | 'recurrenceRule' | 'startDate' | 'dueDate' | 'completed' | 'completionDate'>`

### `OpenEventDialogResult`

Android

iOS

The result of presenting the calendar dialog for opening (viewing) an event.

Property| Type| Description
---|---|---
action| `[Extract](https://www.typescriptlang.org/docs/handbook/utility-types.html#extracttype-union)<CalendarDialogResultActions, 'done' | 'canceled' | 'deleted' | 'responded'>`| Indicates how user responded to the dialog. On Android, the `action` is always `done`. On iOS, it can be `done`, `canceled`, `deleted` or `responded`.

### `OpenEventPresentationOptions`

Android

iOS

Type: `PresentationOptions` extended by:

Property| Type| Description
---|---|---
allowsCalendarPreview(optional)| `boolean`| Only for: iOS
Determines whether event can be shown in calendar day view preview. This property applies only to invitations.Default:`false`
allowsEditing(optional)| `boolean`| Only for: iOS
Whether to allow the user to edit the previewed event. This property applies only to events in calendars created by the user. Note that if the user edits the event, the returned action is the one that user performs last. For example, when user previews the event, confirms some edits and finally dismisses the dialog, the event is edited, but response is `canceled`.Default:`false`

### `PermissionExpiration`

Android

iOS

Literal Type: `union`

Permission expiration time. Currently, all permissions are granted permanently.

Acceptable values are: `'never'` | `number`

### `PermissionHookOptions`

Android

iOS

Literal Type: `union`

Acceptable values are: `PermissionHookBehavior` | `Options`

### `PermissionResponse`

Android

iOS

An object obtained by permissions get and request functions.

Property| Type| Description
---|---|---
canAskAgain| `boolean`| Indicates if user can be asked again for specific permission. If not, one should be directed to the Settings app in order to enable/disable the permission.
expires| `PermissionExpiration`| Determines time when the permission expires.
granted| `boolean`| A convenience boolean that indicates if the permission is granted.
status| `PermissionStatus`| Determines the status of the permission.

### `PresentationOptions`

Android

iOS

Property| Type| Description
---|---|---
startNewActivityTask(optional)| `boolean`| Only for: Android
Whether to launch the Activity as a new [task](https://developer.android.com/reference/android/content/Intent#FLAG_ACTIVITY_NEW_TASK). If `true`, the promise resolves with `'done'` action immediately after opening the calendar activity.Default:`true`

### `RecurrenceRule`

Android

iOS

A recurrence rule for events or reminders, allowing the same calendar item to recur multiple times. This type is based on [the iOS interface](https://developer.apple.com/documentation/eventkit/ekrecurrencerule/1507320-initrecurrencewithfrequency) which is in turn based on [the iCal RFC](https://tools.ietf.org/html/rfc5545#section-3.8.5.3) so you can refer to those to learn more about this potentially complex interface.

Not all the combinations make sense. For example, when frequency is `DAILY`, setting `daysOfTheMonth` makes no sense.

Property| Type| Description
---|---|---
daysOfTheMonth(optional)| `number[]`| Only for: iOS
The days of the month this event occurs on. `-31` to `31` (not including `0`). Negative indicates a value from the end of the range. This field is only valid for `Calendar.Frequency.Monthly`.
daysOfTheWeek(optional)| `DaysOfTheWeek[]`| Only for: iOS
The days of the week the event should recur on. An array of `DaysOfTheWeek` object.
daysOfTheYear(optional)| `number[]`| Only for: iOS
The days of the year this event occurs on. `-366` to `366` (not including `0`). Negative indicates a value from the end of the range. This field is only valid for `Calendar.Frequency.Yearly`.
endDate(optional)| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`| Date on which the calendar item should stop recurring; overrides `occurrence` if both are specified.
frequency| `Frequency`| How often the calendar item should recur.
interval(optional)| `number`| Interval at which the calendar item should recur. For example, an `interval: 2` with `frequency: DAILY` would yield an event that recurs every other day.Default:`1`
monthsOfTheYear(optional)| `MonthOfTheYear[]`| Only for: iOS
The months this event occurs on. This field is only valid for `Calendar.Frequency.Yearly`.
occurrence(optional)| `number`| Number of times the calendar item should recur before stopping.
setPositions(optional)| `number[]`| Only for: iOS
TAn array of numbers that filters which recurrences to include. For example, for an event that recurs every Monday, passing 2 here will make it recur every other Monday. `-366` to `366` (not including `0`). Negative indicates a value from the end of the range. This field is only valid for `Calendar.Frequency.Yearly`.
weeksOfTheYear(optional)| `number[]`| Only for: iOS
The weeks of the year this event occurs on. `-53` to `53` (not including `0`). Negative indicates a value from the end of the range. This field is only valid for `Calendar.Frequency.Yearly`.

### `RecurringEventOptions`

iOS

Options for specifying a particular instance of a recurring event. This type is used in various methods that operate on recurring events, such as updating or deleting a single occurrence or a set of future occurrences.

Property| Type| Description
---|---|---
futureEvents(optional)| `boolean`| Whether future events in the recurring series should also be updated. If `true`, will apply the given changes to the recurring instance specified by `instanceStartDate` and all future events in the series. If `false`, will only apply the given changes to the instance specified by `instanceStartDate`.
instanceStartDate(optional)| `string | [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`| Date object representing the start time of the desired instance, if looking for a single instance of a recurring event. If this is not provided and id represents a recurring event, the first instance of that event will be returned by default.

### `Source`

Android

iOS

A source account that owns a particular calendar. Expo apps will typically not need to interact with `Source` objects.

Property| Type| Description
---|---|---
id(optional)| `string`| Only for: iOS
Internal ID that represents this source on the device.
isLocalAccount(optional)| `boolean`| Only for: Android
Whether this source is the local phone account. Must be `true` if `type` is `undefined`.
name| `string`| Name for the account that owns this calendar and was used to sync the calendar to the device.
type| `string | SourceType`| Type of the account that owns this calendar and was used to sync it to the device. If `isLocalAccount` is falsy then this must be defined, and must match an account on the device along with `name`, or the OS will delete the calendar. On iOS, one of `SourceType`s.

## Enums

### `AlarmMethod`

Android

#### `ALARM`

`AlarmMethod.ALARM ＝ "alarm"`

#### `ALERT`

`AlarmMethod.ALERT ＝ "alert"`

#### `DEFAULT`

`AlarmMethod.DEFAULT ＝ "default"`

#### `EMAIL`

`AlarmMethod.EMAIL ＝ "email"`

#### `SMS`

`AlarmMethod.SMS ＝ "sms"`

### `AttendeeRole`

Android

iOS

#### `ATTENDEE`

Android

`AttendeeRole.ATTENDEE ＝ "attendee"`

#### `CHAIR`

iOS

`AttendeeRole.CHAIR ＝ "chair"`

#### `NONE`

Android

`AttendeeRole.NONE ＝ "none"`

#### `NON_PARTICIPANT`

iOS

`AttendeeRole.NON_PARTICIPANT ＝ "nonParticipant"`

#### `OPTIONAL`

iOS

`AttendeeRole.OPTIONAL ＝ "optional"`

#### `ORGANIZER`

Android

`AttendeeRole.ORGANIZER ＝ "organizer"`

#### `PERFORMER`

Android

`AttendeeRole.PERFORMER ＝ "performer"`

#### `REQUIRED`

iOS

`AttendeeRole.REQUIRED ＝ "required"`

#### `SPEAKER`

Android

`AttendeeRole.SPEAKER ＝ "speaker"`

#### `UNKNOWN`

iOS

`AttendeeRole.UNKNOWN ＝ "unknown"`

### `AttendeeStatus`

Android

iOS

#### `ACCEPTED`

`AttendeeStatus.ACCEPTED ＝ "accepted"`

#### `COMPLETED`

iOS

`AttendeeStatus.COMPLETED ＝ "completed"`

#### `DECLINED`

`AttendeeStatus.DECLINED ＝ "declined"`

#### `DELEGATED`

iOS

`AttendeeStatus.DELEGATED ＝ "delegated"`

#### `IN_PROCESS`

iOS

`AttendeeStatus.IN_PROCESS ＝ "inProcess"`

#### `INVITED`

Android

`AttendeeStatus.INVITED ＝ "invited"`

#### `NONE`

Android

`AttendeeStatus.NONE ＝ "none"`

#### `PENDING`

iOS

`AttendeeStatus.PENDING ＝ "pending"`

#### `TENTATIVE`

`AttendeeStatus.TENTATIVE ＝ "tentative"`

#### `UNKNOWN`

iOS

`AttendeeStatus.UNKNOWN ＝ "unknown"`

### `AttendeeType`

Android

iOS

#### `GROUP`

iOS

`AttendeeType.GROUP ＝ "group"`

#### `NONE`

Android

`AttendeeType.NONE ＝ "none"`

#### `OPTIONAL`

Android

`AttendeeType.OPTIONAL ＝ "optional"`

#### `PERSON`

iOS

`AttendeeType.PERSON ＝ "person"`

#### `REQUIRED`

Android

`AttendeeType.REQUIRED ＝ "required"`

#### `RESOURCE`

`AttendeeType.RESOURCE ＝ "resource"`

#### `ROOM`

iOS

`AttendeeType.ROOM ＝ "room"`

#### `UNKNOWN`

iOS

`AttendeeType.UNKNOWN ＝ "unknown"`

### `Availability`

Android

iOS

#### `BUSY`

`Availability.BUSY ＝ "busy"`

#### `FREE`

`Availability.FREE ＝ "free"`

#### `NOT_SUPPORTED`

iOS

`Availability.NOT_SUPPORTED ＝ "notSupported"`

#### `TENTATIVE`

`Availability.TENTATIVE ＝ "tentative"`

#### `UNAVAILABLE`

iOS

`Availability.UNAVAILABLE ＝ "unavailable"`

### `CalendarAccessLevel`

Android

#### `CONTRIBUTOR`

`CalendarAccessLevel.CONTRIBUTOR ＝ "contributor"`

#### `EDITOR`

`CalendarAccessLevel.EDITOR ＝ "editor"`

#### `FREEBUSY`

`CalendarAccessLevel.FREEBUSY ＝ "freebusy"`

#### `NONE`

`CalendarAccessLevel.NONE ＝ "none"`

#### `OVERRIDE`

`CalendarAccessLevel.OVERRIDE ＝ "override"`

#### `OWNER`

`CalendarAccessLevel.OWNER ＝ "owner"`

#### `READ`

`CalendarAccessLevel.READ ＝ "read"`

#### `RESPOND`

`CalendarAccessLevel.RESPOND ＝ "respond"`

#### `ROOT`

`CalendarAccessLevel.ROOT ＝ "root"`

### `CalendarDialogResultActions`

Android

iOS

Enum containing all possible user responses to the calendar UI dialogs. Depending on what dialog is presented, a subset of the values applies.

#### `canceled`

iOS

`CalendarDialogResultActions.canceled ＝ "canceled"`

The user canceled or dismissed the dialog.

#### `deleted`

iOS

`CalendarDialogResultActions.deleted ＝ "deleted"`

The user deleted the event.

#### `done`

`CalendarDialogResultActions.done ＝ "done"`

On Android, this is the only possible result because the OS doesn't provide enough information to determine the user's action - the user may have canceled the dialog, modified the event, or deleted it.

On iOS, this means the user simply closed the dialog.

#### `responded`

iOS

`CalendarDialogResultActions.responded ＝ "responded"`

The user responded to and saved a pending event invitation.

#### `saved`

iOS

`CalendarDialogResultActions.saved ＝ "saved"`

The user saved a new event or modified an existing one.

### `CalendarType`

iOS

#### `BIRTHDAYS`

`CalendarType.BIRTHDAYS ＝ "birthdays"`

#### `CALDAV`

`CalendarType.CALDAV ＝ "caldav"`

#### `EXCHANGE`

`CalendarType.EXCHANGE ＝ "exchange"`

#### `LOCAL`

`CalendarType.LOCAL ＝ "local"`

#### `SUBSCRIBED`

`CalendarType.SUBSCRIBED ＝ "subscribed"`

#### `UNKNOWN`

`CalendarType.UNKNOWN ＝ "unknown"`

### `DayOfTheWeek`

iOS

#### `Sunday`

`DayOfTheWeek.Sunday ＝ 1`

#### `Monday`

`DayOfTheWeek.Monday ＝ 2`

#### `Tuesday`

`DayOfTheWeek.Tuesday ＝ 3`

#### `Wednesday`

`DayOfTheWeek.Wednesday ＝ 4`

#### `Thursday`

`DayOfTheWeek.Thursday ＝ 5`

#### `Friday`

`DayOfTheWeek.Friday ＝ 6`

#### `Saturday`

`DayOfTheWeek.Saturday ＝ 7`

### `EntityTypes`

iOS

#### `EVENT`

`EntityTypes.EVENT ＝ "event"`

#### `REMINDER`

`EntityTypes.REMINDER ＝ "reminder"`

### `EventAccessLevel`

Android

#### `CONFIDENTIAL`

`EventAccessLevel.CONFIDENTIAL ＝ "confidential"`

#### `DEFAULT`

`EventAccessLevel.DEFAULT ＝ "default"`

#### `PRIVATE`

`EventAccessLevel.PRIVATE ＝ "private"`

#### `PUBLIC`

`EventAccessLevel.PUBLIC ＝ "public"`

### `EventStatus`

Android

iOS

#### `CANCELED`

`EventStatus.CANCELED ＝ "canceled"`

#### `CONFIRMED`

`EventStatus.CONFIRMED ＝ "confirmed"`

#### `NONE`

`EventStatus.NONE ＝ "none"`

#### `TENTATIVE`

`EventStatus.TENTATIVE ＝ "tentative"`

### `Frequency`

Android

iOS

#### `DAILY`

`Frequency.DAILY ＝ "daily"`

#### `MONTHLY`

`Frequency.MONTHLY ＝ "monthly"`

#### `WEEKLY`

`Frequency.WEEKLY ＝ "weekly"`

#### `YEARLY`

`Frequency.YEARLY ＝ "yearly"`

### `MonthOfTheYear`

iOS

#### `January`

`MonthOfTheYear.January ＝ 1`

#### `February`

`MonthOfTheYear.February ＝ 2`

#### `March`

`MonthOfTheYear.March ＝ 3`

#### `April`

`MonthOfTheYear.April ＝ 4`

#### `May`

`MonthOfTheYear.May ＝ 5`

#### `June`

`MonthOfTheYear.June ＝ 6`

#### `July`

`MonthOfTheYear.July ＝ 7`

#### `August`

`MonthOfTheYear.August ＝ 8`

#### `September`

`MonthOfTheYear.September ＝ 9`

#### `October`

`MonthOfTheYear.October ＝ 10`

#### `November`

`MonthOfTheYear.November ＝ 11`

#### `December`

`MonthOfTheYear.December ＝ 12`

### `ReminderStatus`

iOS

#### `COMPLETED`

`ReminderStatus.COMPLETED ＝ "completed"`

#### `INCOMPLETE`

`ReminderStatus.INCOMPLETE ＝ "incomplete"`

### `SourceType`

iOS

#### `BIRTHDAYS`

`SourceType.BIRTHDAYS ＝ "birthdays"`

#### `CALDAV`

`SourceType.CALDAV ＝ "caldav"`

#### `EXCHANGE`

`SourceType.EXCHANGE ＝ "exchange"`

#### `LOCAL`

`SourceType.LOCAL ＝ "local"`

#### `MOBILEME`

`SourceType.MOBILEME ＝ "mobileme"`

#### `SUBSCRIBED`

`SourceType.SUBSCRIBED ＝ "subscribed"`

## Permissions

### Android

If you only intend to use the [system-provided calendar UI](/versions/latest/sdk/calendar#launching-system-provided-calendar-dialogs), you don't need to request any permissions.

Otherwise, you must add the following permissions to your app.json inside the [`expo.android.permissions`](/versions/latest/config/app#permissions) array.

Android Permission| Description
---|---
`READ_CALENDAR`| Allows an application to read the user's calendar data.
`WRITE_CALENDAR`| Allows an application to write the user's calendar data.

### iOS

If you only intend to create events using system-provided calendar UI with [`createEventInCalendarAsync`](/versions/latest/sdk/calendar#createeventincalendarasynceventdata-presentationoptions), you don't need to request permissions.

The following usage description keys are used by this library:

Info.plist Key| Description
---|---
`NSCalendarsUsageDescription`| A message that tells the user why the app is requesting access to the user’s calendar data.
`NSCalendarsFullAccessUsageDescription`| A message that tells the user why the app is requesting full access to the user's calendar data.
`NSCalendarsWriteOnlyAccessUsageDescription`| A message that tells the user why the app is requesting write-only access to the user's calendar data.
`NSRemindersUsageDescription`| A message that tells the user why the app is requesting access to the user’s reminders.