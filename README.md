# Authenticity Checker

A browser-only web app that checks documents and images for signs of editing or AI generation, and lets an organization issue and verify signed document credentials.

Files are processed in the browser and never uploaded.

## Features

- **Check a file**: PDF, DOCX, XLSX, PPTX, JPG, PNG, WEBP
  - PDF: digital signatures (and content added after signing), producer/creator history, incremental saves, duplicate font subsets, white-box and text-box overlays, JavaScript
  - Office: author vs last editor, dates, revisions, tracked changes (shows removed and added text), hidden text, macros, hidden sheets
  - Images: EXIF camera and software data, C2PA presence, AI-generator markers (IPTC digital source type, generator names, Stable Diffusion/ComfyUI prompt fields), error-level analysis map
- **Issue credential**: signs a document's SHA-256 hash with an ECDSA P-256 issuer key (Web Crypto) and produces a JSON proof
- **Verify credential**: checks the proof's signature, that the document hash matches, and whether the issuer key is trusted

## Verdicts

| Verdict | Meaning |
| --- | --- |
| Verified | Signature valid, document hash matches, issuer trusted |
| No red flags | Nothing suspicious found (not proof) |
| Needs review | One or more warnings, such as edits after creation |
| Likely manipulated | A risk finding: AI marker, broken signature or hash mismatch |

## Run it

It is a single static file. Open `index.html` in a browser, or host the folder on any static host (GitHub Pages, Netlify, Vercel).

Signing needs a secure context, so use `https://` or `http://localhost`, not `file://`. Locally:

```
python3 -m http.server 8000
```

then open http://localhost:8000.

### GitHub Pages

1. Create a repository and upload `index.html`, `.nojekyll` and this README.
2. Settings > Pages > Build and deployment > Deploy from a branch > `main` / root.
3. The app is live at `https://<user>.github.io/<repo>/` within a minute or two.

## Limits

- Forensic checks raise or lower suspicion; only a valid signature or issuer record proves a file is original.
- PDF and Office signature certificates and C2PA signatures are detected but not cryptographically validated yet.
- The issuer key is stored in the browser's localStorage. For production, keep issuer keys in a key vault and sign on a server.

## Libraries

- [JSZip 3.10.1](https://stuk.github.io/jszip/) (Office files)
- [exifr 7.1.3](https://github.com/MikeKovarik/exifr) (image metadata)
