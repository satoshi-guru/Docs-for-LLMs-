---
title: "ExpoMediaLibrary"
url: https://docs.expo.dev/versions/latest/sdk/media-library
---

# ExpoMediaLibrary

# Expo MediaLibrary

A library that provides access to the device's media library.

Android

iOS

tvOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-media-library/src)[npm](https://www.npmjs.com/package/expo-media-library)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-media-library/src/CHANGELOG.md)

Bundled version:

~56.0.6

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-media-library` provides access to the user's media library, allowing apps to read existing images and videos, as well as save new ones.

> On Android, full access to the media library (the main purpose of this package) is allowed only for apps that require broad access to photos. See [details on Google Play's Photo and Video Permissions policy](https://support.google.com/googleplay/android-developer/answer/14115180).

## Installation

Terminal

Copy

`- ``npx expo install expo-media-library`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Usage

Add a new asset from the web

Add a new asset from the web

Copy

Open in Snack


    import { View, Text } from 'react-native';
    import { Image } from 'expo-image';
    import { useEffect, useState } from 'react';
    import { File, Paths } from 'expo-file-system';
    import { Asset, requestPermissionsAsync } from 'expo-media-library';

    export default function SaveToMediaLibraryExample() {
      const [asset, setAsset] = useState<Asset | null>(null);

      const downloadFile = async () => {
        const url = 'https://picsum.photos/200/300';
        const destinationFile = new File(Paths.cache, 'test_image.jpg');
        if (destinationFile.exists) {
          return destinationFile;
        } else {
          return File.downloadFileAsync(url, destinationFile);
        }
      };

      useEffect(() => {
        const downloadAndSaveAsset = async () => {
          const file = await downloadFile();
          const { status } = await requestPermissionsAsync();
          if (status !== 'granted') {
            return;
          }
          const asset = await Asset.create(file.uri);
          setAsset(asset);
        };

        downloadAndSaveAsset();
      }, []);

      return (
        <View>
          {asset ? (
            <>
              <Text>{asset.id}</Text>
              <Image source={{ uri: asset.id }} style={{ width: 200, height: 300 }} />
            </>
          ) : (
            <Text>Downloading and creating asset...</Text>
          )}
        </View>
      );
    }


Retrieve asset properties

Retrieve asset properties

Copy

Open in Snack


    import { View, Text } from 'react-native';
    import { useEffect, useState } from 'react';
    import { AssetField, MediaType, Query, requestPermissionsAsync } from 'expo-media-library';

    export default function RetrievingAssetPropertiesExample() {
      const [assetInfo, setAssetInfo] = useState<{
        id: string;
        filename: string;
        mediaType: string;
        width: number;
        height: number;
        creationTime: number | null;
        modificationTime: number | null;
      } | null>(null);

      useEffect(() => {
        const querySomeAsset = async () => {
          const { status } = await requestPermissionsAsync();
          if (status !== 'granted') {
            return;
          }

          const [asset] = await new Query().limit(1).eq(AssetField.MEDIA_TYPE, MediaType.IMAGE).exe();

          if (asset) {
            const filename = await asset.getFilename();
            const mediaType = (await asset.getMediaType()).toString();
            const width = await asset.getWidth();
            const height = await asset.getHeight();
            const creationTime = await asset.getCreationTime();
            const modificationTime = await asset.getModificationTime();
            setAssetInfo({
              id: asset.id,
              filename,
              mediaType,
              width,
              height,
              creationTime,
              modificationTime,
            });
          } else {
            console.log('No assets found in the media library.');
          }
        };

        querySomeAsset();
      }, []);

      return (
        <View>
          {assetInfo ? (
            <View>
              <Text>Asset ID: {assetInfo.id}</Text>
              <Text>Filename: {assetInfo.filename}</Text>
              <Text>Media Type: {assetInfo.mediaType}</Text>
              <Text>
                Dimensions: {assetInfo.width} x {assetInfo.height}
              </Text>
              <Text>
                Creation Time:{' '}
                {assetInfo.creationTime
                  ? new Date(assetInfo.creationTime).toLocaleString()
                  : 'Unavailable'}
              </Text>
              <Text>
                Modification Time:{' '}
                {assetInfo.modificationTime
                  ? new Date(assetInfo.modificationTime).toLocaleString()
                  : 'Unavailable'}
              </Text>
            </View>
          ) : (
            <Text>Fetching asset ...</Text>
          )}
        </View>
      );
    }


Create a new album

Create a new album

Copy

Open in Snack


    import { View, Text, FlatList, Image, Button } from 'react-native';
    import { useState } from 'react';
    import {
      Asset,
      AssetField,
      MediaType,
      Query,
      requestPermissionsAsync,
      Album,
    } from 'expo-media-library';

    export default function CreateAlbumExample() {
      const [assets, setAssets] = useState<Asset[]>([]);
      const [album, setAlbum] = useState<Album | null>(null);
      const [albumTitle, setAlbumTitle] = useState<string>('');

      const createAlbumWithAsset = async () => {
        await requestPermissionsAsync();

        const [asset] = await new Query().limit(1).eq(AssetField.MEDIA_TYPE, MediaType.IMAGE).exe();

        if (!asset) {
          console.log('No assets found in the media library.');
          return;
        }

        const newAlbum = await Album.create('MyNewAlbum', [asset]);

        setAlbum(newAlbum);
        setAlbumTitle(await newAlbum.getTitle());
        const albumAssets = await newAlbum.getAssets();
        setAssets(albumAssets);
      };

      return (
        <View style={{ flex: 1, padding: 20 }}>
          <Button title="Create Album and Add Asset" onPress={createAlbumWithAsset} />

          {assets.length > 0 ? (
            <>
              <Text style={{ marginTop: 20, fontSize: 18, fontWeight: 'bold' }}>
                Assets in {albumTitle}:
              </Text>
              <FlatList
                data={assets}
                keyExtractor={item => item.id}
                renderItem={({ item }) => (
                  <View style={{ marginVertical: 10 }}>
                    <Image
                      source={{ uri: item.id }}
                      style={{ width: 100, height: 100, borderRadius: 8 }}
                    />
                  </View>
                )}
              />
            </>
          ) : (
            <Text style={{ marginTop: 20 }}>{album ? 'Album is empty.' : 'No album created yet.'}</Text>
          )}
        </View>
      );
    }


## API

## Hooks

### `usePermissions(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options(optional)| `PermissionHookOptions<{  granularPermissions: GranularPermission[],  writeOnly: boolean }>`




Check or request permissions to access the media library. This uses both `requestPermissionsAsync` and `getPermissionsAsync` to interact with the permissions.

Returns:

`[PermissionResponse | null, RequestPermissionMethod<PermissionResponse>, GetPermissionMethod<PermissionResponse>]`

Example


    const [permissionResponse, requestPermission] = MediaLibrary.usePermissions({
      writeOnly: true,
      granularPermissions: ['photo'],
    });


## Classes

### `Album`

Android

iOS

tvOS

Type: Class extends `Album`

Album Properties

### `id`

Android

iOS

tvOS

Type: `string`

Unique identifier of the album. Can be used to re-instantiate an Album later.

Album Methods

### `add(assets)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
assets| `[Asset](/versions/latest/sdk/asset#asset) | [Asset[]](/versions/latest/sdk/asset#asset)`| The Asset or list of Asset objects to add.




Adds one or more assets to the album.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the assets have been added.

Example


    const asset = await Asset.create("file:///path/to/photo.png");
    await album.add(asset);


Example


    await album.add([asset1, asset2]);


### `create(name, assetsRefs, moveAssets)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
name| `string`| Name of the new album.
assetsRefs| `string[] | [Asset[]](/versions/latest/sdk/asset#asset)`| List of Asset objects or file paths (file:///...) to include.
moveAssets(optional)| `boolean`| On Android, whether to move assets into the album. Defaults to `true`.




A static function. Creates a new album with a given name and assets. On Android, if assets are provided and `moveAssets` is true, the assets will be moved into the new album. If false or not supported, the assets will be copied.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album>`

A promise resolving to the created Album.

Example


    const album = await Album.create("My Album", [asset]);
    console.log(await album.getTitle()); // "My Album"


### `delete()`

Android

iOS

tvOS

Permanently deletes the album from the device. On Android, it deletes the album and all its assets. On iOS, it deletes the album but keeps the assets in the main library.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the deletion has completed.

Example


    await album.delete();


### `delete(albums, deleteAssets)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
albums| `Album[]`| An array of Album instances to delete.
deleteAssets(optional)| `boolean`| On iOS, whether to delete the assets in the albums as well. Defaults to `false`.




A static function. Deletes multiple albums at once. On Android, assets are always deleted along with the album regardless of `deleteAssets`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the albums have been deleted.

Example


    const album = await Album.create("My Album", [asset]);
    await Album.delete([album]);


### `get(title)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
title| `string`| The title of the album to retrieve.




A static function. Retrieves an album by its title.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album | null>`

A promise resolving to the Album if found, or `null` if not found.

Example


    const album = await Album.get("Camera");
    if (album) {
      console.log(await album.getTitle()); // "Camera"
    }


### `getAll()`

Android

iOS

tvOS

A static function. Retrieves all albums on the device.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album[]>`

A promise resolving to an array of Album objects.

Example


    const albums = await Album.getAll();


### `getAssets()`

Android

iOS

tvOS

Retrieves all assets contained in the album.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[Asset[]](/versions/latest/sdk/asset#asset)>`

A promise resolving to an array of Asset objects.

Example


    const assets = await album.getAssets();
    console.log(assets.length);


### `getTitle()`

Android

iOS

tvOS

Gets the display title (name) of the album. Note that album titles are not guaranteed to be unique.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

A promise resolving to the album’s title string.

Example


    const title = await album.getTitle();
    console.log(title); // "Camera"


### `removeAssets(assets)`

iOS

Parameter| Type| Description
---|---|---
assets| `[Asset[]](/versions/latest/sdk/asset#asset)`| An array of Asset objects to remove from the album.




Removes assets from the album without deleting them from the library. This is supported only on iOS.

On Android, an asset can belong to only one album. To remove it from an album, delete it or add it to another album.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the assets have been removed.

Example


    const assets = await album.getAssets();
    await album.removeAssets(assets.slice(0, 2));


### `Asset`

Android

iOS

tvOS

Type: Class extends `[Asset](/versions/latest/sdk/asset#asset)`

Asset Properties

### `id`

Android

iOS

tvOS

Type: `string`

ID of the asset. Can be used to re-instantiate an Asset later. For android it is a contentUri and PHAsset localIdentifier URI for iOS.

Asset Methods

### `create(filePath, album)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
filePath| `string`| Local filesystem path (for example, `file:///...`) of the file to import.
album(optional)| `Album`| Optional Album instance to place the asset in.




A static function. Creates a new asset from a given file path. Optionally associates the asset with an album. On Android, if not specified, the asset will be placed in the default "Pictures" directory.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[Asset](/versions/latest/sdk/asset#asset)>`

A promise resolving to the created Asset.

Example


    const asset = await Asset.create("file:///storage/emulated/0/DCIM/Camera/IMG_20230915_123456.jpg");
    console.log(await asset.getFilename()); // "IMG_20230915_123456.jpg"


### `delete()`

Android

iOS

tvOS

Deletes the asset from the device’s media store.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the deletion has completed.

Example


    await asset.delete();


### `delete(assets)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
assets| `[Asset[]](/versions/latest/sdk/asset#asset)`| An array of Asset instances to delete.




A static function. Deletes multiple assets from the device's media store.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the deletion has completed.

### `getAlbums()`

Android

iOS

tvOS

Gets the albums containing this asset. On Android, an asset is typically associated with a single album. On iOS, an asset may belong to multiple albums.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album[]>`

A promise resolving to an array of Album objects.

Example


    const albums = await asset.getAlbums();
    console.log(albums.length);


### `getCreationTime()`

Android

iOS

tvOS

Gets the creation time of the asset.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number | null>`

A promise resolving to the UNIX timestamp in milliseconds, or `null` if unavailable.

### `getDuration()`

Android

iOS

tvOS

Gets the duration of the asset. Applies only to assets with media type MediaType.audio or MediaType.video. For other media types, it returns `null`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number | null>`

A promise resolving to the duration in milliseconds, or `null` if not applicable.

### `getExif()`

Android

iOS

tvOS

Gets the exif data of the MediaType.image asset. On Android, this method requires the `ACCESS_MEDIA_LOCATION` permission to access location metadata.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<undefined>`

A promise resolving to the exif data object or an empty object if the exif data is unavailable.

### `getFavorite()`

Android

iOS

tvOS

Gets whether the asset is marked as a favorite. On iOS, this checks if the asset is part of the system "Favorites" smart album. On Android, this reads the `IS_FAVORITE` column from MediaStore (requires Android 10+; always returns `false` on older versions).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

A promise resolving to `true` if the asset is a favorite, or `false` otherwise.

Example


    const isFavorite = await asset.getFavorite();
    console.log(isFavorite); // true or false


### `getFilename()`

Android

iOS

tvOS

Gets the filename of the asset, including its extension.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

A promise resolving to the filename string.

### `getHeight()`

Android

iOS

tvOS

Gets the height of the asset in pixels. Only applicable for image and video assets.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number>`

A promise resolving to the height in pixels.

### `getInfo()`

Android

iOS

tvOS

Gets detailed information about the asset.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<AssetInfo>`

A promise resolving to an AssetInfo

### `getIsInCloud()`

iOS

Gets whether the asset is stored in iCloud and not available locally. This does not trigger a download of the asset.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

A promise resolving to `true` if the asset is stored in iCloud and not available locally.

### `getLivePhotoVideoUri()`

iOS

Gets the URI of the paired video for a Live Photo asset. The video is extracted to a temporary file.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

A promise resolving to a `file://` URI string, or `null` if the asset is not a Live Photo.

### `getLocation()`

Android

iOS

tvOS

Gets the location of the asset. On Android, this method requires the `ACCESS_MEDIA_LOCATION` permission to access location metadata.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Location | null>`

A promise resolving to the Location object or `null` if the location data is unavailable.

### `getMediaSubtypes()`

iOS

Gets the media subtypes of the asset, describing specific variations such as Live Photo, panorama, HDR, etc.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<MediaSubtype[]>`

A promise resolving to an array of MediaSubtype strings. Returns an empty array if no subtypes apply.

### `getMediaType()`

Android

iOS

tvOS

Gets the media type of the asset (image, video, audio or unknown).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<MediaType>`

A promise resolving to a MediaType enum value.

### `getModificationTime()`

Android

iOS

tvOS

Gets the last modification time of the asset.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number | null>`

A promise resolving to the UNIX timestamp in milliseconds, or `null` if unavailable.

### `getOrientation()`

iOS

Gets the EXIF display orientation of the asset. Only applicable for assets with media type MediaType.image.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number | null>`

A promise resolving to a value between 1 and 8 as defined by the [EXIF orientation specification](http://sylvana.net/jpegcrop/exif_orientation.html), or `null` if unavailable.

### `getShape()`

Android

iOS

tvOS

Gets the shape (width and height) of the asset.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Shape | null>`

A promise resolving to the Shape object, or `null` if any dimension is unavailable.

### `getUri()`

Android

iOS

tvOS

Gets the URI pointing to the asset’s location in the system. Example, for Android: `file:///storage/emulated/0/DCIM/Camera/IMG_20230915_123456.jpg`.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

A promise resolving to the string URI.

### `getWidth()`

Android

iOS

tvOS

Gets the width of the asset in pixels. Only applicable for image and video assets.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number>`

A promise resolving to the width in pixels.

### `setFavorite(isFavorite)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
isFavorite| `boolean`| Whether the asset should be marked as favorite.




Marks or unmarks the asset as a favorite. On iOS, this adds or removes the asset from the system "Favorites" smart album. On Android, this updates the `IS_FAVORITE` column in MediaStore (requires Android 10+; no-op on older versions).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that resolves once the operation has completed.

> Note: On Android, some third-party gallery apps maintain their own favorites list and may not reflect changes made through this method.

Example


    await asset.setFavorite(true);


### `Query`

Android

iOS

tvOS

Type: Class extends `Query`

Query Methods

### `album(album)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
album| `Album`| The album to filter assets by.




Filters assets to only those contained in the specified album.

Returns:

`Query`

The updated query object for chaining.

### `eq(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `T`| an AssetField to filter by.
value| `AssetFieldValueMap[T]`| The value that the field should equal. Each field has a specific unique type.




Filters assets where the specified field is equal to the given value.

Returns:

`Query`

The updated query object for chaining.

### `exe()`

Android

iOS

tvOS

Executes the query and retrieves the matching assets.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[Asset[]](/versions/latest/sdk/asset#asset)>`

A promise that resolves to an array of Asset objects that match the query criteria.

Example


    const assets = await new Query()
     .eq(AssetField.MEDIA_TYPE, MediaType.IMAGE)
     .lte(AssetField.HEIGHT, 1080)
     .orderBy(AssetField.CREATION_TIME)
     .limit(20)
     .exe();


### `gt(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `AssetField`| an AssetField to filter by.
value| `number`| The value that the field should be greater than.




Filters assets where the specified field is greater than the given value.

Returns:

`Query`

The updated query object for chaining.

### `gte(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `AssetField`| an AssetField to filter by.
value| `number`| The value that the field should be greater than or equal to.




Filters assets where the specified field is greater than or equal to the given value.

Returns:

`Query`

### `limit(limit)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
limit| `number`| The maximum number of results to return.




Limits the number of results returned by the query.

Returns:

`Query`

The updated query object for chaining.

### `lt(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `AssetField`| an AssetField to filter by.
value| `number`| The value that the field should be less than.




Filters assets where the specified field is less than the given value.

Returns:

`Query`

The updated query object for chaining.

### `lte(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `AssetField`| an AssetField to filter by.
value| `number`| The value that the field should be less than or equal to.




Filters assets where the specified field is less than or equal to the given value.

Returns:

`Query`

The updated query object for chaining.

### `offset(offset)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
offset| `number`| The number of results to skip.




Skips the specified number of results.

Returns:

`Query`

The updated query object for chaining.

### `orderBy(sortDescriptors)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
sortDescriptors| `AssetField | SortDescriptor`| An instance of SortDescriptor or an AssetField. If an AssetField is provided, the sorting will be done in ascending order by default.




Orders the results by the specified sort descriptor or asset field.

Returns:

`Query`

### `within(field, value)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
field| `T`| an AssetField to filter by.
value| `undefined`| An array of values that the field should match. Each field has a specific unique type.




Filters assets where the specified field's value is in the given array of values.

Returns:

`Query`

The updated query object for chaining.

## Methods

> Deprecated: Use `album.add()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.addAssetsToAlbumAsync(assets, album, copy)`

Android

iOS

tvOS

Parameter| Type
---|---
assets| `AssetRef | AssetRef[]`
album| `AlbumRef`
copy(optional)| `boolean`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.albumNeedsMigrationAsync(album)`

Android

iOS

tvOS

Parameter| Type
---|---
album| `AlbumRef`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Use `Album.create()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.createAlbumAsync(albumName, asset, copyAsset, initialAssetLocalUri)`

Android

iOS

tvOS

Parameter| Type
---|---
albumName| `string`
asset(optional)| `AssetRef`
copyAsset(optional)| `boolean`
initialAssetLocalUri(optional)| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album>`

> Deprecated: Use `Asset.create()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.createAssetAsync(localUri, album)`

Android

iOS

tvOS

Parameter| Type
---|---
localUri| `string`
album(optional)| `AlbumRef`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[Asset](/versions/latest/sdk/asset#asset)>`

> Deprecated: Use `album.delete()` or `Album.delete()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.deleteAlbumsAsync(albums, assetRemove)`

Android

iOS

tvOS

Parameter| Type
---|---
albums| `AlbumRef | AlbumRef[]`
assetRemove(optional)| `boolean`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Use `asset.delete()` or `Asset.delete()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.deleteAssetsAsync(assets)`

Android

iOS

tvOS

Parameter| Type
---|---
assets| `AssetRef | AssetRef[]`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Use `Album.get(title)` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.getAlbumAsync(title)`

Android

iOS

tvOS

Parameter| Type
---|---
title| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album>`

> Deprecated: Use `Album.getAll()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.getAlbumsAsync(options)`

Android

iOS

tvOS

Parameter| Type
---|---
options(optional)| `AlbumsOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album[]>`

> Deprecated: Use `asset.getInfo()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.getAssetInfoAsync(asset, options)`

Android

iOS

tvOS

Parameter| Type
---|---
asset| `AssetRef`
options(optional)| `MediaLibraryAssetInfoQueryOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<AssetInfo>`

> Deprecated: Use the `Query` class or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.getAssetsAsync(assetsOptions)`

Android

iOS

tvOS

Parameter| Type
---|---
assetsOptions(optional)| `AssetsOptions`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PagedInfo<[Asset](/versions/latest/sdk/asset#asset)>>`

> Deprecated: Import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.getMomentsAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Album[]>`

### `MediaLibrary.getPermissionsAsync(writeOnly, granularPermissions)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
writeOnly(optional)| `boolean`| Whether to check write-only access without read permissions. Defaults to `false`.Default:`false`
granularPermissions(optional)| `GranularPermission[]`| A list of `GranularPermission` values. This parameter has an effect only on Android 13 and newer. By default, `expo-media-library` will ask for all possible permissions.




Checks user's permissions for accessing media library.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

A promise that fulfils with `PermissionResponse` object.

> Deprecated: Import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.isAvailableAsync()`

Android

iOS

tvOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.migrateAlbumIfNeededAsync(album)`

Android

iOS

tvOS

Parameter| Type
---|---
album| `AlbumRef`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

### `MediaLibrary.presentPermissionsPicker(mediaTypes)`

Android 14+

iOS

Parameter| Type| Description
---|---|---
mediaTypes(optional)| `MediaTypeFilter[]`| Limits the type(s) of media that the user will be granting access to. By default, a list that shows both photos and videos is presented.




Allows the user to update the assets that your app has access to. The system modal is only displayed if the user originally allowed only `limited` access to their media library, otherwise this method is a no-op.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A promise that either rejects if the method is unavailable, or resolves to `void`.

> Note: This method doesn't inform you if the user changes which assets your app has access to. That information is only exposed by iOS, and to obtain it, you need to subscribe for updates to the user's media library using `addListener()`. If `hasIncrementalChanges` is `false`, the user changed their permissions.

> Deprecated: Use `presentPermissionsPicker()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.presentPermissionsPickerAsync(mediaTypes)`

Android

iOS

tvOS

Parameter| Type
---|---
mediaTypes(optional)| `MediaTypeFilter[]`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `album.removeAssets()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.removeAssetsFromAlbumAsync(assets, album)`

Android

iOS

tvOS

Parameter| Type
---|---
assets| `AssetRef | AssetRef[]`
album| `AlbumRef`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Use `subscription.remove()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.removeSubscription(subscription)`

Android

iOS

tvOS

Parameter| Type
---|---
subscription| `EventSubscription`




Returns:

`void`

### `MediaLibrary.requestPermissionsAsync(writeOnly, granularPermissions)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
writeOnly(optional)| `boolean`| Whether to request write-only access without read permissions. Defaults to `false`.Default:`false`
granularPermissions(optional)| `GranularPermission[]`| A list of `GranularPermission` values. This parameter has an effect only on Android 13 and newer. By default, `expo-media-library` will ask for all possible permissions.

> When using granular permissions with a custom config plugin configuration, make sure that all the requested permissions are included in the plugin.




Asks the user to grant permissions for accessing media in user's media library.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PermissionResponse>`

A promise that fulfils with `PermissionResponse` object.

> Deprecated: Use `Asset.create()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.saveToLibraryAsync(localUri)`

Android

iOS

tvOS

Parameter| Type
---|---
localUri| `string`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

> Deprecated: Use `asset.setFavorite()` or import this method from `expo-media-library/legacy`. This method will throw in runtime.

### `MediaLibrary.setAssetFavoriteAsync(asset, isFavorite)`

Android

iOS

tvOS

Parameter| Type
---|---
asset| `AssetRef`
isFavorite| `boolean`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

## Event Subscriptions

### `MediaLibrary.addListener(listener)`

Android

iOS

tvOS

Parameter| Type| Description
---|---|---
listener| `(event: MediaLibraryAssetsChangeEvent) => void`| A callback that is fired when any assets have been inserted or deleted from the library. On Android it's invoked with an empty object. On iOS it's invoked with `MediaLibraryAssetsChangeEvent` object.




Subscribes for updates in user's media library.

Returns:

`EventSubscription`

An `EventSubscription` object that you can call `remove()` on when you would like to unsubscribe the listener.

### `MediaLibrary.removeAllListeners()`

Android

iOS

tvOS

Removes all listeners.

Returns:

`void`

## Interfaces

### `EventSubscription`

Android

iOS

tvOS

A subscription object that allows to conveniently remove an event listener from the emitter.

EventSubscription Methods

### `remove()`

Android

iOS

tvOS

Removes an event listener for which the subscription has been created. After calling this function, the listener will no longer receive any events from the emitter.

Returns:

`void`

## Types

### `AssetFieldValueMap`

Android

iOS

tvOS

Property| Type| Description
---|---|---
creationTime| `number`| -
duration| `number`| -
height| `number`| -
mediaType| `MediaType`| -
modificationTime| `number`| -
width| `number`| -

### `AssetInfo`

Android

iOS

tvOS

Property| Type| Description
---|---|---
creationTime| `number | null`| -
duration| `number | null`| -
filename| `string`| -
height| `number`| -
id| `string`| -
isFavorite| `boolean`| -
mediaType| `MediaType`| -
modificationTime| `number | null`| -
uri| `string`| -
width| `number`| -

### `GranularPermission`

Android

iOS

tvOS

Literal Type: `string`

Acceptable values are: `'audio'` | `'photo'` | `'video'`

### `Location`

Android

iOS

tvOS

Property| Type| Description
---|---|---
latitude| `number`| -
longitude| `number`| -

### `MediaLibraryAssetsChangeEvent`

Android

iOS

tvOS

An event emitted when assets in the media library change.

Property| Type| Description
---|---|---
deletedAssets(optional)| `string[]`| Only for: iOS
Array of asset IDs (`ph://` URIs) that have been deleted from the library. Only populated when `hasIncrementalChanges` is `true`.
hasIncrementalChanges| `boolean`| Whether the media library's changes can be described as incremental changes. `true` indicates the changes are described by the `insertedAssets`, `deletedAssets` and `updatedAssets` values. `false` indicates that the scope of changes is too large and you should perform a full assets reload. On Android this is always `false` because the platform does not provide incremental change details.
insertedAssets(optional)| `string[]`| Only for: iOS
Array of asset IDs (`ph://` URIs) that have been inserted to the library. Only populated when `hasIncrementalChanges` is `true`.
updatedAssets(optional)| `string[]`| Only for: iOS
Array of asset IDs (`ph://` URIs) that have been updated. Only populated when `hasIncrementalChanges` is `true`.

### `MediaTypeFilter`

Android

iOS

tvOS

Literal Type: `string`

Acceptable values are: `'photo'` | `'video'`

### `PermissionHookOptions`

Android

iOS

tvOS

Literal Type: `union`

Acceptable values are: `PermissionHookBehavior` | `Options`

### `PermissionResponse`

Android

iOS

tvOS

An object obtained by permissions get and request functions.

Property| Type| Description
---|---|---
canAskAgain| `boolean`| Indicates if user can be asked again for specific permission. If not, one should be directed to the Settings app in order to enable/disable the permission.
expires| `PermissionExpiration`| Determines time when the permission expires.
granted| `boolean`| A convenience boolean that indicates if the permission is granted.
status| `PermissionStatus`| Determines the status of the permission.

### `Shape`

Android

iOS

tvOS

Property| Type| Description
---|---|---
height| `number`| -
width| `number`| -

### `SortDescriptor`

Android

iOS

tvOS

Property| Type| Description
---|---|---
ascending(optional)| `boolean`| -
key| `AssetField`| -

## Enums

### `AssetField`

Android

iOS

tvOS

#### `CREATION_TIME`

`AssetField.CREATION_TIME ＝ "creationTime"`

#### `DURATION`

`AssetField.DURATION ＝ "duration"`

#### `HEIGHT`

`AssetField.HEIGHT ＝ "height"`

#### `MEDIA_TYPE`

`AssetField.MEDIA_TYPE ＝ "mediaType"`

#### `MODIFICATION_TIME`

`AssetField.MODIFICATION_TIME ＝ "modificationTime"`

#### `WIDTH`

`AssetField.WIDTH ＝ "width"`

### `MediaSubtype`

iOS

Describes specific variations of asset media. Maps to [`PHAssetMediaSubtype`](https://developer.apple.com/documentation/photokit/phassetmediasubtype).

#### `DEPTH_EFFECT`

`MediaSubtype.DEPTH_EFFECT ＝ "depthEffect"`

#### `HDR`

`MediaSubtype.HDR ＝ "hdr"`

#### `HIGH_FRAME_RATE`

`MediaSubtype.HIGH_FRAME_RATE ＝ "highFrameRate"`

#### `LIVE_PHOTO`

`MediaSubtype.LIVE_PHOTO ＝ "livePhoto"`

#### `PANORAMA`

`MediaSubtype.PANORAMA ＝ "panorama"`

#### `SCREENSHOT`

`MediaSubtype.SCREENSHOT ＝ "screenshot"`

#### `SPATIAL_MEDIA`

`MediaSubtype.SPATIAL_MEDIA ＝ "spatialMedia"`

#### `STREAM`

`MediaSubtype.STREAM ＝ "stream"`

#### `TIME_LAPSE`

`MediaSubtype.TIME_LAPSE ＝ "timelapse"`

#### `VIDEO_CINEMATIC`

`MediaSubtype.VIDEO_CINEMATIC ＝ "videoCinematic"`

### `MediaType`

Android

iOS

tvOS

#### `AUDIO`

`MediaType.AUDIO ＝ "audio"`

#### `IMAGE`

`MediaType.IMAGE ＝ "image"`

#### `UNKNOWN`

`MediaType.UNKNOWN ＝ "unknown"`

#### `VIDEO`

`MediaType.VIDEO ＝ "video"`