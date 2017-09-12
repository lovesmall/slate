---
title: Avetics Tango API Reference

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - drones
  - errors

search: true
---

# Introduction

This is API documentation is for [Avetics Tango](http:tango.avetics.com), which is a web system for managing drone and data. This API follows [JSON API](http://jsonapi.org/) format.

It also includes a iOS app (coming soon) to capture and sync the photo with its position/orientation info to Tango.

# Authentication

## Sign Up

User can create a account. After this, user need to sign in to obtain token info.

### HTTP Request

`POST http://tango.avetics.com/api/v1/auth`

> JSON response:

```json
{
  "data": {
    "id": "3",
    "type": "users",
    "attributes": {
      "email": "user@avetics.com",
      "name": "Narco",
      "avatar-url": "url-link-of-a-user",
      "created-at": "2016-11-16T07:42:54Z"
    }
  }
}
```


### Post Form Parameters

| Parameter    | Description                              |
| ------------ | ---------------------------------------- |
| email        | User email address. |
| name         | User name. |
| password     | User email address. |
| password_confirmation     | User password confirmation.|


<aside class="notice">
Info — User need to confirm the email before can sign in
</aside>


## Sign In

Tango uses token to authenticate user. Post user 'email' and 'password' to sign in. You will receive token info contains `access-token`, `client` and `uid` in the response. 

**Attach these token info in every subsequent request header for authentication.**

### HTTP Request

`POST http://tango.avetics.com/api/v1/auth/sign_in`

> The response header include token info.

```
access-token: oSPkpQnk3b5nF7EU88USng
client: qZE0ZBcIe2O49PNDpDgFrQ
uid: user1@avetics.com
```

> JSON response:


```json
{
  "data": {
    "id": "3",
    "type": "users",
    "attributes": {
      "email": "user@avetics.com",
      "name": "Narco",
      "avatar-url": "url-link-of-a-user",
      "created-at": "2016-11-16T07:42:54Z"
    }
  }
}
```


### Post Form Parameters

| Parameter    | Description                              |
| ------------ | ---------------------------------------- |
| email        | User email address. |
| password     | User password.|



<aside class="notice">
Info — the token info are in the response header, not body.
</aside>


# User

## Get a User

### HTTP Request

`GET http://tango.avetics.com/api/v1/users/<ID>`

> JSON response:

```json
{
  "data": {
    "id": "3",
    "type": "users",
    "attributes": {
      "email": "user@avetics.com",
      "name": "Narco",
      "avatar-url": "url-link-of-a-user",
      "created-at": "2016-11-16T07:42:54Z"
    }
  }
}
```

### URL Parameters

| Parameter | Description                      |
| --------- | -------------------------------- |
| ID        | The ID of the user to retrieve |

<aside class="success">
Remember — pass in the token info in the request header.
</aside>


# Project

## List All Project

### HTTP Request

`GET http://tango.avetics.com/api/v1/projects`

> JSON response:

```json
{
  "data": [
    {
      "id": "50",
      "type": "projects",
      "attributes": {
        "name": "api-project-1",
        "due-date": "2016-12-16T00:00:00.000Z",
        "status": "planning",
        "created-at": "2016-12-05T06:53:43Z",
        "updated-at": "2016-12-13T07:03:01Z"
      }
    },
    {
      "id": "51",
      "type": "projects",
      "attributes": {
        "name": "api-project-2",
        "due-date": null,
        "status": "planning",
        "created-at": "2016-09-28T08:53:29Z",
        "updated-at": "2016-09-28T08:53:50Z"
      }
    }
  ]
}
```

## Create a Project

### HTTP Request

`POST http://tango.avetics.com/api/v1/projects`

> JSON response:

```json
{
  "data": {
    "id": "3",
    "type": "users",
    "attributes": {
      "email": "user@avetics.com",
      "name": "Narco",
      "avatar-url": "url-link-of-a-user",
      "created-at": "2016-11-16T07:42:54Z"
    }
  }
}
```

### Post Form Parameters

| Parameter         | Description                              |
| ----------------- | ---------------------------------------- |
| project[name]     | Project name |
| project[status]   | `planning`, `ongoing` or `completed`|
| project[due-date] | optional, format in `YYYY-MM-DD`|



## Get a Project

### HTTP Request

`GET http://tango.avetics.com/api/v1/projects/<ID>`

> JSON response:

```json
{
  "data": {
    "id": "50",
    "type": "projects",
    "attributes": {
      "name": "api-test-1",
      "due-date": "2016-12-16T00:00:00.000Z",
      "status": "planning",
      "created-at": "2016-12-05T06:53:43Z",
      "updated-at": "2016-12-13T07:03:01Z"
    }
  }
}
```

### URL Parameters

| Parameter | Description                      |
| --------- | -------------------------------- |
| ID        | The ID of the project to retrieve |

## Create a Photo

Create a photo for a project requires 3 steps.

1. Get a AWS S3 Presigned Post from tango.
2. Upload photo to AWS S3 using this presigned post. A S3 ID will be returned.
3. Upload the attributes of this photo, including the S3 ID, to tango.

Tango will then link the photo with the file uploaded in S3 and process it accordingly. 

### STEP 1

Get a AWS S3 Presigned Post from tango.

#### HTTP Request

`GET http://tango.avetics.com/api/v1/attachments/cache/presign`

> STEP 1 JSON response:

```json
{
  "url": "https://avetics-tango.s3-ap-southeast-1.amazonaws.com",
  "fields": {
    "key": "cache/the-s3-id-of-this-file",
    "acl": "public-read",
    "policy": "eyJleHBpcmF0aW9uIjoiMjAxNi0xMi0xM1QxMTo0MjoxOFoiLCJjb25kaXRpb25zIjpbeyJidWNrZXQiOiJhdmV0aWNzLXRhbmdvIn0seyJrZXkiOiJaYWNoZS8wNWUxZWQzOWY3MzU4YzJjMGYyYzYyYzAwMWFlNTdkMiJ9LHsiYWNsIjoicHVibGljLXJlYWQifSxbImNvbnRlbnQtbGVuZ3RoLXJhbmdlIiwwLDUyNDI4ODAwXSx7IngtYW16LWNyZWRlbnRpYWwiOiJBS0lBSkZTS1VZSFU0V0taWlAzUS8yMDE2MTIxMy9hcC1zb3V0aGVhc3QtMS9zMy9hd3M0X3JlcXVlc3QifSx7IngtYW16LWFsZ29yaXRobSI6IkFXUzQtSE1BQy1TSEEyNTYifSx7IngtYW16LWRhdGUiOiIyMDE2MTIxM1QxMDQyMThaIn1dfQ==",
    "x-amz-credential": "AKIAJFSKUYHU4WKEZP3Q/20161213/ap-southeast-1/s3/aws4_request",
    "x-amz-algorithm": "AWS4-HMAC-SHA256",
    "x-amz-date": "20161213T104218Z",
    "x-amz-signature": "da21701e447a1835062e9bf202d54659a908ac23cd8c0e5266e7b936c4dabb17"
  }
}
```


### STEP 2

Upload photo to AWS S3 using this presigned post. A S3 ID will be returned.

#### HTTP Request

Parse the returned presigned post (data) and upload photo to:

`POST https://avetics-tango.s3-ap-southeast-1.amazonaws.com`

> STEP 2 data format for photo upload request:

```js
{
  id: data.formData.key.match(/cache\/(.+)/)[1], // we have to remove the prefix part
  storage: 'cache',
  metadata: {
  size:      data.files[0].size,
  filename:  data.files[0].name.match(/[^\/\\]*$/)[0]
  mime_type: data.files[0].type
}
```


#### Post Form Parameters

The form post to S3 using the fields get from STEP 1, plus the file data. Please refer the format of the STEP 2 post.

### STEP 3

Upload the attributes of this photo, including the S3 ID, to tango.

#### HTTP Request

`POST http://tango.avetics.com/api/v1/projects/<ID>/photos`

> STEP3 JSON data format for photo request:

```json
{
  "data": {
    "photo": {
      "file": {
        "id": "id-of-s3-file",
        "storage": "cache",
        "metadata": {
          "size":      "file-size",
          "filename":  "file-name",
          "mime_type": "file-type"
        }
      }
    }
  }
}
```

### Post Form Parameters

refer the JSON data format for photo.



