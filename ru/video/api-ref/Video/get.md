---
editable: false
sourcePath: en/_api-ref/video/v1/api-ref/Video/get.md
---

# Video API, REST: Video.Get {#Get}

Returns the specific video.

## HTTP request

```
GET https://video.{{ api-host }}/video/v1/videos/{videoId}
```

## Path parameters

#|
||Field | Description ||
|| videoId | **string**

Required field. ID of the video. ||
|#

## Response {#yandex.cloud.video.v1.Video}

**HTTP Code: 200 - OK**

```json
{
  "id": "string",
  "channelId": "string",
  "title": "string",
  "description": "string",
  "thumbnailId": "string",
  "status": "string",
  "duration": "string",
  "visibilityStatus": "string",
  // Includes only one of the fields `tusd`
  "tusd": {
    "url": "string"
  },
  // end of the list of possible fields
  // Includes only one of the fields `publicAccess`, `authSystemAccess`
  "publicAccess": "object",
  "authSystemAccess": "object",
  // end of the list of possible fields
  "createdAt": "string",
  "updatedAt": "string",
  "labels": "string"
}
```

#|
||Field | Description ||
|| id | **string**

ID of the video. ||
|| channelId | **string**

ID of the channel where the video was created. ||
|| title | **string**

Video title. ||
|| description | **string**

Video description. ||
|| thumbnailId | **string**

ID of the thumbnail. ||
|| status | **enum** (VideoStatus)

Video status.

- `VIDEO_STATUS_UNSPECIFIED`: Video status unspecified.
- `WAIT_UPLOADING`: Waiting for the whole number of bytes to be loaded.
- `PROCESSING`: Video processing.
- `READY`: Video is ready, processing is completed.
- `ERROR`: An error occurred during video processing. ||
|| duration | **string** (duration)

Video duration. Optional, may be empty until the transcoding result is ready. ||
|| visibilityStatus | **enum** (VisibilityStatus)

Video visibility status.

- `VISIBILITY_STATUS_UNSPECIFIED`: Visibility status unspecified.
- `PUBLISHED`: Video is published and available for viewing.
- `UNPUBLISHED`: Video is unpublished, only admin can watch. ||
|| tusd | **[VideoTUSDSource](#yandex.cloud.video.v1.VideoTUSDSource)**

Upload video using the tus protocol.

Includes only one of the fields `tusd`.

Source type. ||
|| publicAccess | **object**

Video is available to everyone.

Includes only one of the fields `publicAccess`, `authSystemAccess`.

Video access rights. ||
|| authSystemAccess | **object**

Checking access rights using the authorization system.

Includes only one of the fields `publicAccess`, `authSystemAccess`.

Video access rights. ||
|| createdAt | **string** (date-time)

Time when video was created.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| updatedAt | **string** (date-time)

Time of last video update.

String in [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) text format. The range of possible values is from
`0001-01-01T00:00:00Z` to `9999-12-31T23:59:59.999999999Z`, i.e. from 0 to 9 digits for fractions of a second.

To work with values in this field, use the APIs described in the
[Protocol Buffers reference](https://developers.google.com/protocol-buffers/docs/reference/overview).
In some languages, built-in datetime utilities do not support nanosecond precision (9 digits). ||
|| labels | **string**

Custom labels as `` key:value `` pairs. Maximum 64 per resource. ||
|#

## VideoTUSDSource {#yandex.cloud.video.v1.VideoTUSDSource}

#|
||Field | Description ||
|| url | **string**

URL for uploading video via the tus protocol. ||
|#