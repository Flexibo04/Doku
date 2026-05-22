# Prüfbericht & offene Punkte – Projektdokumentation

**Projekt:** Erstellen einer JTL Wawi Cloud App in Form eines BI-Tools
**Prüfling:** Felix Bock | **Stand:** 23.05.2026
**Grundlage:** IHK-Regensburg-Bewertungsbogen, Inhaltskataloge sowie die Richtlinien des it-berufe-podcast.de

---

## Kurzfazit

Die Dokumentation ist inhaltlich **stark und im Kern abgabefähig**: Struktur, Wirtschaftlichkeit, Technikteil, Nutzwertanalyse, Verzeichnisse, Protokoll und Eigenständigkeitserklärung sind vorhanden und korrekt. Es bleibt **ein kritischer Punkt** (Mock-Badge im Screenshot) sowie einige formale und Konsistenz-Punkte, die die Bewertung heben.

Grobe Einschätzung: solides „gut", mit klarem Weg zu „sehr gut".

---

## ✅ Bereits erledigt

- Literaturverzeichnis mit 14 Quellen (`\footcite`)
- Betreuer-Daten (Name + Telefon) auf dem Deckblatt
- IHK-Protokoll als Anhang A.12 eingebunden (PDF geflattet, damit Eintragungen sichtbar)
- Eigenständigkeitserklärung auf eigener Seite + Unterschrift + „Regensburg, 22.05.2026"
- Benutzerhandbuch ↔ Code teils angeglichen (Event-Name, Interface, Begriffe)
- Event-Name vereinheitlicht (`CustomerChanged`)
- Interface `DashboardKPIs` korrigiert (`totalOrders`, `totalCustomers`)
- Bearbeitungszeitraum-Datum korrigiert (04.05.2026 – 22.05.2026)
- Durchgehende Nummerierung für Abbildungen/Tabellen/Listings (keine A.x-Kollision mehr)
- Abkürzungsverzeichnis bereinigt (ERP/HTTP/CSS/Wawi aufgenommen, 9 tote Definitionen entfernt)
- Echte Overfull-Stellen behoben (`emergencystretch`)
- Lastenheft lösungsneutral gemacht (FA01/FA05/NFA04 ohne Technik-Vorgaben)
- Abweichung vom Projektantrag (REST → GraphQL) in der Einleitung benannt
- Break-Even-/Amortisationsgrafik ergänzt (Abbildung mit Schnittpunkt bei Monat 10)
- Iterationsplan-Hinweis (65 h vs. 80 h erklärt)

---

## 🔴 Kritisch – unbedingt vor Abgabe

| # | Punkt | Aktion |
|---|---|---|
| K1 | **„Mock-Daten"-Badge im Dashboard-Screenshot** (`bilder/image.png`, Abb. 6 *und* Abb. 7). Verstößt direkt gegen die absolute Projektregel „kein Mock-Hinweis, auch nicht in Screenshots". | **Neuer Screenshot ohne Badge** nötig (Bildinhalt kann nicht per LaTeX geändert werden) – **deine Aktion**. |

---

## 🟠 Hoch

| # | Punkt | Aktion |
|---|---|---|
| H1 | **Screenshot ↔ Text widersprechen sich.** Screenshot zeigt 4 KPI-Karten (Umsatz, Bestellungen, Aktive Kunden, Ø Bestellwert), „letzte 12 Monate", Top-5-Artikel, Niedriger Bestand. Text (5.3.3 + Benutzerhandbuch) spricht von „zwei KPIs / letzte 7 Tage". GraphQL-Tabelle + Interface beschreiben aber die reiche Variante. | Text an den echten Screenshot angleichen (4 Karten, Monats-/12-Monats-Ansicht, Top-Artikel, niedrige Bestände). **Umsetzbar.** |
| H2 | **13 Artefakte ohne Textverweis** (`\ref` fehlt). Benotungsrelevant – steht in den 17-Fehlern *und* den offiziellen Kriterien. Betroffen: `fig:architektur`, `fig:komponenten`, `fig:mockup`, `fig:screenshot_app`, `fig:screenshot_dashboard`, `tab:kosten`, `tab:nutzwert`, `tab:api_endpunkte`, `tab:lastenheft_fa`, `tab:lastenheft_nfa`, `tab:pflichtenheft_matrix`, `tab:auftragsstatus`, `tab:fehlerbehebung`. | Verweise (`\autoref`/`Tabelle~\ref`) im Fließtext ergänzen. **Umsetzbar.** |

---

## 🟡 Mittel

| # | Punkt | Aktion |
|---|---|---|
| M1 | **Quellenangaben bei Abbildungen fehlen** (0 vorhanden). IHK verlangt „Titel und Quellenangabe". | Bei eigenen Abbildungen „Quelle: Eigene Darstellung" ergänzen. **Umsetzbar.** |
| M2 | **Durchkopplung englischer Begriffe** (Duden): „Cloud App" → Cloud-App, „App Shell" → App-Shell, „Developer Portal" → Developer-Portal, „Single Page Application" → Single-Page-Application. | Bindestriche ergänzen (Paketnamen wie „JTL Platform UI" bleiben). **Umsetzbar.** |
| M3 | **Fließtext-Länge** ~13–14 Seiten reiner Text (über Regensburg-Richtwert 10–12, im Rahmen der 15-Seiten-Faustregel). Mess-Basis: ~4.020 Wörter Kapitel 1–8. | Gegen verbindliche Vorgabe abgleichen; ggf. Redundanz straffen (siehe unten). |

### Streichkandidaten, falls auf 10–12 Seiten gekürzt werden soll (nur Redundanz, keine Substanz)
1. Nutzwertanalyse-Fließtext (5 Absätze, die die Tabelle nacherzählen) → ~0,8 S.
2. Mehrfach beschriebener Token-/Datenfluss (Sequenzdiagramm + 5.2.3 + 5.3.3) → ~0,6 S.
3. Hilfsfunktionen/Recharts-Datenaufbereitung in 5.3.3 → ~0,3 S.
4. iframe-/App-Shell-Definitions-Einschübe in 4.2 → ~0,2 S.
5. Wiederholte Motivation (1.1, 1.4, 3.1) → ~0,2 S.

---

## 🟢 Niedrig / Optional

- **Zeitform** gemischt (Präsens-Beschreibung vs. „wurde"-Formen). Empfehlung: Durchführung im Präteritum, vor allem konsistent. Subjektiv, flächendeckender Umbau riskant.
- **Sprechender Kurztitel** (1 Wort/Akronym) für Kopfzeile/Diagramme – derzeit nur langer Titel.
- **ROI** nicht explizit (nur Amortisation/Break-Even).
- Gleicher Screenshot zweimal verwendet (A.8 + Benutzerhandbuch) – Abwechslung möglich (Setup-/Pane-Screenshot).
- Zwei Architektur-Abbildungen dicht hintereinander (Übersichtsbox + Komponentendiagramm) – leichte Redundanz, vertretbar.
- 17-pt-tabularx-Log-Warnung (harmlos, kein sichtbarer Fehler – visuell geprüft).

---

## ⏸️ Bewusst ausgelassen (deine Entscheidung)

- Deckblatt: Geburtsdatum / Kontaktdaten Azubi + Betrieb
- Abgabedatum / Prüfungsjahr aufs Deckblatt
- Personelle/organisatorische Schnittstellen in Abschnitt 1.5
- Use-Case-Diagramm

---

## ✅ Richtlinien-Abgleich (it-berufe-podcast) – bestätigt konform

- Gliederung deckt sich mit der empfohlenen 4-teiligen Struktur
- Erzählperspektive: Passiv / 3. Person (keine Ich-Form)
- Nutzwertanalyse formal korrekt (Kriterien erläutert, Skala 1–5, Gewichtung begründet, Berechnung gezeigt)
- Verzeichnisse vollständig (inkl. Listings + Abkürzungen)
- Max. 3 Gliederungsebenen · Blocksatz + Silbentrennung
- **Glossar nicht nötig** (Standard-IT-Begriffe gehören laut Richtlinie nicht hinein) → von der offenen Liste gestrichen
- Schriftart: Helvetica (sans-serif) ist für **IHK Regensburg (Arial)** korrekt, auch wenn der Podcast allgemein Serif empfiehlt
- Wirtschaftlichkeit vollständig · kein Mock-/Dummy-Hinweis im *Text* · kein mock-Verzeichnis im Struktur-Listing · 0 undefinierte Verweise

---

## 🔎 Erfordert deine Aktion (nicht durch mich umsetzbar)

- **Neuer Dashboard-Screenshot ohne „Mock-Daten"-Badge** (K1)
- **Menschliches Korrekturlesen** (Rechtschreibung/Grammatik), idealerweise von mehreren Personen
- **Visuelle Kontrolle des Protokolls** (A.12) – ob alle Ankreuzkästchen korrekt gerendert sind
- **Verbindliche Vorgabe klären** (10–12 vs. ~15 Seiten) für die Längen-Entscheidung

---

## Empfohlene nächste Schritte (durch mich umsetzbar, risikoarm)

1. **H2** – 13 Artefakt-Verweise ergänzen
2. **M1** – Quellenangaben „Quelle: Eigene Darstellung"
3. **M2** – Durchkopplung englischer Begriffe
4. **H1** – Text an den echten Screenshot angleichen (nach neuem Screenshot bzw. parallel)
