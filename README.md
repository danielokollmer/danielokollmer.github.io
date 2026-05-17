# DaKo Apps — GitHub Pages Hosting (Mono-Repo)

**Version 1.0** — 2026-05-16
**Owner:** Daniel Kollmer
**Repo-Strategie:** User-Page-Monorepo (Option C aus IMPRINT_DISCLAIMER_TEMPLATES Master)

## Was ist das

Dieses Verzeichnis enthaelt den vollstaendigen Inhalt fuer das GitHub-Pages-Repo `danielokollmer.github.io`. Alle DaKo-Web-Apps werden als Subordner unter diesem User-Page-Repo gehostet:

```
https://danielokollmer.github.io/periodensystem/   ← Periodensystem
https://danielokollmer.github.io/mindnook/         ← MindNook (geplant)
https://danielokollmer.github.io/<neue-app>/       ← Konvention fuer alle weiteren
```

## Warum diese Struktur

**Technisches Constraint TWA / Google Play:**
`assetlinks.json` MUSS unter `https://<host>/.well-known/assetlinks.json` liegen — am Root der Domain. Bei mehreren Apps stehen alle Eintraege in dieser einen Datei. Ohne User-Page-Repo skaliert das nicht.

**Vorteile:**
- Eine `assetlinks.json` fuer alle Apps (TWA-tauglich)
- Saubere URLs ohne Repo-Praefix
- Ein GitHub Pages Setup, ein Deployment-Workflow
- Cross-Linking zwischen Apps moeglich (z. B. Landing-Page mit Karten)

## Ordnerstruktur

```
gh_pages_repo/
├── index.html                  ← Landing-Page mit App-Karten (oeffentlich)
├── README.md                   ← Dieses File (Dev-Doku)
├── DEPLOYMENT.md               ← Schritt-fuer-Schritt zum Live-Setup
├── .gitignore                  ← Standard + Keystore-Schutz
├── .well-known/
│   └── assetlinks.json         ← TWA-Verifikation fuer ALLE Apps (zentral)
└── periodensystem/             ← App 1 (live nach Phase 2 Deployment)
    ├── periodensystem.html     ← Haupt-Datei (PWA-fahig)
    ├── impressum.html          ← Variante A aus Imprint-Master
    ├── datenschutz.html        ← DSGVO-konform
    ├── hilfe.html
    ├── roadmap.html            ← Deployment-Plan (oeffentlich, Transparenz)
    ├── manifest.json           ← PWA-Metadaten
    ├── sw.js                   ← Service Worker (Offline)
    ├── icons/                  ← 5 Groessen (48/96/144/192/512)
    └── screenshots/            ← Store-Material (TODO)
```

## Konvention fuer neue Apps

**Pflicht-Schritte bei jeder neuen App:**

1. **Ordner anlegen:** `gh_pages_repo/<app-slug>/` — Slug klein, keine Umlaute, keine Unterstriche
2. **PWA-Dateien:**
   - `<app-slug>.html` — Haupt-App (Service Worker registrieren!)
   - `manifest.json` — `start_url: ./<app-slug>.html`, `scope: ./`, eigene `theme_color`
   - `sw.js` — eigener Cache-Name pro App (kollidiert sonst zwischen Apps!)
3. **Pflicht-Begleitseiten (aus Master `_MASTER/IMPRINT_DISCLAIMER_TEMPLATES.md` Variante A):**
   - `impressum.html` — Klarname + Adresse + § 5 DDG
   - `datenschutz.html` — DSGVO Art. 13
4. **Icons:** 5 PNG-Groessen `icons/icon-{48,96,144,192,512}.png` mit `purpose: "any maskable"` fuer die grossen Groessen
5. **Landing-Page aktualisieren:** Neue Karte in `index.html` hinzufuegen
6. **TWA-Eintrag ergaenzen:** Nach PWABuilder-Signing in `.well-known/assetlinks.json` neuen Block einfuegen (siehe DEPLOYMENT.md)
7. **Cross-Link Konsistenz:** Footer-Links in impressum/datenschutz/hilfe immer auf relative Pfade `./<app-slug>.html`

**Naming-Konvention Packages (Android/TWA):**
`de.dakocreatives.<app-slug>` — z. B. `de.dakocreatives.periodensystem`, `de.dakocreatives.mindnook`

## Geltungsbereich Imprint/Disclaimer

Jede App-Subordner-Variante MUSS:
- `impressum.html` mit Daniel Kollmer + Baumgartnerstrasse 21 + DE-79540 Loerrach + `danielokollmer@gmail.com`
- § 5 DDG, § 18 Abs. 2 MStV, EU-ODR-Link, GitHub-Pages-Hosting-Hinweis
- DSGVO Art. 13 Datenschutzerklaerung
- AI Bildquellen-Block (OpenArt AI + Pixabay)
- Entertainment & Educational Disclaimer + Equality & Respect Block

Vorlage: `C:\Users\danik\OneDrive\Dokumente\DaKo_Online_Businesses\_MASTER\IMPRINT_DISCLAIMER_TEMPLATES.md` Variante A.

## Cache-Konvention sw.js (WICHTIG)

Jede App MUSS einen eindeutigen Cache-Namen verwenden, sonst ueberschreiben sich die Caches gegenseitig:

```js
const CACHE = "<app-slug>-v1.0";   // z. B. "periodensystem-v2.2"
```

Bei jeder Content-Aenderung: Version inkrementieren, damit Service Worker den neuen Cache lade.

## Workflow

Lokale Aenderungen → commit → push zu `danielokollmer.github.io` (main branch) → GitHub Pages re-deployt automatisch innerhalb 1–2 Minuten.

Siehe `DEPLOYMENT.md` fuer den ersten Setup.
