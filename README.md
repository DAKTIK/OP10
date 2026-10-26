# I7HVN – the objective truth (OP010) · Website

An interactive digital booklet for the release on Mirror Zone: ten tracks and ten scanned ceramic objects on a Ferris wheel, with music snippets and texts.

**In short:** This is a purely static website. There is no server code, no build step, no database, no cookies and no tracking. Put the folder as it is on any static host and the site is live.

---

## 1. What's in the folder

```
index.html            the whole site (HTML, CSS and JavaScript in one file)
models/01–10.glb      3D scans of the objects, tracks 1–10 (binary glTF, compressed with gltfpack)
audio/01–10.mp3       music snippets, tracks 1–10 (29 s loops, 128 kbps)
js/three.min.js       three.js r147 (3D engine, MIT licence, self-hosted instead of a CDN)
js/GLTFLoader.js      loader for the .glb files (slightly patched, see section 6)
fonts/                IBM Plex Mono Regular / Medium / Bold (self-hosted, SIL OFL)
favicon.svg / -32.png / apple-touch-icon.png   icons
og-image.png          preview image for shared links (WhatsApp, Instagram, iMessage …)
_headers              cache and security headers (Netlify / Cloudflare Pages)
```

Total size is about 10 MB. On load, the objects come first. The audio snippets load gradually: always the tracks that are at the front of the wheel or right next to it.

## 2. Going live (recommended: Netlify, free)

**Fastest way, about 5 minutes:**

1. Create an account at https://app.netlify.com.
2. Choose **Add new site → Deploy manually** and drag the whole `the-objective-truth` folder into the drop zone.
3. Done. The site is live right away at `something.netlify.app`, with HTTPS.
4. Under **Site configuration → Change site name**, pick a readable name, e.g. `the-objective-truth.netlify.app`.

**Custom domain** (e.g. `theobjectivetruth.com` or `mirrorzone.xyz/op010`): under **Domain management → Add a domain**, enter the domain and set the DNS records shown there at your domain registrar. The HTTPS certificate is issued automatically.

**Updating later:** under **Deploys**, just drag the changed folder in again.

**Alternatives that work the same way:** Cloudflare Pages, Vercel, GitHub Pages, or any classic web hosting via FTP. Classic web hosting ignores the `_headers` file. That's fine, it only improves caching.

**Important:** The site must be served over `http(s)://`. Opened as a local file by double-click (`file://`), the browser won't load the models. To test locally, run `python3 -m http.server` inside the folder and open `http://localhost:8000`.

## 3. After going live

- [ ] **Make og-image absolute:** in `index.html`, change `<meta property="og:image" content="og-image.png">` to the full address, e.g. `https://YOUR-DOMAIN/og-image.png`. Otherwise some apps won't show the preview image. Optionally also add `<meta property="og:url" content="https://YOUR-DOMAIN/">`.
- [ ] **Test on real phones:** iPhone with Safari and Android with Chrome. Check that all 10 objects appear, that swiping up/down turns the wheel and swiping sideways spins the object, that the sound plays, that the text button works, that "legal" opens and closes the Legal & Privacy panel, and that the Bandcamp link opens.
- [ ] **Company details and privacy statement:** replace the placeholders in the Legal & Privacy panel with the real details, see sections 4 and 5.

## 4. Legal (the operator is based in the Netherlands) – please don't forget

Dutch law applies. What counts is where the operator is established, not where the server is.

- **Company details (Art. 3:15d BW):** for a commercial offering, the site must show the name as registered with the KvK, the business address, e-mail and phone, the KvK number and, if applicable, the BTW (VAT) number. A label's release page linking to sales on Bandcamp is, when in doubt, commercial. More info: https://www.acm.nl/nl/verkoop-aan-consumenten/consumenten-informeren/bedrijfsgegevens-vermelden
- **A privacy statement (GDPR/AVG)** is also required, because the host stores IP addresses in its server logs.
- **Already built in:** the vertical "legal" label on the right opens a Legal & Privacy panel with both parts (in English, design from Figma). It still holds **placeholders in `[SQUARE BRACKETS]`**: the real company details, the host and the log retention period must be filled in before launch. The final text will come from the label.
- **No cookie banner** is needed, because the site sets no cookies.
- **Relevant for the privacy statement** (already covered in the panel text):
  - The site sets no cookies and has no tracking or analytics.
  - The font and libraries are self-hosted. No Google Fonts or CDNs are loaded, so no data goes to third parties when someone visits.
  - The host stores server logs for technical reasons (IP address etc.). The host therefore has to be named, together with the data processing agreement (verwerkersovereenkomst). With Netlify (USA), you accept their Data Processing Agreement. If you'd rather keep data in the EU, use an EU host.
  - The Bandcamp link only leads to an external provider once it's clicked.
- Licences of the software used are included: `js/three-LICENSE.txt` (MIT) and `fonts/OFL-LICENSE.txt` (SIL Open Font License).
- The track headlines in "Comsat Navy-Box" are embedded as vector paths (outlines from Figma); the font file itself is not served. If the font is a paid licence, quickly check that it covers use on the web.

## 5. Changing content (places in `index.html`)

| What | Where (search term in `index.html`) |
|---|---|
| Track texts | `const TEXTS = [` (JSON, tracks 1–10 in order) |
| Track headlines | `const HEADS = [` (SVG paths from Figma, font "Comsat Navy-Box". If a title changes, export it again from Figma as outlines) |
| Bandcamp link | `href="https://mirrorzone.bandcamp.com/"` |
| Legal & Privacy text | `class="legal-body"` (plain text; line breaks are kept as written, special characters like `&` written as `&amp;`) |
| Tilt of individual objects | `const TILT = {4: 0.24}` (track 5 "Pendant" tilted slightly forward, value in radians) |
| Strength of the stretch effect | `const STREAK_MIN_K` (lower = longer streaks) |
| Snapping behaviour | `const SNAP_W, SNAP_Z` (stiffness / damping) |
| Replacing objects or music | replace the file in `models/` or `audio/` with one of the same name |

Track-to-file mapping: `01` The Objective Truth · `02` Hussar · `03` Pyrrhonism · `04` Spiti · `05` Pendant · `06` Transmigration · `07` Knight of Cups · `08` Stag Hunt Mosaic · `09` Tenebrism · `10` Rosin.

## 6. Technical notes

- The site is **mobile-first**. On desktop it is shown in a 390 px wide phone frame.
- Rendering uses three.js (WebGL): the objects sit on a Ferris wheel. The front object is textured, all others are black silhouettes. The stretch effect renders the front object into a texture and pulls it apart vertically.
- Audio uses the Web Audio API: forward and reversed buffers per track, crossfaded by the wheel position, and the playback speed follows spinning the object by hand. On iPhones, sound only starts after the first touch. That's a browser rule, not a bug.
- `js/GLTFLoader.js` has one small change from the original: textures load as `data:` URLs instead of `blob:` URLs, because some in-app browsers block `blob:`.
- If something fails to load, a red notice box with the error message appears at the bottom. That helps with debugging on phones.
