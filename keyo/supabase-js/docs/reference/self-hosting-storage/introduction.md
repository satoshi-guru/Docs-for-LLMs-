---
title: "Self-Hosting Storage"
url: https://supabase.com/docs/reference/self-hosting-storage/introduction
---

# Self-Hosting Storage

Storage Server Reference

# Self-Hosting Storage

An S3 compatible object storage service that integrates with Postgres.

  * Uses Postgres as it's datastore for storing metadata
  * Authorization rules are written as Postgres Row Level Security policies
  * Integrates with S3 as the storage backend (with more in the pipeline!)
  * Extremely lightweight and performant


### Client libraries#

  * [JavaScript](https://github.com/supabase/storage-js)
  * [Dart](https://github.com/supabase/storage-dart)


### Additional links#

  * [Source code](https://github.com/supabase/storage-api)
  * [Known bugs and issues](https://github.com/supabase/storage-js/issues)
  * [Storage guides](/docs/guides/storage)
  * [OpenAPI docs](https://supabase.github.io/storage/)
  * [Why we built a new object storage service](https://supabase.com/blog/supabase-storage)


* * *

## Create a bucket

post`/bucket/`

### Body

  * name

Required

string

  * id

Optional

string

  * public

Optional

boolean

  * type

Optional

enum

Accepted values

  * file_size_limit

Optional

any of the following options

Options

  * allowed_mime_types

Optional

Array<string>


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "name": "avatars"

    3

    }

* * *

## Gets all buckets

head`/bucket`

### Query parameters

  * limit

Optional

integer

  * offset

Optional

integer

  * sortColumn

Optional

enum

Accepted values

  * sortOrder

Optional

enum

Accepted values

  * search

Optional

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    [

    2

      {

    3

        "id": "bucket2",

    4

        "name": "bucket2",

    5

        "public": false,

    6

        "file_size_limit": 1000000,

    7

        "allowed_mime_types": [

    8

          "image/png",

    9

          "image/jpeg"

    10

        ],

    11

        "owner": "4d56e902-f0a0-4662-8448-a4d9e643c142",

    12

        "created_at": "2021-02-17T04:43:32.770206+00:00",

    13

        "updated_at": "2021-02-17T04:43:32.770206+00:00"

    14

      }

    15

    ]

* * *

## Empty a bucket

post`/bucket/{bucketId}/empty`

### Path parameters

  * bucketId

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "message": "Empty bucket has been queued. Completion may take up to an hour."

    3

    }

* * *

## Get details of a bucket

head`/bucket/{bucketId}`

### Path parameters

  * bucketId

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "id": "lorem",

    3

      "name": "lorem",

    4

      "owner": "lorem",

    5

      "owner_id": "lorem",

    6

      "public": true,

    7

      "type": "STANDARD",

    8

      "created_at": "lorem",

    9

      "updated_at": "lorem"

    10

    }

* * *

## Update properties of a bucket

put`/bucket/{bucketId}`

### Path parameters

  * bucketId

Required

string


### Body

Any of the following:

Details

Details

Details

### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "message": "Successfully updated"

    3

    }

* * *

## Delete a bucket

delete`/bucket/{bucketId}`

### Path parameters

  * bucketId

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "message": "Successfully deleted"

    3

    }

* * *

## Delete an object

delete`/object/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "message": "Successfully deleted"

    3

    }

* * *

## Get object

get`/object/{bucketName}/{*}`

Serve objects

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Response codes

  * 4XX


* * *

## Update the object at an existing key

put`/object/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "Id": "lorem",

    3

      "Key": "avatars/folder/cat.png"

    4

    }

* * *

## Upload a new object

post`/object/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "Id": "lorem",

    3

      "Key": "avatars/folder/cat.png"

    4

    }

* * *

## Delete multiple objects

delete`/object/{bucketName}`

### Path parameters

  * bucketName

Required

string


### Body

  * prefixes

Required

Array<string>


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    [

    2

      {

    3

        "name": "folder/cat.png",

    4

        "bucket_id": "avatars",

    5

        "owner": "317eadce-631a-4429-a0bb-f19a7a517b4a",

    6

        "id": "eaa8bdb5-2e00-4767-b5a9-d2502efe2196",

    7

        "updated_at": "2021-04-06T16:30:35.394674+00:00",

    8

        "created_at": "2021-04-06T16:30:35.394674+00:00",

    9

        "last_accessed_at": "2021-04-06T16:30:35.394674+00:00",

    10

        "metadata": {

    11

          "size": 1234

    12

        }

    13

      }

    14

    ]

* * *

## Retrieve an object

get`/object/authenticated/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Query parameters

  * download

Optional

string


### Response codes

  * 4XX


* * *

## Generate a presigned url to retrieve an object

post`/object/sign/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Body

  * expiresIn

Required

integer

  * transform

Optional

object

Object schema


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "signedURL": "/object/sign/avatars/folder/cat.png?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cmwiOiJhdmF0YXJzL2ZvbGRlci9jYXQucG5nIiwiaWF0IjoxNjE3NzI2MjczLCJleHAiOjE2MTc3MjcyNzN9.s7Gt8ME80iREVxPhH01ZNv8oUn4XtaWsmiQ5csiUHn4"

    3

    }

* * *

## Retrieve an object via a presigned URL

head`/object/sign/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Query parameters

  * download

Optional

string

  * token

Required

string


### Response codes

  * 4XX


* * *

## Generate presigned urls to retrieve objects

post`/object/sign/{bucketName}`

### Path parameters

  * bucketName

Required

string


### Body

  * expiresIn

Required

integer

  * paths

Required

Array<string>


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    [

    2

      {

    3

        "error": "Either the object does not exist or you do not have access to it",

    4

        "path": "folder/cat.png",

    5

        "signedURL": "/object/sign/avatars/folder/cat.png?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cmwiOiJhdmF0YXJzL2ZvbGRlci9jYXQucG5nIiwiaWF0IjoxNjE3NzI2MjczLCJleHAiOjE2MTc3MjcyNzN9.s7Gt8ME80iREVxPhH01ZNv8oUn4XtaWsmiQ5csiUHn4"

    6

      }

    7

    ]

* * *

## Moves an object

post`/object/move`

### Body

  * bucketId

Required

string

  * sourceKey

Required

string

  * destinationBucket

Optional

string

  * destinationKey

Required

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "message": "Successfully moved"

    3

    }

* * *

## Search for objects under a prefix

post`/object/list/{bucketName}`

### Path parameters

  * bucketName

Required

string


### Body

  * prefix

Required

string

  * limit

Optional

integer

  * offset

Optional

integer

  * sortBy

Optional

object

Object schema

  * search

Optional

string


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    [

    2

      {

    3

        "name": "folder/cat.png",

    4

        "bucket_id": "avatars",

    5

        "owner": "317eadce-631a-4429-a0bb-f19a7a517b4a",

    6

        "id": "eaa8bdb5-2e00-4767-b5a9-d2502efe2196",

    7

        "updated_at": "2021-04-06T16:30:35.394674+00:00",

    8

        "created_at": "2021-04-06T16:30:35.394674+00:00",

    9

        "last_accessed_at": "2021-04-06T16:30:35.394674+00:00",

    10

        "metadata": {

    11

          "size": 1234

    12

        }

    13

      }

    14

    ]

* * *

## Retrieve object info

head`/object/info/{bucketName}/{*}`

Object Info

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Query parameters

  * height

Optional

integer

  * width

Optional

integer

  * resize

Optional

enum

Accepted values

  * format

Optional

enum

Accepted values

  * quality

Optional

integer


### Response codes

  * 4XX


* * *

## Copies an object

post`/object/copy`

### Body

  * bucketId

Required

string

  * sourceKey

Required

string

  * destinationBucket

Optional

string

  * destinationKey

Required

string

  * metadata

Optional

object

Object schema

  * copyMetadata

Optional

boolean


### Response codes

  * 200
  * 4XX


### Response (200)

exampleschema


    1

    {

    2

      "Id": "lorem",

    3

      "Key": "folder/destination.png",

    4

      "name": "lorem",

    5

      "bucket_id": "lorem",

    6

      "owner": "lorem",

    7

      "owner_id": "lorem",

    8

      "version": "lorem",

    9

      "id": "lorem",

    10

      "updated_at": "lorem",

    11

      "created_at": "lorem",

    12

      "last_accessed_at": "lorem",

    13

      "metadata": {},

    14

      "user_metadata": {},

    15

      "buckets": {

    16

        "id": "bucket2",

    17

        "name": "bucket2",

    18

        "public": false,

    19

        "file_size_limit": 1000000,

    20

        "allowed_mime_types": [

    21

          "image/png",

    22

          "image/jpeg"

    23

        ],

    24

        "owner": "4d56e902-f0a0-4662-8448-a4d9e643c142",

    25

        "created_at": "2021-02-17T04:43:32.770206+00:00",

    26

        "updated_at": "2021-02-17T04:43:32.770206+00:00"

    27

      }

    28

    }

* * *

## Retrieve an object from a public bucket

get`/object/public/{bucketName}/{*}`

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Query parameters

  * download

Optional

string


### Response codes

  * 4XX


* * *

## Get object info

get`/object/info/public/{bucketName}/{*}`

returns object info

### Path parameters

  * bucketName

Required

string

  * *

Required

string


### Query parameters

  * height

Optional

integer

  * width

Optional

integer

  * resize

Optional

enum

Accepted values

  * format

Optional

enum

Accepted values

  * quality

Optional

integer


### Response codes

  * 4XX