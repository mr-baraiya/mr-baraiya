# How to get YouTube API (YouTube Data API v3)

This document explains, step-by-step, how to obtain credentials and use the YouTube Data API (v3). It covers creating a Google Cloud project, enabling the API, creating API keys and OAuth clients, basic usage examples, common endpoints, quota notes, and troubleshooting.

---

## 1. Overview

The YouTube Data API lets you access YouTube resources (channels, playlists, videos, comments, etc.) programmatically. For most server-side or public-data tasks you can use an **API key**. For user-specific operations (accessing private playlists, uploading videos, or actions on behalf of users) you must use **OAuth 2.0**.

---

## 2. Create a Google Cloud Project

1. Go to the **Google Cloud Console**.
2. Click **Select a project** → **New Project**.
3. Give the project a name (e.g., `My-YouTube-Project`) and click **Create**.

> Tip: Keep the project selected for the next steps.

---

## 3. Enable the YouTube Data API (v3)

1. In Cloud Console, open **APIs & Services** → **Library**.
2. Search for **YouTube Data API v3**.
3. Click the API and choose **Enable**.

---

## 4. Create Credentials

You can create two main types of credentials:

### A. API Key (for public, unauthenticated requests)

1. Go to **APIs & Services** → **Credentials**.
2. Click **Create credentials** → **API key**.
3. Copy the generated API key.

**Recommended:** Restrict the API key to your website's domain(s) or server IPs and to the YouTube Data API to prevent abuse.

### B. OAuth 2.0 Client ID (for user authorization)

1. In **Credentials** click **Create credentials** → **OAuth client ID**.
2. If prompted, configure the **OAuth consent screen** (App name, support email). You can choose `Internal` for GSuite org or `External` for public apps.
3. After consent is configured, choose application type:

   * **Web application** — for web servers.
   * **Desktop app** — for local tools.
   * **Android / iOS** — mobile apps.
4. For Web application, set **Authorized redirect URIs** (e.g., `https://yourdomain.com/oauth2callback`).
5. Create and copy the **Client ID** and **Client Secret**.

**Scopes for YouTube** (common):

* `https://www.googleapis.com/auth/youtube.readonly` (read-only)
* `https://www.googleapis.com/auth/youtube` (manage YouTube account)
* `https://www.googleapis.com/auth/youtube.upload` (upload videos)

Use the least privilege scope you need.

---

## 5. Basic Usage Examples

### Using an API Key (public data)

**Get playlist items (curl)**

```bash
curl "https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&playlistId=PLAYLIST_ID&maxResults=50&key=YOUR_API_KEY"
```

**Get channel details**

```bash
curl "https://www.googleapis.com/youtube/v3/channels?part=snippet,statistics&id=CHANNEL_ID&key=YOUR_API_KEY"
```

### Using OAuth 2.0 (access private data / act for user)

1. Direct user to consent URL (replace `CLIENT_ID`, `REDIRECT_URI`, and `SCOPE`):

```
https://accounts.google.com/o/oauth2/v2/auth?client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&access_type=offline
```

2. After user consents, Google redirects to `REDIRECT_URI` with `code` parameter.
3. Exchange `code` for tokens (POST to token endpoint) using your client secret.
4. Use the returned access token as `Authorization: Bearer ACCESS_TOKEN` in API requests.

**Example: get my playlists (authorized request)**

```bash
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "https://www.googleapis.com/youtube/v3/playlists?part=snippet,contentDetails&mine=true"
```

---

## 6. Common Endpoints

* `playlistItems` — list videos in a playlist
* `playlists` — list playlists for a channel/user
* `search` — search for videos/channels/playlists
* `videos` — get video details, statistics
* `channels` — get channel metadata and statistics
* `commentThreads` / `comments` — read and manage comments

Check `part` parameter and request only the fields you need (e.g., `part=snippet,statistics`).

---

## 7. Quotas & Limits

* Every request consumes quota units. Different methods and parts cost different units.
* Typical read requests cost 1 unit, but some `part` combinations or write methods cost more.
* Check your project’s quota limits in **APIs & Services → Dashboard → YouTube Data API** and request higher quota if needed.

---

## 8. Best Practices

* **Restrict API keys** to domains or IP addresses.
* **Use OAuth** when accessing user-specific or private data.
* **Cache responses** where possible to reduce quota usage.
* **Request minimal `part` fields** to lower costs and bandwidth.
* **Rotate credentials** and keep client secrets secure.

---

## 9. Quick Code Snippets

### Node.js (using `googleapis` package)

```js
const {google} = require('googleapis');
const youtube = google.youtube({version: 'v3', auth: YOUR_API_KEY});

async function listPlaylistItems(playlistId) {
  const res = await youtube.playlistItems.list({
    part: ['snippet'],
    playlistId,
    maxResults: 50,
  });
  return res.data.items;
}
```

### Python (requests, API key)

```py
import requests

url = "https://www.googleapis.com/youtube/v3/playlistItems"
params = {
    'part': 'snippet',
    'playlistId': 'PLAYLIST_ID',
    'maxResults': 50,
    'key': 'YOUR_API_KEY'
}
res = requests.get(url, params=params)
print(res.json())
```

---

## 10. Troubleshooting

* **401 Unauthorized / Invalid Credentials**: Check API key or OAuth token validity and scopes.
* **403 Quota Exceeded**: Check quotas in Cloud Console or reduce request rate/fields.
* **403 Access Not Configured**: Ensure YouTube Data API v3 is enabled for your project.
* **Redirect URI mismatch**: Ensure the redirect URI in OAuth client matches exactly the one used in the request.

---

## 11. Additional Notes

* For production apps that need user data, use **OAuth** with refresh tokens and store them securely.
* If you plan to publish an app for many users, you may need to go through **OAuth verification** and provide a privacy policy and app details.
* For server-to-server usage, consider using service accounts for other Google APIs, but YouTube Data API often requires OAuth for user data and does not support service accounts for user accounts.

---

## 12. Summary Checklist

* [ ] Create Google Cloud project
* [ ] Enable YouTube Data API v3
* [ ] Create API key and/or OAuth client ID
* [ ] Restrict and secure credentials
* [ ] Use appropriate scopes and minimal `part`
* [ ] Monitor quota and errors
