# API Documentation

## Overview

The News Feed System exposes RESTful APIs that allow clients to interact with backend services. All APIs follow REST principles and communicate using JSON.

Base URL:

```
https://api.newsfeed.com/v1
```

---

# Authentication APIs

## Register User

POST /register

Request

```json
{
  "username":"john",
  "email":"john@example.com",
  "password":"password123"
}
```

Response

```json
{
  "message":"User Registered Successfully"
}
```

---

## Login

POST /login

Request

```json
{
  "email":"john@example.com",
  "password":"password123"
}
```

Response

```json
{
  "token":"JWT_TOKEN"
}
```

---

# User APIs

## Get Profile

GET /users/{id}

Response

```json
{
  "id":101,
  "username":"john",
  "followers":120,
  "following":85
}
```

---

## Follow User

POST /follow/{id}

Response

```json
{
  "message":"User Followed"
}
```

---

## Unfollow User

DELETE /follow/{id}

Response

```json
{
  "message":"User Unfollowed"
}
```

---

# Post APIs

## Create Post

POST /posts

Request

```json
{
  "content":"Hello World",
  "mediaUrl":"image.jpg"
}
```

Response

```json
{
  "postId":5001,
  "status":"Created"
}
```

---

## Get Post

GET /posts/{id}

---

## Update Post

PUT /posts/{id}

---

## Delete Post

DELETE /posts/{id}

---

# Feed APIs

## Get News Feed

GET /feed

Returns the personalized news feed.

---

## Refresh Feed

GET /feed/refresh

Refreshes the latest posts.

---

# Engagement APIs

## Like Post

POST /like/{id}

---

## Unlike Post

DELETE /like/{id}

---

## Comment

POST /comment/{id}

Request

```json
{
  "comment":"Nice Post!"
}
```

---

## Share

POST /share/{id}

---

# Search APIs

## Search Users

GET /search/users?q=john

---

## Search Posts

GET /search/posts?q=technology

---

## Trending Posts

GET /trending

---

# HTTP Status Codes

| Code | Meaning |
|------|---------|
|200|Success|
|201|Created|
|400|Bad Request|
|401|Unauthorized|
|404|Not Found|
|500|Internal Server Error|
