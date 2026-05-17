# DaKo Apps — Deployment-Guide (Erst-Setup)

**Version 1.0** — 2026-05-16
**Zielsetup:** GitHub-Pages User-Page-Monorepo `danielokollmer.github.io`

Dieser Guide fuehrt einmalig durch das initiale Repo-Setup (Phase 2 der Roadmap). Nach dem Erst-Setup gilt der einfache Workflow: Aenderung → commit → push → live.

---

## 0. Voraussetzung

- GitHub-Account angelegt (kostenlos, github.com)
- Git lokal installiert (PowerShell: `git --version`)
- GitHub-Username: **danielokollmer** (in allen folgenden URLs fest eingesetzt)

---

## 1. GitHub-Repo erstellen (Browser)

1. github.com → "New repository"
2. Repository name: **`danielokollmer.github.io`** (EXAKT in Kleinbuchstaben, muss zum Username passen)
3. Public ✓ (Pflicht fuer kostenlose GitHub Pages)
4. **NICHT** "Initialize with README" — wir pushen das fertige Repo
5. Create repository

GitHub-URL ergibt sich automatisch: `https://danielokollmer.github.io/`

---

## 2. Lokales Repo initialisieren

PowerShell im Ordner `Apps/_github_publishing/gh_pages_repo`:

```powershell
cd "C:\Users\danik\OneDrive\Dokumente\DaKo_Online_Businesses\Apps\_github_publishing\gh_pages_repo"

git init
git branch -M main
git add .
git commit -m "Initial commit: Periodensystem v2.2 + monorepo scaffolding"

git remote add origin https://github.com/danielokollmer/danielokollmer.github.io.git
git push -u origin main
```

GitHub fragt nach Login: Personal Access Token (PAT) erzeugen unter github.com → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (Scope: `repo`).

---

## 3. GitHub Pages aktivieren

1. Im Repo: Settings → Pages
2. Source: **Deploy from a branch**
3. Branch: **main**, Folder: **`/ (root)`**
4. Save

Nach 1–2 Minuten ist die App live:

- Landing: `https://danielokollmer.github.io/`
- Periodensystem: `https://danielokollmer.github.io/periodensystem/`
- Imprint: `https://danielokollmer.github.io/periodensystem/impressum.html`

---

## 4. HTTPS + PWA-Check

GitHub Pages liefert automatisch HTTPS. Pruefe mit Chrome:

1. Chrome → `https://danielokollmer.github.io/periodensystem/`
2. F12 → Lighthouse → Kategorien: "Progressive Web App" + "Best Practices"
3. **Score >= 90 fuer PWA** ist Voraussetzung fuer Microsoft Store + Google Play
4. Wenn niedriger: Anzeige zeigt fehlende Punkte (oft: Screenshots fehlen, manifest.json-Felder fehlen)

---

## 5. Screenshots erstellen (fuer Stores)

Microsoft Store und Google Play verlangen App-Screenshots. Diese auch in der `manifest.json` referenziert (`screenshots`-Array, bereits konfiguriert).

**Pflicht-Screenshots:**
- Desktop: `periodensystem/screenshots/desktop.png` — 1280x800 PNG
- Mobile: `periodensystem/screenshots/mobile.png` — 390x844 PNG

**Erstellung:**
- Desktop: Chrome F11 (Vollbild), Snipping Tool / Win+Shift+S
- Mobile: Chrome DevTools → Device Mode → iPhone 14 (oder Pixel 7) → Screenshot via DevTools Menue

Danach push → live in Sekunden.

---

## 6. Microsoft Store Submission (Phase 3 der Roadmap)

1. **Developer-Account:** storedeveloper.microsoft.com → kostenlos seit September 2025
2. **App-Namen reservieren:** Partner Center → "Neues Produkt" → MSIX/PWA → Name: "Interaktives Periodensystem"
3. **Package bauen:**
   - pwabuilder.com
   - URL eingeben: `https://danielokollmer.github.io/periodensystem/`
   - "Start" → Report Card >= 70%
   - "Package For Stores" → Windows → Publisher Name + Publisher ID aus Partner Center
   - "Download Package" → erhalte `.msixbundle`
4. **Submission:** Partner Center → App → Submission → Preis (z. B. 2,99 EUR), Altersfreigabe, Screenshots, Beschreibung → `.msixbundle` upload → Einreichen
5. **Review:** 24–48 Stunden

---

## 7. Google Play Submission (Phase 4 der Roadmap)

1. **Developer-Account:** play.google.com/console → 25 USD einmalig
2. **Package bauen:**
   - pwabuilder.com → gleiche URL → "Package for Stores" → Android
   - Package Name: `de.dakocreatives.periodensystem`
   - App Version: 1
   - Display: standalone
   - Signing: "Neuen Keystore erstellen"
3. **KEYSTORE BACKUPEN!** Die generierte `.jks`-Datei und das Passwort SICHER aufbewahren (KeePass, verschluesseltes Backup). Ohne Keystore koennen keine Updates veroeffentlicht werden. **Niemals in Git committen** (siehe `.gitignore`).
4. **SHA-256 in assetlinks.json eintragen:**
   - PWABuilder zeigt den SHA-256-Fingerprint des Keystores
   - Datei oeffnen: `gh_pages_repo/.well-known/assetlinks.json`
   - Den String `REPLACE_AFTER_PWABUILDER_KEYSTORE_GENERATION` durch den echten Fingerprint ersetzen (Format `XX:XX:XX:...:XX`)
   - Commit + push
   - Pruefen: `https://danielokollmer.github.io/.well-known/assetlinks.json` muss den echten SHA-256 zeigen
5. **Submission:** Play Console → "App erstellen" → `.aab` upload → Store-Eintrag (Beschreibung DE+EN, Screenshots, Icon, Datenschutz-URL `https://danielokollmer.github.io/periodensystem/datenschutz.html`) → Interne Tests → Produktion
6. **Review:** 3–7 Werktage beim ersten Mal

---

## 8. Neue App publizieren (zukuenftig)

Nach dem Erst-Setup ist jede weitere App ein Sub-Prozess:

1. App in `gh_pages_repo/<neuer-app-slug>/` anlegen (siehe README.md "Konvention fuer neue Apps")
2. Landing-Page `index.html` um neue Karte erweitern
3. Commit + push → live
4. PWABuilder fuer neue App durchlaufen → eigener Keystore + SHA-256
5. **Neuen Block** in `.well-known/assetlinks.json` ergaenzen:

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "de.dakocreatives.periodensystem",
      "sha256_cert_fingerprints": ["AA:BB:..."]
    }
  },
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "de.dakocreatives.mindnook",
      "sha256_cert_fingerprints": ["CC:DD:..."]
    }
  }
]
```

6. Submit jede App separat (eigener Microsoft-Partner-Center-Eintrag, eigener Play-Console-Eintrag)

---

## Quick-Referenz Daily Workflow

Nach Erst-Setup:

```powershell
# Lokal aendern (Imprint, Content, neue App-Datei ...)
cd "C:\Users\danik\OneDrive\Dokumente\DaKo_Online_Businesses\Apps\_github_publishing\gh_pages_repo"
git add .
git commit -m "Update: <kurzbeschreibung>"
git push
```

Live in 1–2 Minuten unter `https://danielokollmer.github.io/`.

---

## Bekannte Stolpersteine

- **OneDrive + Git:** OneDrive synct die `.git`-Ordner manchmal nach. Wenn Git komische Errors wirft: OneDrive-Pause aktivieren, dann committen.
- **Service Worker Cache:** Nach Aenderung im Browser hart reloaden (Strg+F5) oder Cache-Version in `sw.js` bumpen.
- **`assetlinks.json` Caching:** Google cached die Datei aggressiv. Bei Aenderungen kann es 24h dauern bis TWA neu verifiziert.
- **GitHub Pages Subdomain:** `danielokollmer.github.io` MUSS exakt der Username sein. `dakocreatives.github.io` funktioniert nur, wenn der GitHub-Username `dakocreatives` ist.
