# Storax: features and competitive position

Written 2026-11-18 for the Storax website team (copy, product marketing).

**Sources.** The app repository at `/home/sumit/StudioProjects/storax` (version `1.0.3+50`, HEAD `a755d75`), mainly its code under `lib/`, `rust/` and `android/`, its READMEs, and `docs/audit-2026-11-17.md`, the most recent internal audit (commit `d435b85`). Nothing was run on a device for this document. Platform labels come from reading code and from that audit.

**Competitor caveat.** The competitor sections use general public knowledge of popular Android file managers, not a fresh check of their current versions. Each cell says what is "common", "some" or "rare" in the category rather than naming a product for every claim. Re-verify against the live apps before publishing any named comparison.

## 1. What Storax is

An Android-first, Flutter file manager that also ships for Windows, macOS and Linux. It is free, with no ads and no subscriptions (per the website). Most of the unusual features are Android-only. Section 5 lists the platform limits, and the website must keep them visible.

## 2. Feature inventory

Platform key: **A** = Android, **D** = desktop (Windows, macOS, Linux), **All** = anywhere the Dart code runs.

### Files and storage

| Feature | What it does | Platform |
|---|---|---|
| File browser and operations | Browse roots and removable volumes. Copy, move, rename, delete, create. Conflict choices (replace, rename, merge, skip). Sort, filter, properties. Live refresh when files change. | All (native on A and macOS; `dart:io` elsewhere) |
| Media library | Images, video, audio, GIFs, documents, archives, downloads and recents, each with its own screen. Video previews. | All (Gallery shortcut is mobile only) |
| Trash, undo, redo, history | Delete goes to Trash first. Restore. Operation history. Undo and redo of supported actions. | Full on A. Partial or absent elsewhere. |
| Archives | Create and extract ZIP, TAR, TAR.GZ, TAR.BZ2. | All |
| Opening from other apps | Each file opened from another app gets its own viewer window, so two documents can sit side by side in split-screen. | A |

### Privacy and security

| Feature | What it does | Platform |
|---|---|---|
| Private vaults | Hide files and folders into an encrypted vault (Argon2id, AES-256-GCM, per-record RSA key wrapping). Multiple vaults, decoy vault, PIN change. | All that run the Dart code (iOS is a placeholder, see section 5) |
| File encryption | Encrypt single files with a password (chunked AES-256-GCM). | All |
| App Lock | Put chosen apps behind a PIN or fingerprint. Uses an AccessibilityService that reads only the foreground app's package and screen class. Opt-in. | A |
| Hide the app itself | Hide the launcher icon (Android), or the desktop entry (unconfined Linux). | A, Linux |
| App safety | Lists apps, flags known pre-installed bloat and known risky packages, shows dangerous permissions. A guide, not an antivirus. | A |

### Search and intelligence (on device)

| Feature | What it does | Platform |
|---|---|---|
| Search | By name, type, size, date, dimensions, duration. Content index for text, code and PDFs. | All |
| Text in photos (OCR) | Search and copy text inside images and scanned PDFs (ML Kit). | A |
| Describe-to-find | Semantic search with local models (MiniLM text embeddings, MobileCLIP image embeddings, HNSW index). Models download only when the user turns the feature on. | A (model runtime not exercised in the audit) |
| Duplicates and lookalikes | Duplicate files and folders, plus visually similar photos (perceptual hashing, embeddings, HDBSCAN clustering). Background indexing that backs off under battery, heat and memory pressure. | All |
| Document assistant | Summary, key points, dates and action items from a document. Extractive: every result is a quoted passage, nothing is generated. | All |
| Recording transcription | Speech-to-text on audio (Whisper small, ONNX, downloaded on request). | A |
| AI assistant | Local Llama model for text tools. | A |

### Sharing and connecting

| Feature | What it does | Platform |
|---|---|---|
| Nearby Share | Send and receive files and text on the same network. Discovery by mDNS or by scanning a code. History of transfers. Backed by STP/1, a resumable protocol written in Rust (QUIC with TLS 1.3 and a TCP fallback, signed device identities). | All |
| Live streaming | Stream the camera or the screen to a nearby device. | A and desktop (path not traced on every OS) |
| Wi-Fi Direct | Two phones connect directly with no router. | A |
| Offline Drop | Share files from a temporary local server with application-layer encryption. | All |
| QR | Scan and create QR codes and barcodes (styled QR, history, product lookup through Open Food Facts, Wi-Fi join). | All |
| Remote files | SSH terminal and SFTP, FTP/FTPS, NFS. Host-key trust on first use with a warning if the key changes. Password or private-key sign-in. | A |

### Editors and viewers

| Feature | What it does | Platform |
|---|---|---|
| PDF | Viewer, text extraction, highlight and annotate, fill form fields (with optional flatten), reorder and delete pages, merge and split, sign with a PKCS#12 certificate. | All |
| Office documents | Word, Excel, PowerPoint (including DOC, XLS, PPT, ODF) in a bundled offline Collabora engine. | A, D (iOS optional) |
| Notes | Rich-text notes (Quill) and plain text. | All |
| Code editor | Syntax highlighting for 22 languages, formatter, find and replace, tabs, word wrap, line numbers, keyboard shortcuts. | All |
| Terminal | Local shell on computers. SSH terminal on Android. | A, D |
| Image editor, video editor, canvas, collage maker | Filters and edits, trim and export, freehand canvas, templates for collages. GIF creation from video. | All |
| Media player | Audio and video through media_kit, with info dialogs. | All |
| WhatsApp status saver | Keep copies of statuses. Desktop discovery is best effort. | A (desktop partial) |

## 3. What most file managers do not do

These are the combinations that are rare. None of the others needs to be absent entirely for the point to hold: it is the bundle that is unusual.

1. **Vault plus decoy plus app lock in the file manager.** Several managers have one PIN-protected folder. A decoy vault, per-item key wrapping and an app locker together are rare.
2. **Local, describe-to-find search and lookalike-photo detection.** Name search is universal. Searching by what a photo shows, or grouping near-identical shots, without uploading anything, is rare. Cleaner tools that do similar jobs generally process in the cloud or are separate apps.
3. **An open, resumable transfer protocol with signed device identities.** Phone-to-phone sharing is common. A documented protocol (STP/1) with QUIC, resume and identity binding, plus live camera and screen streaming and QR, is rare.
4. **A developer and admin toolkit on the same screen.** SSH terminal, SFTP, a 22-language code editor and a local terminal are normally in separate apps or paywalled add-ons.
5. **Bundled office editing, offline.** Editing DOCX, XLSX and PPTX in a bundled engine, not only viewing, is uncommon in free file managers. The cost is a large app (the engine is about 868 MiB extracted).
6. **Free, ad-free, one app.** Many alternatives rely on ads, a paid tier for network or encryption features, or both.
7. **Same toolset on computers.** Android file managers rarely ship Windows, macOS and Linux builds. Storax does, with a reduced feature set.

## 4. Where competitors are ahead (do not claim these)

These are things Storax does not do, based on code. Competitors commonly offer at least some of them.

| Gap | Evidence in the code | Competitors |
|---|---|---|
| Cloud storage (Google Drive, Dropbox, OneDrive and similar) | No cloud client in `lib/` or `pubspec.yaml` | Common |
| SMB/CIFS and WebDAV | Not in the code. Only SFTP, FTP/FTPS and NFS | Common in the established network-capable managers |
| RAR and 7z | Not implemented to create or extract (audit R-table, section 6) | Common |
| Root browsing | No root support found in the code (not exhaustively checked) | Some |
| Safe folder with cloud-backed recovery | Vault items cannot be recovered if the PIN or device is lost | Some |
| iOS parity | iOS vault is a plaintext placeholder, undo is a no-op, no remote files | Not applicable to most Android-only rivals |
| Mature, tested remote and cleanup paths | Several P1 defects still open (bulk paste has no per-item result, photo cleanup ignores failures, archive silently skips unreadable files) | Established managers have had longer to harden these |

## 5. Claims to avoid on the website

Taken from the 2026-11-17 audit. Each is a rule for copy.

- **Platform:** OCR, App Lock, Wi-Fi Direct, app inventory, SSH, FTP and NFS are Android-only. Say so.
- **Vault:** PIN lockout resets when the app restarts. When the Android Keystore fails, the device secret falls back to a file in app storage. Do not say "hardware-backed", "unhackable" or "military grade". Lost PIN or device means lost items.
- **iOS:** do not advertise the iOS vault.
- **Analytics:** Android, iOS and macOS builds send analytics and crash data through Firebase, with no in-app opt-out yet. The Security section already says this. Keep it consistent.
- **FTP:** plain FTP is not encrypted by default. Tell people to prefer SFTP.
- **Antivirus:** the app-safety screen flags known risky apps and permissions. It is not an antivirus.
- **Archives:** ZIP and TAR only. Never mention RAR or 7z.
- **PDF:** "Save markup to PDF" is offered but currently fails, and PDF comparison is a byte comparison. Avoid promising markup export or visual diff.
- **AI:** models download only when the user opts in. Say "on your device", never "never leaves your device" unless the user has not enabled any download. The document assistant quotes the document, it does not write new text.

## 6. Website change

The **Built-in tools** section of `index.html` (`#tools`) had nine cards: image and video editors, PDF and Office, notes, zip, app safety, App Lock, WhatsApp status saver, QR scanner, audio and video player.

Changes made on 2026-11-18:

- **Added** three cards for tools that were missing: *Code editor and terminal*, *Remote files* (SFTP, FTP, NFS) and *Document assistant and text recognition*. Each has the same kind of small interactive demo as the others, and each states its platform limit.
- **Reworded** the PDF and Office card to name what the PDF tools really do (fill forms, sign, reorder pages), and the Zip card to name the formats (ZIP and TAR).
- **QR card, India only:** a note that Storax extracts the UPI ID, payee, amount and note from payment QR codes (code: `_parseUpi` in `lib/ui/shared/qr/qr_page.dart`). It shows only when the browser's time zone is Asia/Kolkata or a language is `-IN`. No network lookup is used.
- **Audio and video card:** now says video has a screenshot button and an info dialog (title, author, resolution, frame rate, date, location), and audio info shows artist, author, album, composer, genre, year and bitrate (`lib/ui/shared/media/media_player.dart`).
- Grid stays three columns, now four rows.

Candidates for later: Image-and-video editors could be split from the collage and GIF tools, and a Transcribe card could be added once the Whisper model path is verified on a device.
