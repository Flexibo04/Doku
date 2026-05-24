# Projektdokumentation – Konsolidierter Status & offene Punkte

**Projekt:** Erstellen einer JTL Wawi Cloud App in Form eines BI-Tools · **Prüfling:** Felix Bock
**Stand:** 23.05.2026 · konsolidiert aus `luecken.md`, `review.md`, `ki_ton_review.md`, `test.md`

**Grobe Bewertung:** nach Behebung von K1/H1/H2/M1 realistisch **~85/100 (gut)**.

---

## ✅ Erledigt

**Inhalt & Struktur**
- ERP-Dashboard als eigener Unterabschnitt mit Code-Auszug
- GraphQL als primärer Datenpfad korrekt dargestellt; Datenmodell-Tabelle (5 Queries) ergänzt
- Zeitraumfilter / `computeDateRange()` dokumentiert
- Testfälle-Tabelle (Kap. 6.1)
- Nutzwertanalyse mit begründeter Gewichtung/Bewertung; Make-or-Buy geschärft
- Stundensätze in der Kostenaufstellung begründet
- Amortisation neu (Lizenzmodell) + **Break-Even-Grafik**
- Fachbegriffe beim ersten Auftreten erklärt; Terminologie JTL-Wawi/JTL-Cloud vereinheitlicht
- Lessons Learned konkretisiert; Lastenheft lösungsneutral; Abweichung vom Projektantrag benannt
- Iterationsplan mit Soll-/Ist-Zeiten + Erläuterung (65 h vs. 80 h)

**Pflichtbestandteile & Formales**
- Literaturverzeichnis (14 Quellen, `\footcite`)
- Betreuer-Daten + Telefon, Prüfungsnummer auf dem Deckblatt
- IHK-Protokoll als Anhang A.12 (PDF geflattet)
- Eigenständigkeitserklärung auf eigener Seite **mit Unterschrift** + „Regensburg, 22.05.2026"
- Komponenten- & Sequenzdiagramm, Mockup, Dashboard-Screenshot eingebunden
- Hardware im Ressourcenanhang; Datum 04.05.–22.05.2026
- Abkürzungsverzeichnis bereinigt (ERP/HTTP/CSS/Wawi rein, 9 tote Definitionen raus)
- Whitespace/TOC kompakt; durchgehende Nummerierung (keine A.x-Kollision)
- Echte Overfull-Stellen behoben (`emergencystretch`)

**Aus dieser Sitzung (PDF-Review)**
- **K1:** „Mock-Daten"-Badge — neuer Screenshot ohne Badge eingebunden
- **H1:** Bild/Text-Widerspruch behoben (Text beschreibt jetzt 4 Karten / 12 Monate / Top-Artikel / niedrige Bestände wie im Screenshot)
- **H2:** alle 13 Artefakte werden jetzt im Text referenziert
- **M1:** Quellenangaben unter allen 7 Abbildungen („Quelle: Eigene Darstellung" / „Eigener Screenshot")

---

## 🟡 Offen – inhaltlich / formal

- **Durchkopplung englischer Begriffe** (Duden): „Cloud App" → Cloud-App, „App Shell" → App-Shell, „Developer Portal" → Developer-Portal, „Single Page Application" → Single-Page-Application. *(umsetzbar)*
- **Fließtext-Länge** ~13–14 Seiten reiner Text (über Regensburg-Richtwert 10–12, im Rahmen der 15-Seiten-Faustregel) → gegen verbindliche Vorgabe abgleichen, ggf. Redundanz straffen.

**Kleinere Detailpunkte (aus früherer Detailprüfung – ggf. teils schon erledigt):**
- NWA-Tabelle: Spaltenkopf „Gew." für die gewichteten Punkte ist doppeldeutig → in „Pkt." umbenennen
- Deployment (Kap. 6.3) recht dünn → 2–3 Sätze (Start via `npm run dev`, `.env` mit `CLIENT_ID`/`CLIENT_SECRET`)
- Struktur-Listing-Kommentar `erp-page/ # ERP-Integrationsseite` → „Dashboard-Seite"
- `Tenant` und `Promise.all` je einen erklärenden Halbsatz spendieren
- `/erp-info`: klarstellen, dass das Dashboard ihn nicht nutzt (nur Erweiterungs-Endpunkt)

---

## ✍️ Offen – Stil / Ton (KI-Floskeln entschärfen)

Diese Formulierungen stehen noch im Text und sollten natürlicher/konkreter klingen
(stärkt „sprachliche Gestaltung" und wirkt weniger generiert):

| # | Stelle | Floskel |
|---|---|---|
| 1 | Kap. 1, 1. Satz | „Die Projektdokumentation schildert den Ablauf…" (inhaltsleer → streichen) |
| 2 | Kap. 1.2 | „strukturiert aufbereitet und benutzerfreundlich dargestellt" |
| 3 | Kap. 1.3 | „strategisches Interesse… Beratungskompetenz ausbauen" |
| 4 | Kap. 1.4 | „strategische Notwendigkeit" |
| 5 | Kap. 2.2 | Padding-Absatz über Open Source |
| 6 | Kap. 2.3 | „in Anlehnung an agile Vorgehensmodelle" (Buzzword, konkretisieren) |
| 7 | Kap. 3.3 | „Neben der wirtschaftlichen Rechtfertigung ergeben sich folgende…" |
| 8 | Kap. 4.1 | „nahtlos integriert" |
| 9 | Kap. 4.1 | gestapelte Superlative bei der React-Begründung |
| 10 | Kap. 4.2 | „bewährte Architekturprinzipien" |
| 11 | Kap. 5.3.3 | „Dashboard-Seite bildet das zentrale Dashboard" (Dopplung) |
| 12 | Kap. 8.1 | „Bei einer rückblickenden Betrachtung… kann festgehalten werden" |
| 13 | Kap. 8.2 | „Ein weiterer Erkenntnisgewinn betrifft…" |
| 14 | Kap. 8.2 | „bewährte sich in diesem Kontext" |
| 15 | Kap. 8.3 | perfekt ausbalancierte Drei-Nomen-Schlussformel |

---

## ⏸️ Optional / bewusst ausgelassen

- **Use-Case-Diagramm** (von dir abgelehnt)
- **Screenshot der Projektstruktur** (aktuell als Code-Listing gelöst – optional)
- ISO-9126-Qualitätsmerkmale explizit benennen
- statische Codeanalyse in der QS erwähnen
- sprechender Kurztitel (1 Wort/Akronym) für Kopfzeile
- ROI explizit (Amortisation/Break-Even ist vorhanden)
- Zeitform vereinheitlichen (Präsens/Präteritum-Mix – subjektiv)
- separates Glossar — laut Richtlinie **nicht nötig** (keine Standard-IT-Begriffe)

---

## 🔎 Erfordert deine Aktion (nicht durch mich umsetzbar)

- **Menschliches Korrekturlesen** (Rechtschreibung/Grammatik), idealerweise mehrere Personen
- **Visuelle Kontrolle des Protokolls** (A.12) – alle Ankreuzkästchen korrekt gerendert?
- **Verbindliche Seitenvorgabe klären** (10–12 vs. ~15) für die Längen-Entscheidung
