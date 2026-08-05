---
name: image-magic-bytes-ftyp-is-heic
description: A downloaded "image" whose magic bytes are `...ftyp` is a valid HEIC/AVIF/ISOBMFF container, not a failed/login-page download — convert it, don't re-download
type: feedback
date_written: 2026-06-23
last_validated: 2026-06-23
---

**Rule.** When a `.png`/`.jpg` you downloaded fails to open with the harness error *"File has an image extension but its content is not a valid PNG/JPEG/GIF/WebP. Detected: unrecognized bytes (hex: 00 00 00 NN 66 74 79 70)"*, the bytes `66 74 79 70` = ASCII `ftyp` mean it is a **valid ISO Base Media container** (HEIC / HEIF / AVIF / MP4 / MOV), just not the format its extension claims. Do NOT treat it as a failed download or a saved login/error page — that's the misleading default the error message suggests.

**Why:** 2026-06-23 phase0-vton-test session, downloading product images from a fashion CDN (Sezane). Modern e-commerce CDNs serve AVIF/HEIC by content negotiation, so a `.png` URL can return an ISOBMFF container. The Read tool can't decode HEIC/AVIF, so it reports "unrecognized bytes" and guesses "login page" — wrong here. The fix was a format conversion, after which the file opened fine.

**How to apply:** On the `...ftyp` error: (1) confirm with `file <path>` or check the brand string after `ftyp` (`heic`, `avif`, `mp42`, `qt  `). (2) Convert in place rather than re-downloading — macOS: `sips -s format png in.png --out out.png` (sips reads HEIC/AVIF directly); cross-platform fallback `ffmpeg -i in out.png` or ImageMagick `magick`. (3) Only suspect a real login/error page when the magic bytes are HTML (`3c 21 44 4f`/`3c 68 74 6d`) or text, not `ftyp`. Prefer requesting a real raster format up front (`Accept: image/png` header, or a `?format=png`/`fm=png` CDN query param) to avoid the round-trip.

Related: [[feedback_reference_image_artifacts]] — sibling image-pipeline lesson (verify image bytes/properties before trusting them, don't assume).
