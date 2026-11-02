# Create your own feed

LVX in Radio can stream from any feed that follows this small layout. A feed is a static website: one `catalog.json` and the MP3 files it points to. There is no server code, no account and no API key.

## 1. Layout

```text
site/
  catalog.json
  rock/first-song.mp3
  rock/second-song.mp3
  jazz/another-song.mp3
```

## 2. catalog.json

```json
{
  "schemaVersion": 1,
  "stations": [
    {
      "id": "my-jazz",
      "name": "My Jazz",
      "genre": "Jazz",
      "description": "Slow evenings.",
      "enabled": true,
      "tracks": [
        {
          "id": "another-song",
          "title": "Another Song",
          "url": "jazz/another-song.mp3",
          "sizeBytes": 5761536,
          "durationSec": 180
        }
      ]
    }
  ]
}
```

| Field | Rule |
|---|---|
| `schemaVersion` | Must be `1`. |
| station `id`, track `id` | Required, unique, letters, digits, `_` and `-`, up to 100 characters. Keep them stable: the app uses them to recognise a song between updates. |
| `name`, track `title` | Required. Shown up to 80 and 160 characters. |
| `genre`, `description` | Optional. Up to 80 and 240 characters. |
| `enabled` | Optional. `false` hides the station without deleting it. |
| `url` | Required. Relative to the catalog, or a full URL on the same host. |
| `sizeBytes`, `durationSec` | Optional but recommended. Each MP3 up to 100 MiB. |

Limits: 128 stations, 10,000 songs, a catalog of up to 4 MiB. The dial holds 26 stations in total, counting the included and personal ones, so extra stations are skipped. The ids `lux-edm`, `lux-rock`, `lux-pop` and `lux-relax` belong to the included stations: avoid them.

If the catalog is invalid, the app keeps the previous one and says so.

## 3. Hosting

Any static host works: GitHub Pages, Cloudflare R2, Netlify, your own web server. It must:

- use **HTTPS**. Plain HTTP is accepted only for an address on your own network, for testing;
- serve the catalog and the MP3 files from the **same host**: redirects to another host are refused;
- answer **byte-range requests** (HTTP 206), which streaming needs;
- send MP3 files as `audio/mpeg` or `audio/mp3`;
- allow plain `GET` requests, with no login, cookies or tokens. A private repository or a page behind a login can't be read.

`ETag` or `Last-Modified` headers are welcome: the app uses them to skip downloading an unchanged catalog.

## 4. Publish on GitHub Pages

1. Create a **public** repository and put your catalog and MP3 files in a folder called `site`. The workflow in `.github/workflows/pages.yml` of this repository is a working example: it publishes `site/` when you run it by hand.
2. In **Settings → Pages**, set *Source* to **GitHub Actions**.
3. In **Actions**, choose **Publish radio files** and press **Run workflow**.

Your catalog is then at `https://<user>.github.io/<repository>/catalog.json`.

## 5. Try it in LVX in Radio

Open **Settings**, turn on **LVX Live**, paste your catalog address under **Station catalog** and press **Save settings**. Use **Refresh stations** after you change the feed. The app also checks by itself every 30 minutes by default.

To test on your own network before publishing, serve the folder from a static server that supports byte ranges and use its address on your local network.

## Rights

Only publish music you may distribute.
