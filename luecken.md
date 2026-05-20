# Lückenanalyse & Umsetzungsplan – Doku.tex

## P1 – Kritisch (inhaltlich falsch oder unvollständig)

---

### ~~L1: ERP-Seite hat keinen eigenen Unterabschnitt~~ ✅ ERLEDIGT

**Umgesetzt:** `\subsection{ERP-Dashboard mit Kennzahlen}` in Kapitel 5.4 ergänzt.
Fließtext beschreibt KPI-Karten, BarChart und Bestellliste. Code-Listing aus
`ErpPage.tsx` (load-Callback + JSX-Auszug) enthalten. `formatEur()` und
`formatDate()` erwähnt. Verweis auf Anhang A.11 gesetzt.

---

### L2: GraphQL-Integration ist dokumentarisch unsichtbar

**Problem:**
Das Backend hat einen `/graphql`-Proxy zu `https://api.jtl-cloud.com/erp/v2/graphql`.
`api.ts` enthält fünf konkrete Queries (`DashboardKPIs`, `RevenueGrouped`,
`TopItemsByRevenue`, `QuerySalesOrders`, `LowStockItems`) sowie `fetchDashboard()`
und `computeDateRange()`. Das ist die gesamte Datenabruf-Logik des BI-Tools.
In der Doku erscheint `/graphql` nur in der Endpunkte-Tabelle — der Mechanismus
dahinter wird nirgends erklärt.

**Geplante Umsetzung:**
- Neuer Unterabschnitt `\subsection{GraphQL-Datenabruf}` in Kapitel 5.3
- Erklärung warum GraphQL statt REST (typsichere Abfragen, flexible Selektion)
- Code-Listing: Einen repräsentativen Query aus `api.ts` zeigen (z. B. `DashboardKPIs`)
- Erläuterung der `fetchDashboard()`-Funktion und des `createGqlClient()`
- In Abschnitt 4.4 (Interne API-Endpunkte): kurze Erklärung hinzufügen, dass der
  `/graphql`-Endpunkt als authentifizierter Proxy fungiert

---

### L2a: Doku.tex beschreibt GraphQL als Randthema — tatsächlich ist es der einzige Datenabruf-Mechanismus

**Problem:**
Die aktuelle Doku.tex stellt den REST-Endpunkt `/erp-info/:tenantId/:endpoint` als
primären Datenabruf-Mechanismus dar (prominent in der API-Tabelle, erklärt in Kapitel 5.3).
Das ist faktisch falsch. Beim Lesen des tatsächlichen Quellcodes zeigt sich:

- `ErpPage.tsx` ruft ausschließlich `fetchDashboard()` aus `api.ts` auf
- `fetchDashboard()` sendet **fünf GraphQL-Queries parallel** via `Promise.all` an
  den `/graphql`-Backend-Proxy (`DashboardKPIs`, `RevenueGrouped`, `TopItemsByRevenue`,
  `QuerySalesOrders`, `LowStockItems`)
- Der REST-Endpunkt `/erp-info` wird vom Dashboard **überhaupt nicht verwendet**
- GraphQL via `graphql-request` ist damit der **alleinige Datenabruf-Kanal** der App

Dieser Fehler entstand dadurch, dass bei einer früheren Analyse nur `index.ts` gelesen
wurde und `api.ts` sowie `ErpPage.tsx` nicht. Die Doku beschreibt aktuell eine
Architektur, die so nicht existiert.

**Geplante Umsetzung:**
- Architekturdiagramm in Abschnitt 4.2 korrigieren: GraphQL als primären Datenpfad
  einzeichnen, REST `/erp-info` als vorhandenen aber im Dashboard ungenutzten Endpunkt
  kennzeichnen
- Abschnitt 4.4.1 (Interne API-Endpunkte): Tabelleneintrag für `/graphql` ausführlicher
  beschreiben und den Hinweis ergänzen, dass `/erp-info` als generischer Erweiterungs-
  Endpunkt vorgehalten wird, das Dashboard aber ausschließlich GraphQL nutzt
- Kapitel 5.3 umstrukturieren: GraphQL-Proxy und `fetchDashboard()` als Hauptthema,
  `/erp-info`-Proxy als sekundären Punkt
- Fließtext in Kapitel 5.4 (ERP-Seite) präzisieren: „Die Daten werden über einen
  einzigen `fetchDashboard()`-Aufruf abgerufen, der intern fünf GraphQL-Queries
  parallel ausführt und die Ergebnisse gebündelt zurückgibt."

---

### L3: Datenmodell fehlt fast vollständig

**Problem:**
Abschnitt 4.4 heißt „Datenmodell und API-Kommunikation", beschreibt aber nur
Endpunkte und AppBridge. Die tatsächlichen Datenstrukturen (`DashboardKPIs`,
`RevenuePoint`, `Order`, `LowStockItem`, `TopProduct`) — also was die App
abruft und darstellt — kommen nicht vor. Alle Beispieldokus haben Typen-Definitionen
oder zumindest eine Tabelle der relevanten Datenfelder.

**Geplante Umsetzung:**
- Dokumentation orientiert sich an der GraphQL-Konvention: pro Query wird
  **Operation → Variablen → Rückgabefelder** dargestellt, so wie es die
  JTL-Cloud-API-Dokumentation strukturiert
- Tabelle in Abschnitt 4.4 mit folgenden Spalten:
  `Query-Operation | Variablen ($from, $to, $groupBy) | Rückgabetyp | Felder`
  — eine Zeile pro der fünf Queries aus `api.ts`:

  | Operation | Variablen | Rückgabetyp | Felder |
  |---|---|---|---|
  | `DashboardKPIs` | `$from`, `$to` | `DashboardKPIs` | `totalRevenue`, `totalOrders`, `totalCustomers`, `avgOrderValue` |
  | `RevenueGrouped` | `$from`, `$to`, `$groupBy` | `RevenuePoint[]` | `label`, `revenue`, `orders` |
  | `TopItemsByRevenue` | — | `TopProduct[]` | `sku`, `name`, `stockInOrders`, `salesPriceGross`, `totalRevenue` |
  | `RecentOrdersDetailed` | — | `Order[]` | `salesOrderNumber`, `salesOrderDate`, `totalGrossAmount`, `currencyIso`, `companyName`, `status` |
  | `LowStockItems` | — | `LowStockItem[]` | `sku`, `name`, `quantityTotal`, `quantityAvailable`, `quantityLockedForShipment` |

- Ergänzend: Code-Listing mit den TypeScript-Interfaces aus `api.ts` (Kommentar
  „API types matching real JTL field names" macht deutlich, dass die Interfaces
  1:1 dem GraphQL-Schema der JTL-Cloud entsprechen)
- Kurzer Absatz: Die Felder orientieren sich an den tatsächlichen Feldnamen der
  JTL-ERP-GraphQL-API — keine eigene Modellierung, sondern direkte Übernahme
  der API-Typen in TypeScript-Interfaces

---

### ~~L4: Zeitraumfilter nicht dokumentiert~~ ✅ ERLEDIGT

**Umgesetzt:** Im Rahmen von L1 als eigener Absatz ergänzt. `computeDateRange()`
mit allen vier Modi (daily/weekly/monthly/yearly) erklärt, Rückgabewert
(`from`, `to`, `groupBy`) beschrieben, Architektur-Begründung enthalten
(kein Backend-Eingriff nötig, da `groupBy` direkt als GraphQL-Variable übergeben wird).

---

## P2 – Hoch (strukturelle Schwächen gegenüber IHK-Standard)

---

### L5: Qualitätssicherung zu dünn

**Problem:**
Kapitel 6.1 sagt nur „Funktionstests wurden durchgeführt". IHK-Kriterium 1.3.2
fordert, dass Projektschritte begründet und ihre Ergebnisse dargestellt werden.
Ottmanns Beispieldoku hat Whitebox-Tests mit konkreten Testfällen dokumentiert.

**Geplante Umsetzung:**
- Tabelle mit mindestens 6–8 Testfällen: Testgegenstand, Erwartetes Ergebnis,
  Tatsächliches Ergebnis, Status (Bestanden/Fehlgeschlagen)
- Testfälle abdecken: Session-Token-Verifikation (gültig/ungültig),
  `/connect-tenant`-Endpunkt, GraphQL-Proxy, AppBridge-Events,
  Zeitraumfilter, Fehlerbehandlung bei API-Ausfall
- Kurze Erwähnung von Vitest für Unit-Tests (soweit vorhanden)

---

### L6: Amortisationsberechnung unfertig

**Problem:**
Abschnitt 3.2.3 sagt: „Eine detaillierte Amortisationsberechnung wird nach
Fertigstellung des Prototyps auf Basis von Kundenfeedback durchgeführt."
Das Projekt ist jetzt abgeschlossen — dieser Satz steht als offene Aussage
im fertigen Dokument. Prüfer erwarten eine konkrete Berechnung.

**Geplante Umsetzung:**
- Schätzung auf Basis realistischer Annahmen:
  - Zeitersparnis pro Kunde: x h/Monat durch manuelle Auswertungen entfallen
  - Interner Stundensatz: z. B. 15 €/h (Kundenseite)
  - Anzahl potenzieller Kunden: z. B. 5 (RIS-Kundenstamm)
  - Monatlicher Nutzen: Ersparnis × Kunden
  - Amortisationsdauer = Projektkosten (1.450 €) ÷ monatlicher Nutzen
- Alternativ: Lizenzmodell als Einnahmequelle (monatliche SaaS-Gebühr)
- Konkrete Zahlen von Felix erfragen oder mit realistischen Annahmen füllen

---

### L7: Nutzwertanalyse zu schmal

**Problem:**
Die Nutzwertanalyse in Abschnitt 4.1 vergleicht nur Backend-Technologien
(Node.js vs. Python vs. Java). Die Frontend-Entscheidung (React vs. Vue vs. Angular)
und die Monorepo-Entscheidung (Turbo) sind vollständig unbegründet.
IHK-Kriterium: „Jede Technologie- oder Architekturentscheidung nachvollziehbar
begründen."

**Geplante Umsetzung:**
- Zweite Nutzwertanalyse-Tabelle für das Frontend:
  Kriterien: JTL-SDK-Kompatibilität, Komponentenmodel, TypeScript-Support,
  Kenntnisstand, Community — React vs. Vue vs. Angular
- Turbo/Monorepo-Entscheidung: kurzer Absatz mit Begründung
  (gemeinsame TypeScript-Konfiguration, parallele Builds, einfachere Abhängigkeiten)
- Vollständige Nutzwertanalyse beider Tabellen in Anhang A.5

---

### L8: Iterationsplan ohne Zeitangaben

**Problem:**
Anhang A.8 ist eine reine Stichpunktliste ohne Zeitrahmen oder Abhängigkeiten.
IHK-Kriterium 1.3.3 fordert explizit: Auflistung von geplantem und tatsächlichem
Zeitaufwand pro Schritt mit detaillierter Tätigkeitsbeschreibung.

**Geplante Umsetzung:**
- Iterationsplan als Tabelle: Iteration | Tätigkeit | Geplant (h) | Tatsächlich (h)
- Die Iterationen aus dem bestehenden Bullet-Plan übernehmen und mit Zeiten versehen
- Abweichungen mit kurzem Kommentar begründen (passt zu Soll-/Ist-Vergleich in Kap. 8)

---

### ~~L17: Inhaltsverzeichnis und genereller Whitespace-Überschuss~~ ✅ ERLEDIGT

**Problem:**
Das Inhaltsverzeichnis rendert aktuell jeden Eintrag auf einer eigenen Seite statt
kompakt untereinander — vermutlich durch übermäßige `\vspace`-Befehle, fehlendes
`\setlength{\parskip}` oder ein zu groß gesetztes `tocvsep`/`beforeskip` in der
Kapitelformatierung. Auch im restlichen Dokument gibt es auffällig viel Leerraum:
zwischen Überschriften und nachfolgendem Text, vor/nach Listings und Tabellen,
und zwischen Absätzen. Das wirkt unfertig und verschwendet das knappe Seitenbudget
(10–12 Seiten Fließtext).

**Geplante Umsetzung:**
- `Doku.tex` auf übermäßige `\vspace`, `\bigskip`, `\medskip` und `\\`-Zeilenumbrüche
  durchsuchen und entfernen oder auf Standardabstände reduzieren
- `\setlength{\parskip}` prüfen — sollte nicht mehr als `0.5\baselineskip` sein
- TOC-Abstände prüfen: `titletoc`-Paket oder `tocloft` verwenden, um
  `\setlength{\cftbeforesecskip}` und `\setlength{\cftbeforesubsecskip}` auf
  kompakte Werte zu setzen (z. B. `2pt`)
- Kapitelüberschriften (`\chapter`, `\section`): `titlesec`-Paket prüfen,
  `beforeskip` und `afterskip` auf sinnvolle Werte kürzen
- Nach Listings und Tabellen: überflüssige Leerzeilen im LaTeX-Quellcode entfernen
- Ziel: TOC auf einer einzigen Seite; Fließtext ohne optisch störende Lücken

---

### ~~L16: Fachbegriffe werden ohne Erklärung verwendet~~ ✅ ERLEDIGT

**Problem:**
Die Dokumentation setzt an vielen Stellen technisches Vorwissen voraus, das Laien
und Prüfer ohne tiefen Fachbezug nicht mitbringen. IHK-Kriterium 1.2.1 fordert
Verständlichkeit und Nachvollziehbarkeit. Jeder Begriff muss beim **ersten Auftreten**
in einem Halbsatz erklärt werden — danach genügt der Kurzname.

**Begriffe und benötigte Erklärung (nach Abschnitt geordnet):**

| Begriff | Wo | Was erklären |
|---|---|---|
| **Marktplatzanbindungen** | Kap. 1 (Ist-Analyse) | Anbindung von Online-Shops/Marktplätzen (z. B. Amazon, eBay) an das Warenwirtschaftssystem über standardisierte Schnittstellen |
| **JTL Cloud API / REST-API** | Kap. 1 / 4.4 | REST-API: Schnittstelle, über die Systeme per HTTP-Anfragen Daten austauschen; JTL Cloud API: die von JTL bereitgestellte REST-Schnittstelle zur ERP-Datenabfrage |
| **Key Performance Indicators (KPI)** | Kap. 1 / 3.1 | Kennzahlen, die den Geschäftserfolg messbar machen (z. B. Umsatz, Auftragsanzahl) — bereits als Abkürzung vorhanden, aber nie inhaltlich eingeführt |
| **Monorepo-Struktur** | Kap. 2 / 5.2 | Entwicklungsansatz, bei dem Frontend und Backend in einem einzigen Versionskontroll-Repository verwaltet werden statt in getrennten Repositories |
| **iframe** | Kap. 4.1 / 4.2 | HTML-Element, das eine fremde Webseite als eingebetteten Rahmen innerhalb einer anderen Webseite anzeigt — hier: die Cloud App läuft als iframe innerhalb der JTL-Wawi |
| **Host-System** | Kap. 4.2 | Die übergeordnete Anwendung (JTL-Wawi), in die der iframe eingebettet ist und die den Rahmen (App Shell) stellt |
| **AppBridge** | Kap. 4.4.2 | Kommunikationsschicht zwischen dem eingebetteten iframe (App) und dem Host-System (JTL-Wawi App Shell); kapselt `window.postMessage`-Aufrufe hinter einer strukturierten API |
| **Inter-Process Communication (IPC)** | Kap. 4.4.2 | Mechanismus zur Kommunikation zwischen zwei isolierten Prozessen oder Browser-Kontexten — hier: zwischen iframe und Host über `postMessage` |
| **UI-Komponenten** | Kap. 4.3 / 5.4 | Wiederverwendbare visuelle Bausteine einer Oberfläche (z. B. Schaltflächen, Tabellen, Diagramme), die zusammengesetzt die Benutzeroberfläche bilden |
| **OAuth2-Authentifizierung / client_credentials-Flow** | Kap. 4.4.3 | OAuth2: offener Standard zur Zugriffsautorisierung; client_credentials: Flow für Server-zu-Server-Kommunikation ohne Nutzerinteraktion — Backend authentifiziert sich direkt mit App-ID und App-Secret |
| **JTL-Cloud Auth-Server** | Kap. 4.4.3 | Zentraler Authentifizierungsserver der JTL-Plattform (`auth.jtl-cloud.com`), der nach erfolgreicher Anmeldung Zugriffstoken ausstellt |
| **JWT (JSON Web Token)** | Kap. 4.4.3 / 5.3.1 | Kompaktes, kryptografisch signiertes Trägertoken im Format Header.Payload.Signature; enthält Ansprüche (Claims) wie Tenant-ID und Ablaufzeit, die das Backend ohne Datenbankabfrage prüfen kann |
| **EdDSA-Algorithmus** | Kap. 5.3.1 | Modernes asymmetrisches Signaturverfahren auf Basis elliptischer Kurven (Edwards-curve Digital Signature Algorithm); von JTL zum Signieren der JWTs verwendet — sicherer und schneller als RSA |
| **Statusbadge** | Kap. 5.4 / ERP-Seite | Kleines farbiges Label in der Benutzeroberfläche, das den aktuellen Zustand eines Datensatzes (z. B. Bestellstatus: „Offen", „Versendet") visuell signalisiert |
| **GraphQL** | Kap. 5.3 / L2 | Abfragesprache für APIs, bei der der Client exakt die Felder angibt, die er benötigt — im Gegensatz zu REST, wo der Server feste Antwortstrukturen vorgibt; ein einziger Endpunkt bedient alle Abfragen |
| **Beta-APIs** | Kap. 4 / Ausblick | Schnittstellen, die sich noch im Vorabveröffentlichungsstatus befinden — Funktionsumfang und Stabilität können sich ohne Vorankündigung ändern |

**Geplante Umsetzung:**
- Jeden Begriff in der obigen Tabelle beim ersten Auftreten mit einem Halbsatz
  in Klammern einführen, z. B.:
  *„…authentifiziert sich das Backend über den OAuth2-Client-Credentials-Flow
  (ein Standard für Server-zu-Server-Authentifizierung ohne Nutzerbeteiligung)…"*
- Begriffe, für die noch kein `\newacronym` existiert (iframe, Monorepo, Statusbadge,
  Beta-API, Host-System), bei Bedarf als Glossareintrag ergänzen — oder nur inline
  erklären wenn sie nur einmal vorkommen
- Reihenfolge der Erklärungen richtet sich nach dem ersten Auftreten im Text,
  nicht nach dieser Tabelle

---

## P3 – Mittel (Platzhalter die Felix befüllen muss)

---

### L9: Betreuer-Daten fehlen auf Deckblatt

**Problem:**
`\BetreuerName` und `\BetreuerTelefon` sind noch `[EINTRAGEN]`.
Pflichtbestandteil des Deckblatts laut IHK Regensburg.

**Geplante Umsetzung:**
- Felix trägt Name und Telefonnummer des Projektbetreuers ein
- In `Doku.tex` Zeilen 226–227 aktualisieren

---

### L10: Hardware im Ressourcenanhang fehlt

**Problem:**
Anhang A.2 hat `[HARDWARE EINTRAGEN]` als Platzhalter für die Entwickler-Workstation.

**Geplante Umsetzung:**
- Felix trägt die genaue Hardware-Bezeichnung ein (z. B. „Dell OptiPlex 7090, Intel
  Core i7-11700, 32 GB RAM, Ubuntu 24.04 LTS")

---

### L11: Soll-/Ist-Vergleich ohne Ist-Zeiten

**Problem:**
Tabelle in Kapitel 8.1 hat für alle fünf Phasen `[EINTRAGEN]` als Ist-Zeit und
Differenz. Ohne diese Zahlen ist das wichtigste Fazit-Element leer.

**Geplante Umsetzung:**
- Felix trägt die tatsächlich aufgewendeten Stunden pro Phase ein
- Falls Abweichungen vorhanden: Begründung im Fließtext unter der Tabelle ergänzen
  (IHK-Pflicht: Abweichungen müssen begründet werden)

---

### L18: Erstellte Diagramme in Doku.tex einbinden

**Problem:**
Zwei fertige Diagramme liegen als HTML-Dateien unter `bilder/` vor, sind aber noch
nicht als Abbildungen in der Dokumentation eingebunden:

- `bilder/diagramm_komponenten.html` — Komponentendiagramm der Systemarchitektur
- `bilder/diagramm_sequenz.html` — Sequenzdiagramm (vermutlich OAuth2-/Token-Flow)

In Abschnitt 4.2 existiert aktuell nur eine textbasierte ASCII-Box-Abbildung
(`\fcolorbox`). Das Komponentendiagramm ist informativer und IHK-gerechter.
Das Sequenzdiagramm fehlt vollständig — es wäre der ideale Beleg für Abschnitt
4.4.3 (OAuth2-Authentifizierungskonzept) oder 5.3.1 (JWKS-Verifikation).

**Geplante Umsetzung:**
- Beide HTML-Dateien im Browser öffnen und als PNG exportieren:
  - `bilder/diagramm_komponenten.html` → `bilder/diagramm_komponenten.png`
  - `bilder/diagramm_sequenz.html` → `bilder/diagramm_sequenz.png`
- Komponentendiagramm in Abschnitt 4.2 einbinden — die bestehende `\fcolorbox`-Abbildung
  ersetzen oder direkt darunter als zweite Abbildung einfügen
- Sequenzdiagramm in Abschnitt 4.4.3 einbinden, mit erläuterndem Satz im Fließtext
  (z. B.: *„Abbildung~\ref{fig:sequenz} zeigt den vollständigen Ablauf der
  Token-Verifikation vom Frontend bis zur JTL-Cloud-API."*)
- Beide Abbildungen mit `\label`, `\caption` und `[H]` versehen;
  im Text via `\autoref{}` referenzieren

---

## P4 – Bilder (abhängig von Felix)

---

### L12: Use-Case-Diagramm fehlt (Anhang A.3)

**Geplante Umsetzung:**
- Diagramm zeigt: Akteure (Händler/Nutzer, JTL-Cloud-API, RIS-Backend)
  und Use Cases (App einrichten, Dashboard aufrufen, Zeitraum filtern,
  Kundendaten abrufen, Token verifizieren)
- Als PNG exportieren → `bilder/usecase_diagramm.png`
- Tool-Empfehlung: draw.io, PlantUML oder Lucidchart

---

### L13: UI-Mockups als PNG in Anhang A.6 einbinden

**Problem:**
Anhang A.6 hat nur Platzhaltertext. Die Mockup-HTML-Dateien existieren bereits unter
`bilder/mockup_dashboard.html` und `bilder/mockup_setup.html`, müssen aber noch als
PNG exportiert und in die Doku.tex eingebunden werden.

**Geplante Umsetzung:**
- `bilder/mockup_dashboard.html` im Browser öffnen, auf 390 px Breite einstellen,
  screenshotten → `bilder/mockup_dashboard.png`
- `bilder/mockup_setup.html` im Browser öffnen, screenshotten → `bilder/mockup_setup.png`
- In Anhang A.6 der Doku.tex beide Abbildungen mit `\includegraphics` einbinden
- Platzhaltertext im Anhang durch die echten Abbildungen ersetzen

---

### L14: Screenshot Projektstruktur fehlt (Anhang A.10)

**Geplante Umsetzung:**
- Screenshot der IDE (VS Code) mit geöffnetem Explorer auf dem Monorepo
- Als PNG → `bilder/screenshot_struktur.png`

---

### L15: Screenshot der Anwendung fehlt (Anhang A.11)

**Geplante Umsetzung:**
- Screenshot des fertigen ERP-Dashboards mit KPI-Karten und Chart
- Als PNG → `bilder/screenshot_app.png`

---

## Ausbilder-Feedback (Doku_annotated.pdf, 2026-05-19)

---

### L19: Kleine Textfixes – sofort umsetzbar

| Stelle | Anmerkung | Fix |
|---|---|---|
| Kap. 1 erster Satz | „Die folgende Projektdokumentation…" – „kann weg" | „Die folgende" streichen → „Diese Projektdokumentation…" |
| Kap. 1 / DACH | „Deutschland, Österreich, Schweiz (deutschsprachiger Wirtschaftsraum) (DACH)-Raum" zu klobig | Schreiben: „in Deutschland, Österreich und der Schweiz" – kein Klammerkonstrukt |
| Kap. 3.1 | „Bordmittel" – „wilder Begriff" | Ersetzen durch: „integrierte Analyse-Werkzeuge der JTL-Software" |
| Kap. 2.2 | „kostenfrei (z. B. als Open Source)" – „open source != kostenfrei" | Formulierung korrigieren: Open-Source-Software ist nicht zwingend kostenlos, aber lizenzkostenfrei |
| Kap. 1.5 | System-Schnittstellen: fehlt Erklärung WIE das System kommuniziert | Einleitungssatz ergänzen: „Die Kommunikation erfolgt jeweils über HTTPS-Anfragen." |
| Kap. 6.2 | „zuständigen Projektbetreuer der RIS" – „who?" | Namen des Betreuers eintragen (Felix) |

---

### L20: Amortisationsdauer – Grundannahme falsch, komplett neu schreiben

**Problem:**
Der Ausbilder moniert: „Die App amortisiert sich nicht bei RIS basierend auf der
Zeitersparnis beim Kunden – RIS *verkauft* die App." Die aktuelle Berechnung über
Zeitersparnis beim Händler ist damit falsch angesetzt. Stattdessen empfiehlt er:
entweder strategischen Wert (Wissenserwerb, Marktpositionierung) begründen, ODER
ein konkretes Gedankenspiel mit Lizenzpreisen machen:
*„Annahme: monatliche Lizenzgebühr X € pro Kunde, bei 5 Kunden amortisiert sich
das Projekt nach Y Monaten."*

**Geplante Umsetzung:**
- Abschnitt 3.2.3 komplett umschreiben: weg von Zeitersparnis-Modell
- Zwei Ansätze möglich (Felix entscheidet):
  1. **Strategischer Wert**: Amortisation lässt sich zum Projektzeitpunkt nicht
     monetär berechnen, da es primär um Wissensaufbau und First-Mover-Vorteil geht
  2. **Lizenzmodell-Gedankenspiel**: z. B. 29 €/Monat pro Kunde × 5 Kunden =
     145 €/Monat → Amortisation nach ca. 10 Monaten (Projektkosten 1.450 €)
- Abschnitt 3.3 (Nicht-monetäre Vorteile) entsprechend verknüpfen

---

### L21: Make-or-Buy – Begründung logisch schärfen

**Problem:**
Ausbilder: „Ist eine Make-or-Buy-Entscheidung nicht völlig überflüssig, weil es doch
gar nichts zu kaufen gibt für die JTL-Cloud bisher?" Die aktuelle Begründung
(„keine vergleichbare fertige Lösung") greift zu kurz – das *ist* bereits die
eigentliche Antwort und sollte so benannt werden.

**Geplante Umsetzung:**
- Argument schärfen: Es existiert derzeit kein Drittanbieterprodukt, das die
  JTL-Cloud-Beta-API unterstützt – eine Buy-Entscheidung ist damit strukturell
  ausgeschlossen, nicht nur unvorteilhaft
- Den strategischen Aspekt (Wissensaufbau für RIS als JTL-Dienstleister)
  stärker betonen

---

### L22: Projektkosten – Stundensätze fehlen Begründung

**Problem:**
Ausbilder: „es fehlt die Grundlage der Stundensätze". Die Tabelle zeigt
1.200 € für 80 h, 100 € für 4 h Fachgespräche usw. – aber nirgends steht,
welcher Stundensatz zugrunde liegt und wie er ermittelt wurde.

**Geplante Umsetzung:**
- Satz vor oder unter der Tabelle ergänzen:
  *„Den Berechnungen liegt ein interner Verrechnungssatz von 15 €/h für den
  Auszubildenden und 25 €/h für Mitarbeiter zugrunde, entsprechend den
  betrieblichen Kostensätzen von RIS."*
- Felix bestätigt die konkreten Stundensätze

---

### L23: Nutzwertanalyse – Bewertung und Gewichtung begründen

**Problem:**
Ausbilder: „how why what – das musst du dringend erklären, zumindest ein paar
Punkte, wenn nicht alle. Warum die Gewichtung, warum die Bewertung…"
Außerdem: „sollte es nicht JS sein oder mit entsprechenden Frameworks/Plattformen
vergleichen" – die Tabelle vergleicht Node.js vs. Python vs. Java, aber die
Frontend-Entscheidung für React fehlt komplett (deckt sich mit L7).

**Geplante Umsetzung:**
- Vor der Tabelle: kurzer Absatz der erklärt, wie Gewichtung und Bewertung
  zustande kamen (z. B. „Die Gewichtung (1–5) richtet sich nach der projektspezifischen
  Relevanz; eine 5 bedeutet…")
- Nach der Tabelle: pro Eigenschaft einen Halbsatz Begründung der Top-Bewertung
  von Node.js (z. B. warum React-Kompatibilität mit 5 gewichtet ist)
- Zweite Nutzwertanalyse für das Frontend (React vs. Vue vs. Angular) — L7 bleibt bestehen,
  dieser Punkt ergänzt die Begründungspflicht

---

### L24: Code-Listings zu dominant – mehr Fließtext, Code in Anhang

**Problem:**
Ausbilder (Schlussbemerkung Seite 22): „sehr viel Code-Screenshots im Dokument
und dafür sehr wenig Text. Der Code muss viel genauer erklärt werden.
Pack am besten allen Code als Anhang und erkläre, was in dem Code zu lesen wäre.
Dann prüf unbedingt, ob der Text 51 Stunden gerechtfertigt aussehen lässt."

Konkret: Die langen Listings (OAuth2, Token-Verifikation, ERP-Proxy, Setup-Page,
Pane-Widget, ErpPage) nehmen viele Seiten ein, ohne dass ausreichend erklärt
wird, was sie tun und welche Designentscheidungen darin stecken.

**Geplante Umsetzung:**
- Kurze Listings (≤ 15 Zeilen) die einen konkreten Aspekt belegen: im Text behalten,
  aber mit mehr erklärendem Fließtext davor/danach
- Lange Listings (> 15 Zeilen): in Anhang A.9 verschieben, im Fließtext nur
  referenzieren und das Wesentliche in Prosa erklären
- Pro verbliebenem Listing mind. 2 Sätze Erklärung: Was macht dieser Code?
  Welche Entscheidung steckt darin?

---

### L25: Terminologie-Konsistenz – JTL-Wawi / JTL-Cloud / ERP einheitlich

**Problem:**
Ausbilder: „Zudem solltest du deine Benennungen noch einmal ganz durchziehen.
Wenn du Sachen aus der JTL-Wawi meinst, dann muss auch JTL-Wawi da stehen –
genauso mit JTL-Cloud. Die Prüfer kennen das nicht und du kannst nicht einfach
die Begriffe wechseln."

Konkrete Problemstellen:
- „ERP-System" und „JTL-Cloud-ERP" und „JTL-Wawi" werden teils synonym verwendet
- In Kap. 4.2: „ERP-Methoden" – ist das die Wawi oder die Cloud-API?
- Architektur-Abbildung zeigt „JTL-Wawi / JTL-Cloud-ERP" – was ist der Unterschied?

**Geplante Umsetzung:**
- Klare Definitionen einmalig festlegen:
  - JTL-Wawi = die Desktop-Warenwirtschaftssoftware
  - JTL-Cloud = die browserbasierte Nachfolgeumgebung
  - JTL-Cloud-API = die Schnittstelle, über die die App Daten abruft
- Alle abweichenden Stellen im Dokument vereinheitlichen
- In Kap. 4.2 erklären, dass „ERP" hier synonym für JTL-Wawi/JTL-Cloud steht
  (da JTL ein ERP-System ist)

---

### L26: Kapitel 7 Dokumentation – Abschnitt überarbeiten

**Problem:**
Ausbilder: „brauchst du nicht nennen / es geht nur um deine angemeldete und
zeitlich geplante Doku". Der Projektbericht braucht hier nicht extra erwähnt
zu werden. Außerdem: „kein Auszug – es muss die ganze sein!!!" bezüglich
Anhang A.12 (Entwicklerdokumentation).

**Geplante Umsetzung:**
- Den Absatz über den Projektbericht (vorliegendes Dokument) kürzen oder streichen –
  Fokus nur auf die Kundendokumentation (Entwicklerdoku + Benutzerhandbuch)
- Anhang A.12: vollständige Entwicklerdokumentation einbinden, nicht nur Auszug
  (Felix muss die vollständige Entwicklerdoku liefern)

---

### L27: Lessons Learned – inhaltlich überarbeiten

**Problem:**
- „wat sind Beta-APIs auf einmal / wurde vorher nirgends erwähnt" →
  Beta-APIs wurden im Lessons-Learned-Text erwähnt, obwohl sie im Haupttext
  nicht als zentrales Thema eingeführt wurden — muss konsistenter sein
- „welche Rückmeldungen, es gab doch keine Änderungen" →
  Die Aussage über „frühzeitige Rückmeldung durch den Auftraggeber" ist
  unglaubwürdig, wenn keine konkreten Änderungen durch Feedback entstanden
- Ausbilder (Seite 22): „Die Projektdoku ist wie ein aufgeräumtes Tagebuch/Log.
  Änderungen weil JTL in der Beta sind, sind coole Punkte zu zeigen – zeige,
  dass du hier ein Projekt umsetzt mit all den Problemen die halt existieren."

**Geplante Umsetzung:**
- Lessons Learned konkret machen: Was war die eigentliche Herausforderung?
  (z. B. Beta-API-Instabilität, fehlende Dokumentation, Authentifizierungsflow
  anders als erwartet)
- Iterativer Ansatz: konkrete Beispiele nennen, was sich in Iterationen geändert hat
- Beta-APIs bereits in Kap. 1/4 einführen, damit der Verweis in Kap. 8.2 nicht
  aus dem Nichts kommt

---

---

### ~~DACH fehlt im Abkürzungsverzeichnis~~ ✅ ERLEDIGT

**Umgesetzt:** `\acrshort{dach}` in Zeile 317 durch `\gls{dach}` ersetzt —
DACH erscheint jetzt korrekt im Abkürzungsverzeichnis und wird beim ersten
Auftreten im Text ausgeschrieben.

---

## Zusammenfassung

| ID | Beschreibung | Priorität | Wer | Aufwand |
|----|---|---|---|---|
| ~~L1~~ | ~~ERP-Seite als Unterabschnitt mit Code~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L2~~ | ~~GraphQL-Integration dokumentieren~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L2a~~ | ~~Architektur korrigieren: GraphQL ist alleiniger Datenpfad, nicht REST~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L3~~ | ~~Datenmodell-Tabelle ergänzen~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L4~~ | ~~Zeitraumfilter dokumentieren~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L5~~ | ~~Testfälle-Tabelle in Kap. 6.1~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L6~~ | ~~Amortisationsberechnung konkretisieren~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L7~~ | ~~Nutzwertanalyse Frontend ergänzen~~ | ~~Hoch~~ | ✅ | ✅ |
| L8 | Iterationsplan Ist-Zeiten eintragen | Hoch | **Felix** | minimal |
| ~~L16~~ | ~~Fachbegriffe erklären (JWT, EdDSA, JWKS, OAuth2, GraphQL, AppBridge)~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L17~~ | ~~Whitespace reduzieren: TOC kompakt, Abstände normalisieren~~ | ~~Hoch~~ | ✅ | ✅ |
| L18 | Komponentendiagramm + Sequenzdiagramm als PNG exportieren und einbinden | Hoch | **Felix** + Claude | gering |
| L9 | Betreuer-Daten eintragen | Mittel | **Felix** | minimal |
| L10 | Hardware eintragen | Mittel | **Felix** | minimal |
| L11 | Soll-/Ist-Zeiten eintragen | Mittel | **Felix** | minimal |
| L12 | Use-Case-Diagramm erstellen | Mittel | **Felix** | mittel |
| L13 | UI-Mockups erstellen | Mittel | **Felix** | mittel |
| L14 | Screenshot Projektstruktur | Mittel | **Felix** | minimal |
| L15 | Screenshot Anwendung | Mittel | **Felix** | minimal |
| **— Ausbilder-Feedback —** | | | | |
| ~~L19~~ | ~~Kleine Textfixes (Bordmittel, open source, Schnittstellen, Abnahme)~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L20~~ | ~~Amortisationsdauer neu schreiben: Lizenzmodell statt Zeitersparnis~~ | ~~Kritisch~~ | ✅ | ✅ |
| ~~L21~~ | ~~Make-or-Buy schärfen: kein Kaufprodukt vorhanden = struktureller Ausschluss~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L22~~ | ~~Stundensätze in Kostenaufstellung begründen~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L23~~ | ~~Nutzwertanalyse: Gewichtung und Bewertung begründen~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L24~~ | ~~Code-Listings in Anhang, mehr Fließtext + Erklärungen~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L25~~ | ~~Terminologie JTL-Wawi / JTL-Cloud / ERP konsistent machen~~ | ~~Hoch~~ | ✅ | ✅ |
| ~~L26~~ | ~~Kap. 7 Dokumentation: Projektbericht-Absatz streichen, A.12 vollständig~~ | ~~Mittel~~ | ✅ | ✅ |
| ~~L27~~ | ~~Lessons Learned: Beta-API einführen, konkrete Herausforderungen benennen~~ | ~~Hoch~~ | ✅ | ✅ |
