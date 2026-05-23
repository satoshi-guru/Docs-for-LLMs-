---
title: "ExpoFileSystem"
url: https://docs.expo.dev/versions/latest/sdk/filesystem
---

# ExpoFileSystem

# Expo FileSystem

A library that provides access to the local file system on the device.

Android

iOS

tvOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-file-system)[npm](https://www.npmjs.com/package/expo-file-system)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-file-system/CHANGELOG.md)

Bundled version:

~56.0.7

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-file-system` provides access to files and directories stored on a device or bundled as assets into the native project. It also allows downloading files from the network.

## Installation

Terminal

Copy

`- ``npx expo install expo-file-system`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-file-system` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-file-system",
            {
              "supportsOpeningDocumentsInPlace": true,
              "enableFileSharing": true
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`supportsOpeningDocumentsInPlace`| `false`| Only for: iOS
A boolean to enable `LSSupportsOpeningDocumentsInPlace` in Info.plist. This allows the app to open documents in place.
`enableFileSharing`| `false`| Only for: iOS
A boolean to enable `UIFileSharingEnabled` in Info.plist. This enables file sharing in the iOS Files app, making the app's Documents directory accessible to users through the Files app, iTunes File Sharing, and other file management tools.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) or you're using native ios project manually, then you need to add the `LSSupportsOpeningDocumentsInPlace` and `UIFileSharingEnabled` keys to your project's ios/[app]/Info.plist:


    <key>LSSupportsOpeningDocumentsInPlace</key>
    <true/>
    <key>UIFileSharingEnabled</key>
    <true/>


## Usage


    import { File, Directory, Paths } from 'expo-file-system';


The `File` and `Directory` instances hold a reference to a file, content, or asset URI.

The file or directory does not need to exist — an error will be thrown from the constructor only if the wrong class is used to represent an existing path (so if you try to create a `File` instance passing a path to an already existing directory).

## Features

  * Both synchronous and asynchronous, read and write access to file contents
  * Creation, modification and deletion
  * Available properties, such as `type`, `size`, `creationDate`, and more
  * Ability to read and write files as streams or using the `FileHandle` class
  * Easy file download/upload using `downloadFileAsync` or `expo/fetch`


## Examples

Writing and reading text files

example.ts

Copy


    import { File, Paths } from 'expo-file-system';

    try {
      const file = new File(Paths.cache, 'example.txt');
      file.create(); // can throw an error if the file already exists or no permission to create it
      file.write('Hello, world!');
      console.log(file.textSync()); // Hello, world!
    } catch (error) {
      console.error(error);
    }


Picking files using system pickers

Usage with `expo-document-picker`:

example.ts

Copy


    import { File } from 'expo-file-system';
    import * as DocumentPicker from 'expo-document-picker';

    try {
      const result = await DocumentPicker.getDocumentAsync({ copyToCacheDirectory: true });
      if (!result.canceled) {
        const { uri } = result.assets[0];
        const file = new File(uri);
        console.log(file.textSync());
      }
    } catch (error) {
      console.error(error);
    }


Using the built-in `pickFileAsync` or `pickDirectoryAsync` method on Android:

example.ts

Copy


    import { File } from 'expo-file-system';

    try {
      const file = new File.pickFileAsync();
      console.log(file.textSync());
    } catch (error) {
      console.error(error);
    }


Downloading files

Using `downloadFileAsync`:

example.ts

Copy


    import { Directory, File, Paths } from 'expo-file-system';

    const url = 'https://pdfobject.com/pdf/sample.pdf';
    const destination = new Directory(Paths.cache, 'pdfs');
    try {
      destination.create();
      const output = await File.downloadFileAsync(url, destination);
      console.log(output.exists); // true
      console.log(output.uri); // path to the downloaded file, e.g., '${cacheDirectory}/pdfs/sample.pdf'
    } catch (error) {
      console.error(error);
    }


Or using `expo/fetch`:

example.ts

Copy


    import { fetch } from 'expo/fetch';
    import { File, Paths } from 'expo-file-system';

    const url = 'https://pdfobject.com/pdf/sample.pdf';
    const response = await fetch(url);
    const src = new File(Paths.cache, 'file.pdf');
    src.write(await response.bytes());


Uploading files using `expo/fetch`

You can upload files as blobs directly with `fetch` built into the Expo package:

example.ts

Copy


    import { fetch } from 'expo/fetch';
    import { File, Paths } from 'expo-file-system';

    const file = new File(Paths.cache, 'file.txt');
    file.write('Hello, world!');

    const response = await fetch('https://example.com', {
      method: 'POST',
      body: file,
    });


Or using the `FormData` constructor:

example.ts

Copy


    import { fetch } from 'expo/fetch';
    import { File, Paths } from 'expo-file-system';

    const file = new File(Paths.cache, 'file.txt');
    file.write('Hello, world!');
    const formData = new FormData();
    formData.append('data', file);
    const response = await fetch('https://example.com', {
      method: 'POST',
      body: formData,
    });


Moving and copying files

example.ts

Copy


    import { Directory, File, Paths } from 'expo-file-system';
    try {
      const file = new File(Paths.document, 'example.txt');
      file.create();
      console.log(file.uri); // '${documentDirectory}/example.txt'
      const copiedFile = new File(Paths.cache, 'example-copy.txt');
      file.copy(copiedFile);
      console.log(copiedFile.uri); // '${cacheDirectory}/example-copy.txt'
      file.move(Paths.cache);
      console.log(file.uri); // '${cacheDirectory}/example.txt'
      file.move(new Directory(Paths.cache, 'newFolder'));
      console.log(file.uri); // '${cacheDirectory}/newFolder/example.txt'
    } catch (error) {
      console.error(error);
    }


Using legacy FileSystem API

example.ts

Copy


    import * as FileSystem from 'expo-file-system/legacy';
    import { File, Paths } from 'expo-file-system';

    try {
      const file = new File(Paths.cache, 'example.txt');
      const content = await FileSystem.readAsStringAsync(file.uri);
      console.log(content);
    } catch (error) {
      console.error(error);
    }


Listing directory contents recursively

example.ts

Copy


    import { Directory, Paths } from 'expo-file-system';

    function printDirectory(directory: Directory, indent: number = 0) {
      console.log(`${' '.repeat(indent)} + ${directory.name}`);
      const contents = directory.list();
      for (const item of contents) {
        if (item instanceof Directory) {
          printDirectory(item, indent + 2);
        } else {
          console.log(`${' '.repeat(indent + 2)} - ${item.name} (${item.size} bytes)`);
        }
      }
    }

    try {
      printDirectory(new Directory(Paths.cache));
    } catch (error) {
      console.error(error);
    }


## API

## Constants

### `FileSystem.DEFAULT_DEBOUNCE_MS`

Android

iOS

tvOS

Type: `'100'`

The default debounce time for file system watcher events in milliseconds.

## Classes

### `Directory`

Android

iOS

tvOS

Type: Class extends `FileSystemDirectory`

Represents a directory on the filesystem.

A `Directory` instance can be created for any path, and does not need to exist on the filesystem during creation.

The constructor accepts an array of strings that are joined to create the directory URI. The first argument can also be a `Directory` instance (like `Paths.cache`).

Example


    const directory = new Directory(Paths.cache, "subdirName");


Directory Properties

### `exists`

Android

iOS

tvOS

Type: `boolean`

A boolean representing if a directory exists and can be accessed.

### `size`

Android

iOS

tvOS

Literal type: `union`

A size of the directory in bytes. Null if the directory does not exist, or it cannot be read.

Acceptable values are: `number` | `null`

### `uri`

Android

iOS

tvOS

Read Only • Type: `string`

Represents the directory URI. The field is read-only, but it may change as a result of calling some methods such as `move`.

### `name`

Android

iOS

tvOS

Type: `string`

Directory name.

### `parentDirectory`

Android

iOS

tvOS

Type: `Directory`

Directory containing the file.

Directory Methods

### `copy(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Copies a directory.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `copySync(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Copies a directory synchronously.

Returns:

`void`

### `create(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options(optional)| `DirectoryCreateOptions`




Creates a directory that the current uri points to.

Returns:

`void`

### `createDirectory(name)`

Android

iOS

tvOS

Parameter| Type
---|---
name| `string`




Returns:

`Directory`

### `createFile(name, mimeType)`

Android

iOS

tvOS

Parameter| Type
---|---
name| `string`
mimeType| `string | null`




Returns:

`File`

### `delete()`

Android

iOS

tvOS

Deletes a directory. Also deletes all files and directories inside the directory.

Returns:

`void`

### `info()`

Android

iOS

tvOS

Retrieves an object containing properties of a directory.

Returns:

`DirectoryInfo`

An object with directory metadata (for example, size, creation date, and so on).

### `list()`

Android

iOS

tvOS

Lists the contents of a directory. Calling this method if the parent directory does not exist will throw an error.

Returns:

`(File | Directory)[]`

An array of `Directory` and `File` instances.

### `move(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Moves a directory. Updates the `uri` property that now points to the new location.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `moveSync(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Moves a directory synchronously. Updates the `uri` property that now points to the new location.

Returns:

`void`

### `rename(newName)`

Android

iOS

tvOS

Parameter| Type
---|---
newName| `string`




Renames a directory.

Returns:

`void`

### `watch(callback, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
callback| `(event: WatchEvent<File | Directory>) => void`| Invoked when a change is detected. Receives a `WatchEvent` describing what changed.
options(optional)| `WatchOptions`| Configuration for debouncing and filtering events.




Watches this directory for changes to its contents or the directory itself.

Events are emitted when files or subdirectories are created, modified, deleted, or renamed within this directory. On iOS, child changes are surfaced as a coarse-grained `modified` event on the directory itself, so filtering for child-level `created`, `deleted`, or `renamed` events is not reliable. The watcher automatically stops when the directory is deleted or renamed. To stop watching manually, call `remove()` on the returned subscription.

Returns:

`WatchSubscription`

A subscription handle. Call `remove()` to stop watching.

Example


    const cacheDir = new Directory(Paths.cache);
    const subscription = cacheDir.watch((event) => {
      console.log(`${event.type}: ${event.target.uri}`);
    });

    // Later, stop watching:
    subscription.remove();


### `DownloadTask`

Android

iOS

tvOS

Type: Class extends `FileSystemDownloadTask`

Represents a download task with pause/resume support and progress tracking.

DownloadTask Properties

### `state`

Android

iOS

tvOS

Literal type: `string`

Acceptable values are: `'idle'` | `'active'` | `'paused'` | `'completed'` | `'cancelled'` | `'error'`

DownloadTask Methods

### `addListener(eventName, listener)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
listener| `DownloadTaskEvents[EventName]`




Adds a listener for the given event name.

Returns:

`EventSubscription`

### `cancel()`

Android

iOS

tvOS

Returns:

`void`

### `downloadAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<File | null>`

### `emit(eventName, ...args)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
...args| `Parameters<DownloadTaskEvents[EventName]>`




Synchronously calls all the listeners attached to that specific event. The event can include any number of arguments that will be passed to the listeners.

Returns:

`void`

### `fromSavable(state, options)`

Android

iOS

tvOS

Parameter| Type
---|---
state| `DownloadPauseState`
options(optional)| `DownloadTaskOptions`




Returns:

`DownloadTask`

### `listenerCount(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Returns a number of listeners added to the given event.

Returns:

`number`

### `pause()`

Android

iOS

tvOS

Returns:

`void`

### `pauseAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `release()`

Android

iOS

tvOS

A function that detaches the JS and native objects to let the native object deallocate before the JS object gets deallocated by the JS garbage collector. Any subsequent calls to native functions of the object will throw an error as it is no longer associated with its native counterpart.

In most cases, you should never need to use this function, except some specific performance-critical cases when manual memory management makes sense and the native object is known to exclusively retain some native memory (such as binary data or image bitmap). Before calling this function, you should ensure that nothing else will use this object later on. Shared objects created by React hooks are usually automatically released in the effect's cleanup phase, for example: `useVideoPlayer()` from `expo-video` and `useImage()` from `expo-image`.

Returns:

`void`

### `removeAllListeners(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `'progress'`




Removes all listeners for the given event name.

Returns:

`void`

### `removeListener(eventName, listener)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
listener| `DownloadTaskEvents[EventName]`




Removes a listener for the given event name.

Returns:

`void`

### `resume(url, to, resumeData, options)`

Android

iOS

tvOS

Parameter| Type
---|---
url| `string`
to| `File | Directory`
resumeData| `string`
options(optional)| `Record<string, any>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

### `resumeAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<File | null>`

### `savable()`

Android

iOS

tvOS

Returns:

`DownloadPauseState`

### `start(url, to, options)`

Android

iOS

tvOS

Parameter| Type
---|---
url| `string`
to| `File | Directory`
options(optional)| `Record<string, any>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

### `startObserving(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Function that is automatically invoked when the first listener for an event with the given name is added. Override it in a subclass to perform some additional setup once the event started being observed.

Returns:

`void`

### `stopObserving(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Function that is automatically invoked when the last listener for an event with the given name is removed. Override it in a subclass to perform some additional cleanup once the event is no longer observed.

Returns:

`void`

### `File`

Android

iOS

tvOS

Type: Class extends `FileSystemFile` implements `[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)`

Represents a file on the filesystem.

A `File` instance can be created for any path, and does not need to exist on the filesystem during creation.

The constructor accepts an array of strings that are joined to create the file URI. The first argument can also be a `Directory` instance (like `Paths.cache`) or a `File` instance (which creates a new reference to the same file).

Example


    const file = new File(Paths.cache, "subdirName", "file.txt");


File Properties

### `contentUri`

Android

Type: `string`

A content URI to the file that can be shared to external applications.

### `creationTime`

Android

iOS

tvOS

Literal type: `union`

A creation time of the file expressed in milliseconds since the epoch. Returns a `null` if the file does not exist, cannot be read or the Android version is earlier than API 26.

Acceptable values are: `number` | `null`

### `exists`

Android

iOS

tvOS

Type: `boolean`

A boolean representing if a file exists. `true` if the file exists, `false` otherwise. Also, `false` if the application does not have read access to the file.

### `lastModified`

Android

iOS

tvOS

Literal type: `union`

A last modification time of the file expressed in milliseconds since the epoch. Returns a `null` if the file does not exist, or if it cannot be read.

Acceptable values are: `number` | `null`

### `md5`

Android

iOS

tvOS

Literal type: `union`

A md5 hash of the file. Null if the file does not exist, or it cannot be read.

Acceptable values are: `string` | `null`

> Deprecated: In favor of `lastModified` to be more in line with web [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File)

### `modificationTime`

Android

iOS

tvOS

Literal type: `union`

A last modification time of the file expressed in milliseconds since the epoch. Returns a `null` if the file does not exist, or if it cannot be read.

Acceptable values are: `number` | `null`

### `size`

Android

iOS

tvOS

Type: `number`

A size of the file in bytes. 0 if the file does not exist, or it cannot be read.

### `type`

Android

iOS

tvOS

Type: `string`

A mime type of the file. An empty string if the file does not exist, or it cannot be read.

### `extension`

Android

iOS

tvOS

Type: `string`

File extension.

Example

`'.png'`

### `name`

Android

iOS

tvOS

Type: `string`

File name. Includes the extension.

### `parentDirectory`

Android

iOS

tvOS

Type: `Directory`

Directory containing the file.

### `uri`

Android

iOS

tvOS

Type: `string`

Represents the file URI. The field is read-only, but it may change as a result of calling some methods such as `move`.

File Methods

### `arrayBuffer()`

Android

iOS

tvOS

The `arrayBuffer()` method of the Blob interface returns a Promise that resolves with the contents of the blob as binary data contained in an ArrayBuffer.

[MDN Reference](https://developer.mozilla.org/docs/Web/API/Blob/arrayBuffer)

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)>`

### `base64()`

Android

iOS

tvOS

Retrieves content of the file as base64.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

A promise that resolves with the contents of the file as a base64 string.

### `base64Sync()`

Android

iOS

tvOS

Retrieves content of the file as base64.

Returns:

`string`

The contents of the file as a base64 string.

### `bytes()`

Android

iOS

tvOS

Retrieves byte content of the entire file.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)>>`

A promise that resolves with the contents of the file as a `Uint8Array`.

### `bytesSync()`

Android

iOS

tvOS

Retrieves byte content of the entire file.

Returns:

`[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)`

The contents of the file as a `Uint8Array`.

### `copy(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Copies a file.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `copySync(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Copies a file synchronously.

Returns:

`void`

### `create(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options(optional)| `FileCreateOptions`




Creates a file.

Returns:

`void`

### `createDownloadTask(url, destination, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
url| `string`| The URL of the file to download.
destination| `File | Directory`| The destination directory or file.
options(optional)| `DownloadTaskOptions`| Download options including headers, progress callback, and abort signal.




Creates a download task for downloading a file with pause/resume support.

Returns:

`DownloadTask`

A `DownloadTask` instance that can be used to control the download.

Example


    const dest = new File(Paths.document, 'video.mp4');
    const downloadTask = File.createDownloadTask(url, dest, {
      onProgress: ({ bytesWritten, totalBytes }) => {
        console.log(`Downloaded ${bytesWritten} of ${totalBytes} bytes`);
      }
    });
    const file = await downloadTask.downloadAsync();


### `createUploadTask(url, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
url| `string`| The URL to upload the file to.
options(optional)| `UploadOptions`| Upload options including upload type, headers, progress callback, and abort signal.




Creates an upload task for uploading this file with progress tracking.

Returns:

`UploadTask`

An `UploadTask` instance that can be used to control the upload.

Example


    const file = new File(Paths.document, 'photo.jpg');
    const uploadTask = file.createUploadTask(url, {
      uploadType: UploadType.MULTIPART,
      headers: { Authorization: 'Bearer token' },
      onProgress: ({ bytesSent, totalBytes }) => {
        console.log(`Uploaded ${bytesSent} of ${totalBytes} bytes`);
      }
    });
    const result = await uploadTask.uploadAsync();
    console.log('Upload status:', result.status);


### `delete()`

Android

iOS

tvOS

Deletes a file.

Returns:

`void`

### `info(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options(optional)| `InfoOptions`




Retrieves an object containing properties of a file

Returns:

`FileInfo`

An object with file metadata (for example, size, creation date, and so on).

### `move(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Moves a directory. Updates the `uri` property that now points to the new location.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `moveSync(destination, options)`

Android

iOS

tvOS

Parameter| Type
---|---
destination| `File | Directory`
options(optional)| `RelocationOptions`




Moves a file synchronously. Updates the `uri` property that now points to the new location.

Returns:

`void`

### `open(mode)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
mode(optional)| `FileMode`| The `FileMode` to use.

  * On Android, SAF `content://` URIs do not support `ReadWrite` mode.
  * Defaults:
    * For SAF `content://` URIs, the default is `FileMode.ReadOnly`.
    * For standard `file://` URIs, the default is `FileMode.ReadWrite`.






Returns A `FileHandle` object that can be used to read and write data to the file.

Returns:

`FileHandle`

### `pickFileAsync(options)`

Android

iOS

tvOS

Overload #1

Parameter| Type| Description
---|---|---
options(optional)| `PickSingleFileOptions`| options




An overload of the `pickFileAsync` method, which picks and returns a single `File`. This overload requires options to have `multipleFiles` flag be `undefined` or `false`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PickSingleFileResult>`

### `pickFileAsync(options)`

Android

iOS

tvOS

Overload #2

Parameter| Type| Description
---|---|---
options(optional)| `PickMultipleFilesOptions`| options




An overload of the `pickFileAsync` method, which picks and returns a list of `File`'s. This overload requires options to have `multipleFiles` flag be `true`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PickMultipleFilesResult>`

> Deprecated: Use `pickFileAsync({initialUri, mimeTypes: mimeType})` instead.

### `pickFileAsync(initialUri, mimeType)`

Android

iOS

tvOS

Overload #3

Parameter| Type| Description
---|---|---
initialUri(optional)| `string`| An optional URI pointing to an initial folder on which the file picker is opened.
mimeType(optional)| `string`| A mime type that is used to filter out files that can be picked out.




A static method that opens a file picker to select a single file of specified type. On iOS, it returns a temporary copy of the file leaving the original file untouched.

Selecting multiple files is not supported yet.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<File | File[]>`

A `File` instance or an array of `File` instances.

### `readableStream()`

Android

iOS

tvOS

Returns:

`[ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)<[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)>>`

### `rename(newName)`

Android

iOS

tvOS

Parameter| Type
---|---
newName| `string`




Renames a file.

Returns:

`void`

### `slice(start, end, contentType)`

Android

iOS

tvOS

Parameter| Type
---|---
start(optional)| `number`
end(optional)| `number`
contentType(optional)| `string`




The `slice()` method of the Blob interface creates and returns a new `Blob` object which contains data from a subset of the blob on which it's called.

[MDN Reference](https://developer.mozilla.org/docs/Web/API/Blob/slice)

Returns:

`[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)`

### `stream()`

Android

iOS

tvOS

The `stream()` method of the Blob interface returns a ReadableStream which upon reading returns the data contained within the `Blob`.

[MDN Reference](https://developer.mozilla.org/docs/Web/API/Blob/stream)

Returns:

`[ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)<[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)>>`

### `text()`

Android

iOS

tvOS

Retrieves text from the file.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

A promise that resolves with the contents of the file as string.

### `textSync()`

Android

iOS

tvOS

Retrieves text from the file.

Returns:

`string`

The contents of the file as string.

### `upload(url, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
url| `string`| The URL to upload the file to.
options(optional)| `UploadOptions`| Upload options.




Uploads this file to the network.

The promise resolves with the HTTP response metadata and body for any completed response, including non-2xx status codes. It rejects only for local file errors, transport failures, or cancellation.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<UploadResult>`

### `watch(callback, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
callback| `(event: WatchEvent<File>) => void`| Invoked when a change is detected. Receives a `WatchEvent` describing what changed.
options(optional)| `WatchOptions`| Configuration for debouncing and filtering events.




Watches this file for changes on the filesystem.

The watcher automatically stops when the file is deleted or renamed. To stop watching manually, call `remove()` on the returned subscription.

Returns:

`WatchSubscription`

A subscription handle. Call `remove()` to stop watching.

Example


    const file = new File(Paths.cache, 'data.json');
    const subscription = file.watch((event) => {
      console.log(`File ${event.type}`);
    });

    // Later, stop watching:
    subscription.remove();


### `writableStream()`

Android

iOS

tvOS

Returns:

`[WritableStream](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream)<[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<ArrayBufferLike>>`

### `write(content, options)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
content| `string | [Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<ArrayBufferLike>`| The content to write into the file.
options(optional)| `FileWriteOptions`| -




Writes content to the file.

Returns:

`void`

### `Paths`

Android

iOS

tvOS

Type: Class extends `PathUtilities`

Paths Properties

### `appleSharedContainers`

Android

iOS

tvOS

Type: `Record<string, Directory>`

### `availableDiskSpace`

Android

iOS

tvOS

Type: `number`

A property that represents the available space on device's internal storage, represented in bytes.

### `bundle`

Android

iOS

tvOS

Type: `Directory`

A property containing the bundle directory – the directory where assets bundled with the application are stored.

### `cache`

Android

iOS

tvOS

Type: `Directory`

A property containing the cache directory – a place to store files that can be deleted by the system when the device runs low on storage.

### `document`

Android

iOS

tvOS

Type: `Directory`

A property containing the document directory – a place to store files that are safe from being deleted by the system.

### `totalDiskSpace`

Android

iOS

tvOS

Type: `number`

A property that represents the total space on device's internal storage, represented in bytes.

Paths Methods

### `basename(path, ext)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to get the base name from.
ext(optional)| `string`| An optional file extension.




Returns the base name of a path.

Returns:

`string`

A string representing the base name.

### `dirname(path)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to get the directory name from.




Returns the directory name of a path.

Returns:

`string`

A string representing the directory name.

### `extname(path)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to get the extension from.




Returns the extension of a path.

Returns:

`string`

A string representing the extension.

### `info(...uris)`

Android

iOS

tvOS

Parameter| Type
---|---
...uris| `string[]`




Returns an object that indicates if the specified path represents a directory.

Returns:

`PathInfo`

### `isAbsolute(path)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to check.




Checks if a path is absolute.

Returns:

`boolean`

`true` if the path is absolute, `false` otherwise.

### `join(...paths)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
...paths| `(string | File | Directory)[]`| An array of path segments.




Joins path segments into a single path.

Returns:

`string`

A string representing the joined path.

### `normalize(path)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to normalize.




Normalizes a path.

Returns:

`string`

A string representing the normalized path.

### `parse(path)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
path| `string | File | Directory`| The path to parse.




Parses a path into its components.

Returns:

`{  base: string,  dir: string,  ext: string,  name: string,  root: string }`

An object containing the parsed path components.

### `relative(from, to)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
from| `string | File | Directory`| The base path.
to| `string | File | Directory`| The relative path.




Resolves a relative path to an absolute path.

Returns:

`string`

A string representing the resolved path.

### `UploadTask`

Android

iOS

tvOS

Type: Class extends `FileSystemUploadTask`

Represents an upload task with progress tracking and cancellation support.

UploadTask Properties

### `state`

Android

iOS

tvOS

Type: `UploadTaskState`

UploadTask Methods

### `addListener(eventName, listener)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
listener| `UploadTaskEvents[EventName]`




Adds a listener for the given event name.

Returns:

`EventSubscription`

### `cancel()`

Android

iOS

tvOS

Returns:

`void`

### `emit(eventName, ...args)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
...args| `Parameters<UploadTaskEvents[EventName]>`




Synchronously calls all the listeners attached to that specific event. The event can include any number of arguments that will be passed to the listeners.

Returns:

`void`

### `listenerCount(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Returns a number of listeners added to the given event.

Returns:

`number`

### `release()`

Android

iOS

tvOS

A function that detaches the JS and native objects to let the native object deallocate before the JS object gets deallocated by the JS garbage collector. Any subsequent calls to native functions of the object will throw an error as it is no longer associated with its native counterpart.

In most cases, you should never need to use this function, except some specific performance-critical cases when manual memory management makes sense and the native object is known to exclusively retain some native memory (such as binary data or image bitmap). Before calling this function, you should ensure that nothing else will use this object later on. Shared objects created by React hooks are usually automatically released in the effect's cleanup phase, for example: `useVideoPlayer()` from `expo-video` and `useImage()` from `expo-image`.

Returns:

`void`

### `removeAllListeners(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `'progress'`




Removes all listeners for the given event name.

Returns:

`void`

### `removeListener(eventName, listener)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`
listener| `UploadTaskEvents[EventName]`




Removes a listener for the given event name.

Returns:

`void`

### `start(url, file, options)`

Android

iOS

tvOS

Parameter| Type
---|---
url| `string`
file| `File`
options| `Record<string, any>`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<UploadResult>`

### `startObserving(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Function that is automatically invoked when the first listener for an event with the given name is added. Override it in a subclass to perform some additional setup once the event started being observed.

Returns:

`void`

### `stopObserving(eventName)`

Android

iOS

tvOS

Parameter| Type
---|---
eventName| `EventName`




Function that is automatically invoked when the last listener for an event with the given name is removed. Override it in a subclass to perform some additional cleanup once the event is no longer observed.

Returns:

`void`

### `uploadAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<UploadResult>`

### `FileHandle`

Android

iOS

tvOS

FileHandle Properties

### `offset`

Android

iOS

tvOS

Literal type: `union`

A property that indicates the current byte offset in the file. Calling `readBytes` or `writeBytes` will read or write a specified amount of bytes starting from this offset. The offset is incremented by the number of bytes read or written. The offset can be set to any value within the file size. If the offset is set to a value greater than the file size, the next write operation will append data to the end of the file. Null if the file handle is closed.

Acceptable values are: `number` | `null`

### `size`

Android

iOS

tvOS

Literal type: `union`

A size of the file in bytes or `null` if the file handle is closed.

Acceptable values are: `number` | `null`

FileHandle Methods

### `close()`

Android

iOS

tvOS

Closes the file handle. This allows the file to be deleted, moved or read by a different process. Subsequent calls to `readBytes` or `writeBytes` will throw an error.

Returns:

`void`

### `readBytes(length)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
length| `number`| The number of bytes to read.




Reads the specified amount of bytes from the file at the current offset. Max amount of bytes read at once is capped by ArrayBuffer max size (32 bit signed MAX_INT on Android and 64 bit on iOS), but you can read from a FileHandle multiple times.

Returns:

`[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)<[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)>`

### `writeBytes(bytes)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
bytes| `[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)`| A `Uint8Array` array containing bytes to write.




Writes the specified bytes to the file at the current offset.

Returns:

`void`

## Methods

> Deprecated: Use `new File().copy()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.copyAsync(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options| `RelocatingOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.createDownloadResumable(uri, fileUri, options, callback, resumeData)`

Android

iOS

tvOS

Parameter| Type
---|---
uri| `string`
fileUri| `string`
options(optional)| `DownloadOptions`
callback(optional)| `FileSystemNetworkTaskProgressCallback<DownloadProgressData>`
resumeData(optional)| `string`




Returns:

`any`

> Deprecated: Import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.createUploadTask(url, fileUri, options, callback)`

Android

iOS

tvOS

Parameter| Type
---|---
url| `string`
fileUri| `string`
options(optional)| `FileSystemUploadOptions`
callback(optional)| `FileSystemNetworkTaskProgressCallback<UploadProgressData>`




Returns:

`any`

> Deprecated: Use `new File().delete()` or `new Directory().delete()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.deleteAsync(fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`
options(optional)| `DeletingOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated

### `FileSystem.deleteLegacyDocumentDirectoryAndroid()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `File.downloadFileAsync` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.downloadAsync(uri, fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
uri| `string`
fileUri| `string`
options(optional)| `DownloadOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<FileSystemDownloadResult>`

> Deprecated: Import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.getContentUriAsync(fileUri)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `Paths.availableDiskSpace` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.getFreeDiskStorageAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number>`

> Deprecated: Use `new File().info` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.getInfoAsync(fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`
options(optional)| `InfoOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<FileInfo>`

> Deprecated: Use `Paths.totalDiskSpace` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.getTotalDiskCapacityAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number>`

> Deprecated: Use `new Directory().create()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.makeDirectoryAsync(fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`
options(optional)| `MakeDirectoryOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `new File().move()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.moveAsync(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options| `RelocatingOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `new File().text()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.readAsStringAsync(fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`
options(optional)| `ReadingOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `new Directory().list()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.readDirectoryAsync(fileUri)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string[]>`

> Deprecated: Use `@expo/fetch` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.uploadAsync(url, fileUri, options)`

Android

iOS

tvOS

Parameter| Type
---|---
url| `string`
fileUri| `string`
options(optional)| `FileSystemUploadOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<FileSystemUploadResult>`

> Deprecated: Use `new File().write()` or import this method from `expo-file-system/legacy`. This method will throw in runtime.

### `FileSystem.writeAsStringAsync(fileUri, contents, options)`

Android

iOS

tvOS

Parameter| Type
---|---
fileUri| `string`
contents| `string`
options(optional)| `WritingOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

## Types

### `DirectoryCreateOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
idempotent(optional)| `boolean`| This flag controls whether the `create` operation is idempotent (safe to call multiple times without error). If `true`, creating a file or directory that already exists will succeed silently. If `false`, an error will be thrown when the target already exists.Default:`false`
intermediates(optional)| `boolean`| Whether to create intermediate directories if they do not exist.Default:`false`
overwrite(optional)| `boolean`| Whether to overwrite the directory if it exists.Default:`false`

### `DirectoryInfo`

Android

iOS

tvOS

Property| Type| Description
---|---|---
creationTime(optional)| `number`| A creation time of the directory expressed in milliseconds since epoch. Returns null if the Android version is earlier than API 26.
exists| `boolean`| Indicates whether the directory exists.
files(optional)| `string[]`| A list of file names contained within a directory.
modificationTime(optional)| `number`| The last modification time of the directory expressed in milliseconds since epoch.
size(optional)| `number`| The size of the file in bytes.
uri(optional)| `string`| A `file://` URI pointing to the directory.

### `DownloadOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
headers(optional)| `undefined`| The headers to send with the request.
idempotent(optional)| `boolean`| This flag controls whether the `download` operation is idempotent (safe to call multiple times without error). If `true`, downloading a file that already exists overwrites the previous one. If `false`, an error is thrown when the target file already exists.Default:`false`
onProgress(optional)| `(data: DownloadProgress) => void`| A callback that is invoked with progress updates during the download.
signal(optional)| `AbortSignal`| An `AbortSignal` that can be used to cancel the download. When the signal is aborted, the download is cancelled and the promise rejects with an `AbortError`.

### `DownloadPauseState`

Android

iOS

tvOS

Represents the state of a paused download that can be persisted and resumed later.

Property| Type| Description
---|---|---
fileUri| `string`| The destination file or directory URI.
headers(optional)| `Record<string, string>`| Custom headers that were used for the download request.
isDirectory| `boolean`| Whether the destination is a directory. When `true`, the filename is derived from the URL.
resumeData(optional)| `string`| Platform-specific opaque resume data.
url| `string`| The URL of the download.

### `DownloadProgress`

Android

iOS

tvOS

Data provided to the `onProgress` callback during a file download.

Property| Type| Description
---|---|---
bytesWritten| `number`| The number of bytes written so far.
totalBytes| `number`| The total number of bytes expected to be downloaded. `-1` if the server did not provide a `Content-Length` header.

### `DownloadTaskOptions`

Android

iOS

tvOS

Options for download task operations.

Property| Type| Description
---|---|---
headers(optional)| `Record<string, string>`| Custom headers to include in the request.
onProgress(optional)| `(data: DownloadProgress) => void`| Callback for download progress updates.
sessionType(optional)| `NetworkTaskSessionType`| Only for: iOS
Determines whether the iOS native session should continue in the background. Android accepts this option for API consistency and ignores it. When set to `'background'`, the native transfer may continue after the app is suspended. However, the JavaScript `DownloadTask` instance is not restored if the app is terminated or relaunched, so its promise, progress callbacks, and cancellation state are only available while the original JS runtime is still alive.Default:`'background'`
signal(optional)| `AbortSignal`| AbortSignal to cancel the download.

### `DownloadTaskState`

Android

iOS

tvOS

Literal Type: `string`

Represents the current state of a download task.

Acceptable values are: `'idle'` | `'active'` | `'paused'` | `'completed'` | `'cancelled'` | `'error'`

### `FileCreateOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
intermediates(optional)| `boolean`| Whether to create intermediate directories if they do not exist.Default:`false`
overwrite(optional)| `boolean`| Whether to overwrite the file if it exists.Default:`false`

### `FileInfo`

Android

iOS

tvOS

Property| Type| Description
---|---|---
creationTime(optional)| `number`| A creation time of the file expressed in milliseconds since epoch. Returns null if the Android version is earlier than API 26.
exists| `boolean`| Indicates whether the file exists.
md5(optional)| `string`| Present if the `md5` option was truthy. Contains the MD5 hash of the file.
modificationTime(optional)| `number`| The last modification time of the file expressed in milliseconds since epoch.
size(optional)| `number`| The size of the file in bytes.
uri(optional)| `string`| A URI pointing to the file. This is the same as the `fileUri` input parameter and preserves its scheme (for example, `file://` or `content://`).

### `FileWriteOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
append(optional)| `boolean`| Whether to append the contents to the end of the file or overwrite the existing file.Default:`false`
encoding(optional)| `EncodingType | 'utf8' | 'base64'`| The encoding format to use when writing the file.Default:`FileSystem.EncodingType.UTF8`

### `InfoOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
md5(optional)| `boolean`| Whether to return the MD5 hash of the file.Default:`false`

### `NetworkTaskSessionType`

Android

iOS

tvOS

Literal Type: `string`

The native URL session mode used by iOS upload and download tasks.

Acceptable values are: `'background'` | `'foreground'`

### `PathInfo`

Android

iOS

tvOS

Property| Type| Description
---|---|---
exists| `boolean`| Indicates whether the path exists. Returns true if it exists; false if the path does not exist or if there is no read permission.
isDirectory| `boolean | null`| Indicates whether the path is a directory. Returns true or false if the path exists; otherwise, returns null.

### `PickFileCanceledResult`

Android

iOS

tvOS

Result type for a canceled file pick.

Property| Type| Description
---|---|---
canceled| `true`| -
result| `null`| -

### `PickFileGeneralOptions`

Android

iOS

tvOS

Property| Type| Description
---|---|---
initialUri(optional)| `string`| A URI pointing to an initial folder in which the file picker is opened.
mimeTypes(optional)| `string | string[]`| The [MIME type(s)](https://en.wikipedia.org/wiki/Media_type) of the documents that are available to be picked. It also supports wildcards like `'image/*'` to choose any image. To allow any type of document you can use `'*/*'`.Default:`'*/*'`
multipleFiles(optional)| `boolean`| Allows multiple files to be selected from the system UI.Default:`false`

### `PickMultipleFilesOptions`

Android

iOS

tvOS

Options for picking multiple files.

Type: `PickFileGeneralOptions` extended by:

Property| Type| Description
---|---|---
multipleFiles| `true`| -

### `PickMultipleFilesResult`

Android

iOS

tvOS

Literal Type: `union`

Result type for picking multiple files.

Acceptable values are: `PickMultipleFilesSuccessResult` | `PickFileCanceledResult`

### `PickMultipleFilesSuccessResult`

Android

iOS

tvOS

Result type for a successful picking multiple files.

Property| Type| Description
---|---|---
canceled| `false`| -
result| `File[]`| -

### `PickSingleFileOptions`

Android

iOS

tvOS

Options for picking a single file.

Type: `PickFileGeneralOptions` extended by:

Property| Type| Description
---|---|---
multipleFiles(optional)| `false`| -

### `PickSingleFileResult`

Android

iOS

tvOS

Literal Type: `union`

Result type for picking a single file.

Acceptable values are: `PickSingleFileSuccessResult` | `PickFileCanceledResult`

### `PickSingleFileSuccessResult`

Android

iOS

tvOS

Result type for successfully picking a single file.

Property| Type| Description
---|---|---
canceled| `false`| -
result| `File`| -

### `UploadOptions`

Android

iOS

tvOS

Options for upload operations.

Property| Type| Description
---|---|---
fieldName(optional)| `string`| The field name for the file in multipart uploads.Default:`'file'`
headers(optional)| `Record<string, string>`| Custom headers to include in the request.
httpMethod(optional)| `'POST' | 'PUT' | 'PATCH'`| The HTTP method to use.Default:`'POST'`
mimeType(optional)| `string`| The MIME type of the file.
onProgress(optional)| `(data: UploadProgress) => void`| Callback for upload progress updates.

> Note: For multipart uploads, the reported bytes may include multipart framing overhead (boundary strings, headers, form parameters) in addition to the file content.

parameters(optional)| `Record<string, string>`| Additional form parameters to include in multipart uploads.
sessionType(optional)| `NetworkTaskSessionType`| Only for: iOS
Determines whether the iOS native session should continue in the background. When set to `'background'`, the native transfer may continue after the app is suspended. However, the JavaScript `UploadTask` instance is not restored if the app is terminated or relaunched, so its promise, progress callbacks, and cancellation state are only available while the original JS runtime is still alive.Default:`'background'`
signal(optional)| `AbortSignal`| An `AbortSignal` that can be used to cancel the upload. When the signal is aborted, the upload is cancelled and the promise rejects with an `AbortError`.
uploadType(optional)| `UploadType`| The type of upload operation.Default:`UploadType.BINARY_CONTENT`

### `UploadProgress`

Android

iOS

tvOS

Represents upload progress data.

Property| Type| Description
---|---|---
bytesSent| `number`| The number of bytes sent so far.
totalBytes| `number`| The total number of bytes to send.

### `UploadResult`

Android

iOS

tvOS

Represents the result of an upload operation.

Property| Type| Description
---|---|---
body| `string`| The response body as a string.
headers| `Record<string, string>`| The response headers.
status| `number`| The HTTP status code.

### `UploadTaskState`

Android

iOS

tvOS

Type: `[Exclude](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers)<'idle' | 'active' | 'paused' | 'completed' | 'cancelled' | 'error', 'paused'>`

Represents the current state of an upload task.

### `WatchEvent`

Android

iOS

tvOS

Describes a change detected by a file system watcher.

Property| Type| Description
---|---|---
nativeEventFlags(optional)| `number`| Raw platform-specific event flags for advanced use cases. On Android: FileObserver event flags. On iOS: DispatchSource.FileSystemEvent flags.
newTarget(optional)| `T`| Only for: Android
For rename events, the new path after rename. Populated when MOVED_FROM and MOVED_TO events are correlated within the debounce window.
target| `T`| The file or directory that changed. For `renamed` events, this is the original path before the rename.
type| `WatchEventType`| The kind of change that occurred.

### `WatchEventType`

Android

iOS

tvOS

Literal Type: `string`

The type of change that triggered a watcher event.

  * `created` — a new file or directory was created
  * `modified` — the file contents or metadata changed
  * `deleted` — the file or directory was removed
  * `renamed` — the file or directory was renamed or moved


Acceptable values are: `'created'` | `'modified'` | `'deleted'` | `'renamed'`

### `WatchOptions`

Android

iOS

tvOS

Options for configuring a file system watcher.

Property| Type| Description
---|---|---
debounce(optional)| `number`| The debounce interval in milliseconds for coalescing rapid successive events into a single callback.Default:`DEFAULT_DEBOUNCE_MS`
events(optional)| `WatchEventType[]`| Limits which event types trigger the callback. If omitted, all event types are observed. On iOS, directory watchers only provide coarse-grained notifications that the directory itself changed, so filtering for child-level `created`, `deleted`, or `renamed` events is not reliable.

### `WatchSubscription`

Android

iOS

tvOS

A handle to an active file system watcher. Call `remove()` to stop watching and release resources.

Property| Type| Description
---|---|---
remove| `() => void`| Stops watching for changes and releases native resources. After calling this method, the callback will no longer be invoked.

## Enums

### `EncodingType`

Android

iOS

tvOS

#### `Base64`

`EncodingType.Base64 ＝ "base64"`

Binary, radix-64 representation.

#### `UTF8`

`EncodingType.UTF8 ＝ "utf8"`

Standard encoding format.

### `FileMode`

Android

iOS

tvOS

Specifies the access mode when opening a file handle.

#### `ReadOnly`

`FileMode.ReadOnly ＝ "r"`

Opens the file for reading only. The cursor is positioned at the beginning of the file.

#### `ReadWrite`

`FileMode.ReadWrite ＝ "rw"`

Opens the file for both reading and writing. The cursor is positioned at the beginning of the file.

> Note: This mode cannot be used with SAF (Storage Access Framework) `content://` URIs.

#### `WriteOnly`

`FileMode.WriteOnly ＝ "w"`

Opens the file for writing only. The cursor is positioned at the beginning of the file.

#### `Append`

`FileMode.Append ＝ "wa"`

Opens the file for writing only. The cursor is positioned at the end of the file.

> Note: For SAF files, this is a strict append-only mode. The cursor cannot be moved; calling `seek()` will have no effect.

#### `Truncate`

`FileMode.Truncate ＝ "wt"`

Opens the file for writing only and truncates the file to zero length (wipes content).

### `UploadType`

Android

iOS

tvOS

Represents the type of upload operation.

#### `BINARY_CONTENT`

`UploadType.BINARY_CONTENT ＝ 0`

Binary content upload - the file is uploaded as-is in the request body.

#### `MULTIPART`

`UploadType.MULTIPART ＝ 1`

Multipart form upload - the file is uploaded as part of a multipart/form-data request.