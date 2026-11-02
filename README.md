# streakwise.app

Website der iPhone-App Streakwise: Startseite, Support, Datenschutz, Nutzungsbedingungen, Impressum, jeweils Englisch (Hauptsprache, weltweit) und Deutsch unter `/de/`.

Reines statisches HTML mit einer CSS-Datei (`assets/style.css`, Farben aus `lib/core/theme/app_colors.dart` der App). Kein Build, kein JavaScript, keine Cookies, keine externen Ressourcen. Deshalb braucht die Seite keinen Cookie-Banner.

## Seiten und URLs für App Store Connect

| Feld in App Store Connect | Englisch | Deutsch |
|---|---|---|
| Marketing-URL | https://streakwise.app/ | https://streakwise.app/de/ |
| Support-URL | https://streakwise.app/support/ | https://streakwise.app/de/support/ |
| Datenschutzrichtlinie-URL | https://streakwise.app/privacy/ | https://streakwise.app/de/datenschutz/ |
| Nutzungsbedingungen (Beschreibung, RevenueCat-Paywall) | https://streakwise.app/terms/ | https://streakwise.app/de/nutzungsbedingungen/ |
| Impressum | https://streakwise.app/imprint/ | https://streakwise.app/de/impressum/ |

Jede Seite gibt es doppelt; beide verweisen per `hreflang` aufeinander. Eine neue Seite braucht beide Fassungen, Einträge in Kopf- und Fußzeile und in `sitemap.xml`.

## Veröffentlichen

Die Quelle liegt im App-Repo unter `website/`, veröffentlicht wird aus einem eigenen, öffentlichen Repo (der App-Code bleibt privat).

1. Auf GitHub ein **öffentliches** Repo `streakwise-site` anlegen und den **Inhalt** von `website/` in dessen Wurzel kopieren (nicht den Ordner selbst). Auch die versteckten Dateien `.nojekyll` und `CNAME` gehören dazu (im Finder mit ⌘⇧. sichtbar). Per Terminal:
   ```bash
   rsync -a --delete --exclude .git website/ ../streakwise-site/
   ```
   Nach jeder Änderung an `website/` erneut kopieren.
2. Repo › Settings › Pages: Source „Deploy from a branch“, Branch `main`, Ordner `/ (root)`.
3. Custom domain: `streakwise.app` (steht schon in `CNAME`). Nach dem Zertifikat „Enforce HTTPS“ anhaken. `.app` erzwingt HTTPS im Browser (HSTS preload), die Seite lädt also erst, wenn GitHub das Zertifikat ausgestellt hat (Minuten bis ca. 1 Stunde).
4. Domain gegen Übernahme sichern: GitHub › Settings (Konto) › Pages › „Add a domain“, den TXT-Eintrag beim Domain-Anbieter setzen.

### DNS beim Domain-Anbieter

| Typ | Name | Wert |
|---|---|---|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |
| CNAME | www | jxlian02.github.io |
| TXT | `_github-pages-challenge-jxlian02` | (Wert aus Schritt 4) |

Für `support@streakwise.app` zusätzlich MX-Einträge für ein Postfach oder eine Weiterleitung (beim Domain-Anbieter, iCloud+ mit eigener Domain o. Ä.). Die Adresse steht auf jeder Seite, sie muss vor der Einreichung funktionieren.

## Lokal ansehen

```bash
python3 -m http.server 8000 -d website
# http://localhost:8000
```

Die Links sind absolut ab `/`, sie funktionieren deshalb nur unter der eigenen Domain bzw. lokal ab dem Wurzelordner, nicht unter `jxlian02.github.io/streakwise-site/`.

## Vor dem Launch offen

- [ ] **Rechtstexte prüfen lassen.** Datenschutz, Nutzungsbedingungen und Impressum sind nach bestem Wissen aus dem tatsächlichen Datenfluss der App geschrieben, aber nicht anwaltlich geprüft.
- [ ] Angaben in der Datenschutzerklärung mit der echten Einrichtung abgleichen:
  - Supabase-Projekt in Frankfurt (eu-central-1)
  - PostHog-Projekt in der EU-Cloud im **kostenlosen Tarif** (Aufbewahrung 1 Jahr; bezahlte Tarife halten 7 Jahre, kürzer lässt es sich nicht stellen). Von PostHog schriftlich bestätigen lassen, dass Events nach Ablauf gelöscht und nicht nur ausgeblendet werden. Die Datenschutzerklärung sagt „12 Monate nach Erfassung automatisch gelöscht“.
  - In PostHog „Discard client IP data“ einschalten (die Erklärung sagt, die IP wird nicht gespeichert)
  - Auftragsverarbeitungsverträge (DPA) mit Supabase, RevenueCat und PostHog abschließen
- [ ] **c/o-Anschrift:** Die Anschrift eines Impressum-Service ist verbreitet, als „ladungsfähige Anschrift“ aber nicht ganz unumstritten. Bei der Prüfung der Rechtstexte mit ansprechen.
- [ ] Sobald die App live ist: Platzhalter „Coming soon to the App Store“ / „Bald im App Store“ in `index.html` und `de/index.html` durch das offizielle Badge von Apple (Marketing-Ressourcen, nicht selbst nachbauen) mit Link auf die App ersetzen und `<meta name="apple-itunes-app" content="app-id=…">` einkommentieren.
- [ ] Finales App-Icon: `assets/app-icon.png` (256 px), `apple-touch-icon.png` (180 px) und `favicon.png` (64 px) aus dem neuen Icon erzeugen, z. B. `sips -z 256 256 app_icon.png --out assets/app-icon.png`.
- [ ] Screenshots der App auf der Startseite (statt der nachgebauten Vorschau), sobald es finale gibt.

## Weltweite Veröffentlichung: App Store Connect

- Primärsprache der Metadaten Englisch, Deutsch als zweite Lokalisierung.
- **EU-Händlerstatus (Digital Services Act):** Wer Abos verkauft, ist Händler. Apple zeigt dann in den EU-Stores Anschrift, E-Mail **und Telefonnummer** öffentlich an. Die Anschrift sollte zur c/o-Adresse im Impressum passen.
- **China (Festland):** Apps brauchen eine ICP-Registrierungsnummer. Ohne sie China in „Preise und Verfügbarkeit“ abwählen.
- App-Datenschutz-Etiketten passend zur Datenschutzerklärung angeben (Kontakt- und Kennungsdaten, Gesundheit & Fitness über die Sicherung, Nutzungsdaten mit Einwilligung; kein Tracking).
