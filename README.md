# ofctools · Showcase

A one-page, mobile-first showcase of the **ofctools Android app** (React Native) for my resume. Visitors can download the APK, open the website, or try the compressor right in the page.

**Live link:** _add after deploying_ · **Repo:** <https://github.com/Abhishek25062001/ofctoolsshowcase>

---

## What's on the page

| Section | What it shows |
|---|---|
| Top bar | Logo, **Light / System / Dark** switch (remembered per visitor), "Get app" |
| Hero | Title, one line, **Download APK** + **Open on web**, a phone showing the app |
| Stats | 59 tools · 25 formats · 0 uploads · 100% offline |
| Everything in one app | The 7 tool groups as app icons with job counts, plus popular jobs |
| Private by design | Pick a file → done on your phone → save or share |
| Inside the app | Screenshots in phone frames (hidden until you add some) |
| Try it right here | A working compressor inside a phone frame. Nothing is uploaded |
| Built with React Native | App → native bridge → in-app server + WebView → on-device engine, plus the stack |
| CTA banner | Download APK, Open on web, 3 install steps, a QR code on desktop, GitHub link |
| Mobile dock | A small "Download" bar that slides up on phones once the hero buttons scroll away |

One self-contained `index.html`, no build step. Outside resources: Google Fonts, and the QR library from cdnjs on desktop only.

---

## Folder structure

```
showcase/
├── index.html
├── README.md
├── downloads/
│   └── ofctools.apk          # you add this
└── assets/
    ├── logo.svg
    ├── apple-touch-icon.png
    ├── videos/android.mp4    # optional: portrait screen recording for the hero phone
    └── screens/*.png         # optional: screenshots for "Inside the app"
```

---

## Editing: everything is in `CONFIG`

Find `const CONFIG = {` near the bottom of `index.html`.

| Field | What it does |
|---|---|
| `name`, `role`, `portfolio`, `contact` | Author row in the hero and footer |
| `apk.url` | The APK. Default `downloads/ofctools.apk` |
| `apk.version`, `apk.minAndroid` | Shown under the button: "v1.0 · 85.7 MB · Android 7+" |
| `apk.size` | Only needed when `apk.url` is on another site (the size can't be read from there) |
| `website` | The ofctools website. Empty = the "Open on web" buttons stay hidden |
| `repo` | GitHub link in the banner |
| `hero.video` / `hero.image` | What the hero phone shows. With neither, it shows an illustration of the app's home screen |
| `screens` | `[{ src, label }]` for the screenshot strip |

How the buttons behave:

- **APK on this site:** the page checks the file is really there and reads its size. If it's missing once deployed, the APK buttons hide themselves.
- **iPhone visitors** can't install an APK, so if `website` is set, "Open on web" becomes the main button for them.
- **No APK and no website:** a "Try it now" button points to the in-page demo instead.
- **On localhost**, a "Preview only" box lists what's still missing, and a missing APK shows as a dashed button.

---

## The APK

A release build already exists at `app/ofctools/android/app/build/outputs/apk/release/app-release.apk` (89.9 MB, built 6 Oct). Make sure it's the build you want to share, then copy it in:

```bash
cp app/ofctools/android/app/build/outputs/apk/release/app-release.apk showcase/downloads/ofctools.apk
```

**Size matters:** GitHub rejects files over 100 MB and warns over 50 MB, and every new version adds another ~90 MB to the repo's history. The cleaner option is a **GitHub Release**:

1. On the repo, create a release and attach the file named `ofctools.apk`.
2. Set `apk.url` to `https://github.com/Abhishek25062001/ofctoolsshowcase/releases/latest/download/ofctools.apk` (always points at the newest release).
3. Set `apk.size` to `"85.7 MB"` (the page can't read the size from GitHub).

Visitors installing an APK have to allow "Install unknown apps" for their browser once; the banner's three steps show that.

---

## Hero recording and screenshots

Portrait recording of the app for the hero phone, shrunk for the web:

```bash
ffmpeg -i recording.mp4 -vf "scale=-2:1280" -c:v libx264 -crf 26 -preset slow -movflags +faststart -an assets/videos/android.mp4
```

Screenshots: put PNGs in `assets/screens/` and list them in `CONFIG.screens`. Use a phone screenshot (about 9:19.5) with no personal files visible.

---

## Live demo

The "Try it right here" phone compresses a photo in the visitor's browser at ofctools' Balanced setting (quality 78), in a Web Worker, with a before/after slider. It starts with a sample photo drawn in the page, and visitors can pick their own and save the result. JPG stays JPG, PNG becomes JPG (or WebP if transparent), the original is kept if the result wouldn't be smaller, 60 MB limit.

---

## Preview locally

```bash
python3 -m http.server 4322 --directory showcase
```

Run from the ofctools repo root and open <http://localhost:4322> (also the `showcase` entry in `.claude/launch.json`). The desktop QR code points at the page's own address, so it only becomes useful once deployed.

---

## Deploy

This folder currently sits inside the ofctools repo. To give it its own repo (like the MandirLive showcase), either move the folder out, or add `showcase/` to the ofctools `.gitignore` before running `git init` inside it, so the two repos don't nest. Then:

```bash
git init
git add .
git commit -m "ofctools showcase"
git branch -M main
git remote add origin https://github.com/Abhishek25062001/ofctoolsshowcase.git
git push -u origin main
```

Host it on **Vercel** (`npx vercel --prod` inside the folder, or import the repo), as the MandirLive showcase is. If the host won't take a ~90 MB file, use the GitHub Release link above.

After deploying, open it on an Android phone: download and install the APK, try the demo with a gallery photo, and switch the theme.

---

## Checklist

- [ ] APK added (in `downloads/` or as a GitHub Release) and installs on a phone
- [ ] `CONFIG.website` set
- [ ] Hero recording or screenshot added
- [ ] A few screenshots in `CONFIG.screens`
- [ ] Deployed, tested on a phone, link added to the resume
