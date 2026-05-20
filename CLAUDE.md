# CLAUDE.md – Projektkontext & Arbeitsregeln

## Projekt

**IHK-Abschlussprojekt 2026 – Fachinformatiker Anwendungsentwicklung**
Thema: *Erstellen einer JTL Wawi Cloud App in Form eines BI-Tools*
Prüfling: Felix Bock | Ausbildungsbetrieb: RIS Web- & Software-Development GmbH & Co. KG, Regensburg
**Abgabetermin: 25.05.2026**

Ziel ist eine vollständige, IHK-konforme Projektdokumentation in LaTeX, die den gesamten Projektverlauf der Entwicklung eines Business-Intelligence-Prototyps beschreibt.

---

## Dateien und Wissensquellen

| Datei / Pfad | Bedeutung |
|---|---|
| `Doku.tex` | Hauptdokument (gesamte Dokumentation) |
| `literatur.bib` | BibLaTeX-Literaturdatenbank |
| `Projektantrag/` | Ursprüngliche Projektantrags-Markdown-Dateien — maßgeblich für Scope und Formulierungen |
| `bilder/` | Verzeichnis für Abbildungen (noch befüllen) |
| `~/Projekte/second_brain/claude-obsidian/wiki/JTL/` | Persönliche Wiki mit technischen JTL-Notizen — als Recherchequelle nutzen |
| `~/Projekte/Projektarbeit/Freshstart/felix-projekt/` | **Quellcode der entwickelten Anwendung** — maßgeblich für alle Code-Auszüge, Architekturaussagen und konkrete Implementierungsdetails in der Doku |

### Struktur des Anwendungs-Quellcodes

```
~/Projekte/Projektarbeit/Freshstart/felix-projekt/
├── package.json              (Monorepo-Root, npm workspaces, Turbo)
├── turbo.json
└── packages/
    ├── backend/              (Node.js/Express-Backend)
    │   └── src/
    │       ├── index.ts      (Haupt-Server: API-Endpunkte, JWKS-Verifikation, ERP-Proxy)
    │       └── constants.ts  (Umgebungsvariablen)
    └── frontend/             (React-Frontend)
        └── src/
            ├── App.tsx       (Routing per URL-Pfad, AppBridge-Integration)
            ├── common/       (api.ts, constants.ts)
            ├── pages/
            │   ├── setup-page/     (Token-Verifikation, Tenant-Verbindung)
            │   ├── pane-page/      (Pane-Widget mit Event-Subscription)
            │   ├── erp-page/       (ERP-Datenanzeige)
            │   ├── graphql-demo-page/
            │   ├── hub-page/       (eigenständige Seite ohne AppBridge)
            │   └── welcome-page/
            └── mock/         (nur für lokale Entwicklung — NICHT in der Doku erwähnen)
```

**Wichtig beim Lesen des Quellcodes für die Doku:**
- `packages/backend/src/index.ts` enthält die vollständige Backend-Logik
- Implementierte API-Endpunkte: `POST /connect-tenant`, `POST /graphql`, `ALL /erp-info/:tenantId/:endpoint`
- JWKS-Endpoint: `https://api.jtl-cloud.com/account/.well-known/jwks.json`
- Auth-Endpoint (Client Credentials): `https://auth.jtl-cloud.com/oauth2/token`
- ERP-API-Basis: `https://api.jtl-cloud.com/erp/`

Kompiliert wird mit `latexmk` (pdflatex + biber + makeglossaries).

### Projektantrag als Maßstab

Der Projektantrag (in `Projektantrag/`) ist die verbindliche Grundlage. Die Dokumentation darf den dort beschriebenen Scope **nicht überschreiten**. Konkret:
- Bearbeitungszeitraum: **04.05.2026 – 22.05.2026**
- Zeitbudget: **80 Stunden**
- Ergebnis: **Prototyp** — kein produktionsreifes System
- Kein Multi-Mandant, keine Fremdanbindungen außerhalb der JTL-Cloud-API

---

## JTL-Technisches Hintergrundwissen (aus Wiki)

Die Wiki unter `~/Projekte/second_brain/claude-obsidian/wiki/JTL/` enthält dokumentierte Recherche zur JTL-Plattform. Folgende Kernpunkte sind für die Dokumentation relevant:

### JTL Cloud App — Plattformmodell

- Eine Cloud App ist eine **vom Partner gehostete** Web-Anwendung, die über die JTL App Platform in JTL-Wawi eingebettet wird.
- Die Einbettung erfolgt als **iframe** im JTL App Shell; Kommunikation zwischen Shell und App läuft ausschließlich über `window.postMessage`.
- Jeder Händler, der die App installiert, ist ein eigener **Tenant** — Datenisolation pro Tenant ist Pflicht.
- Apps werden im **JTL Developer Portal** registriert und erhalten `client_id` / `client_secret`.

### Authentifizierung

- **Authorization Code Flow mit PKCE** (RFC 7636): Standard-Flow für Nutzer-facing Apps (initiale Installation durch den Händler).
- **Client Credentials Flow**: Server-zu-Server, ohne Nutzerbeteiligung — wird im Projekt für den Backend-Zugriff auf die JTL-Cloud-ERP-API genutzt.
- Der App Shell liefert nach dem Login ein JWT-Access-Token per `shell:ready`-postMessage-Event an den iframe; die App startet keine eigene OAuth-Verhandlung im iframe.
- `client_secret` darf **niemals** im Frontend-Code erscheinen.

### Wichtiger Hinweis: Implementierung vs. Wiki-Theorie

Die Wiki beschreibt den allgemeinen JTL-Plattformstandard. Die tatsächliche Implementierung in diesem Projekt weicht an einigen Stellen ab (Beta-Phase der JTL-Cloud):

| Aspekt | Wiki-Standard | Implementierung (Doku.tex) |
|---|---|---|
| Token-Lieferung an Frontend | `shell:ready` postMessage | AppBridge (`@jtl-software/cloud-apps-core`) |
| Backend-Auth zur ERP-API | OAuth2 Auth-Code + PKCE | `client_credentials` + JWKS/EdDSA-Verifikation |
| Tenant-Mapping | Workspace-Modell im Portal | In-Memory-Map (Demo-Zwecke, Prototyp) |

Die **Doku.tex ist maßgeblich** — sie beschreibt die tatsächlich umgesetzte Implementierung. Die Wiki dient als Hintergrundkontext für präzise Formulierungen und korrekte Fachbegriffe.

### Platform API (Endpunkte für Datenabfragen)

- Basis-URL: `https://api.jtl-software.com/v1/`
- Relevante Ressourcen: Orders (`/v1/orders`), Products (`/v1/products`), Warehouses (`/v1/warehouses`), Customers (`/v1/customers`)
- Pagination: cursor-basiert (`nextCursor`, `hasMore`)
- Rate Limiting: pro `client_id` pro Tenant; Retry mit Exponential Backoff bei 429

### Scope-Begrenzung für die Dokumentation

Aus dem Projektantrag ergibt sich: Die Doku behandelt einen **Prototyp** mit begrenztem Funktionsumfang. Folgende Themen aus der Wiki gehören **nicht** in die Hauptdokumentation (ggf. nur kurz im Ausblick):
- Webhook-Integration
- Multi-Tenant-Produktivbetrieb
- Vollständige Pagination-Implementierung
- Produktreife / Extension Store-Veröffentlichung

---

## Tech-Stack des entwickelten Projekts

- **Backend:** Node.js 23 / Express 5.1 / TypeScript 6.x / jose 6.x
- **Frontend:** React 19 / TypeScript 6.x / Vite 8.x / Tailwind CSS 4.x / Recharts 3.x / graphql-request 7.x
- **Monorepo:** Turbo 2.5 (npm workspaces)
- **JTL-Pakete:** `@jtl-software/cloud-apps-core` (AppBridge/IPC), `@jtl-software/platform-ui-react`
- **Auth:** OAuth2 `client_credentials`-Flow, JWKS (`https://api.jtl-cloud.com/account/.well-known/jwks.json`), EdDSA via `jose`
- **Tests:** Vitest
- **Versionskontrolle:** Git / GitHub

---

## Kapitelstruktur der Dokumentation

```
Kapitel 1 – Einleitung
  1.1 Projektbeschreibung
  1.2 Projektziel
  1.3 Projektumfeld
  1.4 Projektbegründung
  1.5 Projektschnittstellen
  1.6 Projektabgrenzung

Kapitel 2 – Projektplanung
  2.1 Projektphasen (Tabelle: 80h-Budget)
  2.2 Ressourcenplanung
  2.3 Entwicklungsprozess (iterativ-inkrementell)

Kapitel 3 – Analysephase
  3.1 Ist-Analyse
  3.2 Wirtschaftlichkeitsanalyse
    3.2.1 Make-or-Buy-Entscheidung
    3.2.2 Projektkosten (Tabelle: 1.450 €)
    3.2.3 Amortisationsdauer
  3.3 Nicht-monetäre Vorteile
  3.4 Anwendungsfälle (→ Anhang Use-Case-Diagramm)
  3.5 Lastenheft / Fachkonzept (→ Anhang)

Kapitel 4 – Entwurfsphase
  4.1 Zielplattform (JTL Cloud App, Monorepo)
  4.2 Architekturdesign (Client-Server, MVC-Anlehnung)
  4.3 Entwurf der Benutzeroberfläche (→ Anhang Mockups)
  4.4 Datenmodell und API-Kommunikation
    4.4.1 Interne API-Endpunkte
    4.4.2 JTL AppBridge-Kommunikation
    4.4.3 OAuth2-Authentifizierungskonzept
  4.5 Pflichtenheft (→ Anhang)

Kapitel 5 – Implementierungsphase
  5.1 Iterationsplanung
  5.2 Implementierung der Projektstruktur
  5.3 Implementierung der Geschäftslogik
    5.3.1 Session-Token-Verifikation (JWKS/EdDSA)
    5.3.2 ERP-API-Proxy
  5.4 Implementierung der Benutzeroberfläche
    5.4.1 Setup-Seite mit Token-Verifikation
    5.4.2 Pane-Widget mit Event-Subscription

Kapitel 6 – Abnahme- und Einführungsphase
  6.1 Abnahme durch den Auftraggeber
  6.2 Deployment und Einführung

Kapitel 7 – Dokumentation

Kapitel 8 – Fazit
  8.1 Soll-/Ist-Vergleich  ← NOCH AUSFÜLLEN
  8.2 Lessons Learned      ← NOCH AUSFÜLLEN
  8.3 Ausblick

Anhang A
  A.1 Detaillierte Zeitplanung
  A.2 Verwendete Ressourcen  ← Hardware noch eintragen
  A.3 Use-Case-Diagramm      ← Bild noch einfügen
  A.4 Lastenheft (Auszug)
  A.5 Nutzwertanalyse (vollständig)  ← noch einfügen
  A.6 Mockups der Benutzeroberfläche ← Bilder noch einfügen
  A.7 Pflichtenheft (Auszug)         ← noch einfügen
  A.8 Iterationsplan
  A.9 Quellcode-Auszüge
  A.10 Screenshot Projektstruktur    ← noch einfügen
  A.11 Screenshot Anwendung          ← noch einfügen
  A.12 Entwicklerdokumentation (Auszug)
```

---

## Offene Platzhalter in `Doku.tex`

| Stelle | Was fehlt | Priorität |
|---|---|---|
| Deckblatt | **Prüfungsnummer** | hoch |
| Deckblatt | **Betreuer-Daten** (Name, Vorname, Telefon) | hoch |
| Fehlt ganz | **Eigenständigkeitserklärung** | hoch |
| Fehlt ganz | **IHK-Protokollierungsformular** (www.ihk-regensburg.de) | hoch |
| Kapitel 8, `tab:soll_ist` | Ist-Zeiten pro Phase | hoch |
| Kapitel 8.2 | Lessons Learned (konkreter Text) | mittel |
| Anhang A.2 | Hardware-Bezeichnung der Workstation | niedrig |
| Anhang A.3 | `bilder/usecase_diagramm.png` | mittel |
| Anhang A.5 | Vollständige Nutzwertanalyse | mittel |
| Anhang A.6 | `bilder/mockup_dashboard.png` etc. | mittel |
| Anhang A.7 | Pflichtenheft-Auszug | mittel |
| Anhang A.10 | Screenshot der IDE/Projektstruktur | mittel |
| Anhang A.11 | Screenshot des fertigen Dashboards | mittel |

---

## IHK-Normen und Standards

> **Zuständige IHK: IHK Regensburg** (Ausbildungsbetrieb RIS in Regensburg). Die unten stehenden Regensburg-Vorgaben sind maßgeblich. Münchner Vorgaben wurden ebenfalls eingepflegt wo sie ergänzend passen; bei Widerspruch gilt Regensburg.

---

### Kritische Unterscheidung: Projektbericht vs. Kundendokumentation

Diese Verwechslung kostet Punkte — beide Begriffe bezeichnen unterschiedliche Dokumente:

| | **Projektbericht** | **Kundendokumentation** |
|---|---|---|
| **Für wen** | Prüfungsausschuss | Auftraggeber (RIS / Kunde) |
| **Was** | Dokumentation des Projektverlaufs | Produktdokumentation (Benutzerhandbuch, Entwicklerdoku, …) |
| **Teil des Projekts?** | **Nein** — nicht in der Zeitplanung | **Ja** — Teil der Projektzeit |
| **Max. Zeitanteil** | — | max. 10 % der Gesamtprojektzeit = max. **8 h** bei 80 h |
| **In `Doku.tex`?** | Ja, das ist der Projektbericht | Auszug im Anhang (A.12 Entwicklerdokumentation) |

Die **5 h „Dokumentation"** in der Zeitplanung von `Doku.tex` beziehen sich auf die Kundendokumentation (Entwicklerdoku, JSDoc) — das ist korrekt und liegt unter den 8 h Limit. Der Projektbericht selbst taucht in der Zeitplanung nicht auf, da er laut IHK kein Projektbestandteil ist.

Falls das Projekt Teil eines Gesamtprojekts ist: Die Kundendokumentation darf nur den selbst erstellten Anteil abdecken. Nicht selbst erstellte Anteile müssen eindeutig kenntlich gemacht werden.

---

### Pflichtstruktur des Projektberichts (IHK Regensburg)

1. **Deckblatt** mit:
   - Titel der betrieblichen Projektarbeit ✓
   - Name, Vorname, Ausbildungsberuf ✓
   - **Prüfungsnummer** ← fehlt noch im aktuellen Deckblatt
   - **Angaben zum Ausbildungsbetrieb** ✓
   - **Betreuer-Daten** (Name, Vorname, Telefon des Projektverantwortlichen) ← fehlt noch
2. **Inhaltsverzeichnis** ✓ — zählt nicht zur Seitenzahl
3. **Beschreibung der betrieblichen Projektarbeit** (Ausgangszustand, Zielzustand, wirtschaftliche/technische/organisatorische/zeitliche Vorgaben) ✓
4. **Kundendokumentation** (Auszug oder Verweis) ← Entwicklerdoku-Auszug im Anhang A.12 vorhanden
5. **Anhang** mit praxisbezogenen Unterlagen (Quellcode-Auszüge, Diagramme usw.) ✓
6. **Protokollierung der durchgeführten Projektarbeit** ← separates Formular, Download unter www.ihk-regensburg.de — prüfen ob ausgefüllt und beigefügt

> Die IHK schreibt keine feste Kapitelstruktur mit Analyse-/Entwurfs-/Implementierungsphase vor. Die gewählte Phasengliederung in `Doku.tex` ist inhaltlich korrekt und beibehalten.

---

### Formale Anforderungen

**Seitenumfang (IHK Regensburg — maßgeblich):**
- **10–12 DIN-A4-Seiten** reiner Fließtext
- Folgendes zählt **nicht** zur Seitenzahl: Deckblatt, Inhaltsverzeichnis, Anhänge, Quellenangaben, Kundendokumentation, Bilder/Grafiken/Tabellen/Skizzen
- Das bedeutet: Anhang, Verzeichnisse und Abbildungen können großzügiger gefüllt werden, ohne das Limit zu belasten

**Vergleich IHK München (als Orientierung):**
- Max. 20 Seiten inkl. Anlagen, ohne Deckblatt und Inhaltsverzeichnis; PDF max. 4 MB; Abgabeportal schließt um 24:00 Uhr

**Format (IHK Regensburg):**
- Schriftart: Arial, 11–12 pt — aktuell lmodern 12 pt; bei Bedarf auf Arial umstellen
- Ränder: **Lochrand links 2–2,5 cm | Korrekturrand rechts 1,5 cm** | Kopf-/Fußzeile je 1,5 cm
- Kopf- und Fußzeile enthalten: **Name, Projektbezeichnung und Seitenzahl im Format „3 von 10"** (Einzelseite von Gesamtzahl)
- Zeilenabstand: mindestens einfach, höchstens 1,5-fach — aktuell 1,5-fach ✓
- Seitennummerierung ab nach dem Inhaltsverzeichnis ✓
- Format zwingend Hochformat (Zeichnungen und Grafiken ausgenommen)

**Sprache und Stil:**
- Deutsch, formeller Schreibstil
- Keine Ich-Form: *„Es wurde entschieden …"*, *„Im Rahmen des Projekts wurde …"*
- Keine Umgangssprache (z. B. „da wo"), keine Schachtelsätze, keine Worthülsen, keine Wortwiederholungen
- Auf korrekte Rechtschreibung, Grammatik und Zeichensetzung achten
- Betriebliche Abkürzungen beim ersten Auftreten ausschreiben und ins Abkürzungsverzeichnis aufnehmen (`\gls{}`)
- Bullet-Listen nur für echte Aufzählungen, nicht als Fließtextesatz

**Zitate und Quellen:**
- Jede übernommene Information mit Quellenangabe belegen — `\footcite{}` oder `\textcite{}`
- Keine unbelegten Übernahmen aus Dokumentationen, RFCs, Büchern
- Fremdmaterial im Anhang (Diagramme, Herstellerbilder) als solches kennzeichnen

**Abbildungen und Tabellen:**
- Nummeriert, mit Titel und Quellenangabe, im Text referenziert
- Zählen laut IHK Regensburg nicht zur Seitenzahl → können im Anhang großzügig verwendet werden

---

### Bewertungskriterien des Prüfungsausschusses (aus Bewertungsbogen)

Diese Kriterien stammen aus dem offiziellen Bewertungsbogen und beschreiben, was der Prüfungsausschuss konkret bewertet. Sie sind maßgeblich für den Inhalt und die Darstellung des Projektberichts.

**1.1 Gesamtgestaltung**

*Formale Gestaltung (1.1.1):*
- Deckblatt mit Name, Projektbezeichnung, Ausbildungsbetrieb, Abgabedatum, Fachrichtung und Prüfungsjahr
- Kopf- und Fußzeile mit Name, Projektbezeichnung und Seitenzahl im Format „3 von 10"
- Einheitliche Absatzformatierung (Flattersatz oder Blocksatz mit Silbentrennung)
- Einheitliche Abstände zur Überschrift und Kopfzeile
- Hochformat zwingend (Zeichnungen/Grafiken ausgenommen)
- Optik der Dokumentation muss der Bedeutung der Abschlussprüfung angemessen sein

*Sprachliche Gestaltung (1.1.2):*
- Keine Wortwiederholungen, Schachtelsätze oder Worthülsen, keine Umgangssprache
- Keine Rechtschreib-, Grammatik- und Zeichensetzungsfehler
- Betriebliche Abkürzungen müssen vorher mehrfach ausgeschrieben und im Abkürzungsverzeichnis aufgeführt sein

*Vollständigkeit (1.1.3):*
- Anhang = alle ergänzenden Unterlagen (betriebliche Doku, Formulare, Quellcode-Auszüge, Glossar usw.)
- **Länge der Dokumentation max. 20 Seiten inkl. Anhang** (ohne Deckblatt und Inhaltsverzeichnis)
- Fazit mit Überprüfung und Bewertung der Zielerreichung ist Pflicht

**1.2 Beschreibung / Konkretisierung des Auftrages**

*Verständlichkeit / Nachvollziehbarkeit (1.2.1):*
- Nennung und Begründung des Projektziels
- Grund für dieses Projekt (Motivation, Notwendigkeit des Auftrages) muss klar werden
- Darstellung des Projektumfelds (Arbeitsbereich, technische Umgebung)
- Hinweis auf den Auftraggeber (intern/extern)
- **Abweichungen gegenüber dem Projektantrag müssen benannt werden**
- Auftrag muss zu Beginn der Dokumentation aufgeführt und klar abgegrenzt sein

*Angemessene Darstellung der relevanten Einflussfaktoren (1.2.2):*
- Beschreibung der Ausgangslage
- Beschreibung der Projektschnittstellen (Personen, Abteilungen, Hard- und Software)
- Darstellung von Abhängigkeiten und Einflussfaktoren, die den Projekterfolg mitbestimmen
- Welche Ressourcen stehen zur Verfügung und wie werden sie genutzt?

**1.3 Beschreibung der Projektschritte und der Ergebnisse**

*Nachvollziehbarkeit der Projektschritte (1.3.1):*
- Überlegung der Vorgehensweise und Projektplanung muss vorhanden sein
- Einzelne Projektschritte müssen folgerichtig und nachvollziehbar dargestellt sein
- Alle erforderlichen Projektschritte vorhanden und mit Zuordnung, **wer sie ausgeführt hat**
- **Das Projekt muss ohne Verweis auf den Anhang nachvollziehbar sein** — Anhang ergänzt, ersetzt nicht

*Plausible Begründung der Projektschritte (1.3.2):*
- Jeder Projektschritt muss begründet und sein Ergebnis dargestellt sein

*Plausibilität und Darstellung des Zeitaufwandes (1.3.3):*
- Auflistung von **geplantem und tatsächlichem Zeitaufwand** für jeden Projektschritt mit detaillierter Tätigkeitsbeschreibung
- **Abweichungen zwischen geplantem und realisiertem Zeitaufwand müssen begründet werden** (Abweichungen nach oben und unten sind möglich, aber nicht unkommentiert)
- Zeitaufwand für die Erstellung der Projektdokumentation: nur die Rohdaten sind verbindlich zu erfassen, nicht Formatierung und Ausformulierung

---

### Inhaltliche IHK-Kriterien

- **Eigenleistung sichtbar:** Alle Tätigkeiten müssen als eigenständig geleistet erkennbar sein — keine reine Beschreibung, sondern Bewertung und Begründung der eigenen Entscheidungen
- **Fremdleistungen kennzeichnen:** Tätigkeiten die nicht von Felix durchgeführt wurden (Code Review durch Kollegen, Abnahme durch Betreuer) müssen in der Doku klar als Fremdleistung ausgewiesen werden
- **Fremdmaterialien kennzeichnen:** Anlagen die nicht selbst erstellt wurden mit Quellenangabe versehen und als fremd markieren
- **Begründete Entscheidungen:** Jede Technologie- oder Architekturentscheidung nachvollziehbar begründen (Nutzwertanalyse, Make-or-Buy) — bereits vorhanden
- **Wirtschaftlichkeit:** Kosten, Nutzen, Amortisation — bereits vorhanden
- **Vollständigkeit:** Alle Projektphasen dokumentiert; jeder Anhang-Verweis im Text muss tatsächlich erscheinen
- **Niveau:** Angemessen für Fachinformatiker Anwendungsentwicklung — weder zu oberflächlich noch zu wissenschaftlich

---

### Noch fehlende Pflichtbestandteile (Checkliste)

| Bestandteil | Status |
|---|---|
| Prüfungsnummer auf Deckblatt | ← fehlt |
| Betreuer-Daten (Name, Vorname, Telefon) auf Deckblatt | ← fehlt |
| Eigenständigkeitserklärung | ← fehlt |
| Protokollierung (IHK-Regensburg-Formular) | ← prüfen / beifügen |
| Soll-/Ist-Vergleich (Ist-Zeiten) | ← fehlt |
| Lessons Learned | ← fehlt |
| Screenshots der Anwendung | ← fehlen |
| Bilder: Use-Case-Diagramm, Mockups | ← fehlen |
| Hardware im Ressourcenanhang | ← fehlt |

---

### LaTeX-Konventionen in diesem Dokument

- Abkürzungen immer via `\gls{api}`, `\gls{bi}`, `\glspl{kpi}` (Plural) etc.
- Code-Listings mit `lstlisting`-Umgebung, Style `ihkcode`, immer mit `caption` und `label`
- Tabellen mit `booktabs` (`\toprule`, `\midrule`, `\bottomrule`), kein `\hline`
- Abbildungen mit `[H]` fixieren, immer `\label` und `\caption`
- Querverweise mit `\ref{}`, `\autoref{}` oder `Tabelle~\ref{}` / `Abschnitt~\ref{}`
- Literaturzitate: `\footcite{key}` für Fußnoten, `\textcite{key}` im Fließtext
- Euro-Beträge: `\EUR{1.200,00}` (eurosym-Paket)
- Leerzeichen vor Einheiten: `80\,h`, `12\,pt`
- Deutsche Anführungszeichen: `\enquote{Text}` (csquotes-Paket)
- Deckblatt und Verzeichnisse bereits vollständig — nicht anfassen ohne Rückfrage

---

## Schreibregeln für Claude

- **Sprache:** Immer Deutsch, formeller Wissenschaftsstil, keine Ich-Form
- **Kein Kommentar-Spam:** LaTeX-Kommentare nur wenn der Grund nicht offensichtlich ist
- **Keine neuen Dateien** erstellen — alles in `Doku.tex` einpflegen
- **Scope halten:** Die Doku basiert auf dem Projektantrag — nicht ausufern. Wenn etwas zu detailliert wird, in den Anhang oder weglassen.
- **Seitenbudget (IHK Regensburg):** Ziel **10–12 Seiten Fließtext**. Deckblatt, Inhaltsverzeichnis, Anhänge, Quellenangaben, Bilder und Tabellen zählen nicht dazu — also im Anhang großzügig sein, im Fließtext knapp.
- **Eigenständigkeitserklärung:** Falls noch nicht vorhanden, ergänzen — Pflichtbestandteil.
- **Prüfungsnummer und Betreuer-Daten:** Auf dem Deckblatt noch ergänzen (Felix muss die Werte liefern).
- **Fremdleistungen kennzeichnen:** Code Review, Abnahme durch Dritte → im Text explizit als Fremdleistung ausweisen.
- **Projektbericht ≠ Kundendokumentation:** Die Entwicklerdokumentation (Anhang A.12) ist die Kundendokumentation — sie gehört in den Anhang, nicht in die Zeitplanung des Projektberichts. Die 5h „Dokumentation" in der Zeitplanung sind korrekt (Kundendoku, max. 8h erlaubt).
- **Wiki als Quelle nutzen:** Für technische Hintergrundbegriffe und präzise Formulierungen die Wiki-Seiten unter `~/Projekte/second_brain/claude-obsidian/wiki/JTL/` lesen — aber nie mehr als nötig in die Doku einfließen lassen
- **Implementierung hat Vorrang:** Bei Widerspruch zwischen Wiki-Standard und tatsächlicher Implementierung in `Doku.tex` — die Implementierung ist korrekt, da Beta-Phase
- **Platzhalter sofort kennzeichnen:** Stellen, die noch Infos von Felix brauchen, mit `[EINTRAGEN: ...]` markieren — niemals erfinden
- **Konsistenz:** Bereits verwendete Formulierungen und Struktur beibehalten; keine neuen Abkürzungen einführen ohne `\newacronym` + `\gls{}`
- **Quellen:** Nur auf tatsächlich in `literatur.bib` vorhandene Keys zitieren; neue Quellen zuerst in `literatur.bib` eintragen
- **Anhang ergänzt, ersetzt nicht:** Der Haupttext muss das Projekt ohne Verweis auf den Anhang nachvollziehbar machen — Anhangverweise (`\autoref{...}`) dürfen nur vertiefende Details auslagern, nie zwingend notwendige Informationen.
- **Zeitabweichungen begründen:** Wenn Soll-/Ist-Zeiten abweichen, muss im Fließtext eine Begründung folgen — nicht nur die Zahlen nennen.
- **Abweichungen vom Projektantrag benennen:** Falls die tatsächliche Umsetzung vom Projektantrag abweicht, muss das in der Einleitung/Abgrenzung explizit dokumentiert werden.
- **Kein Mock, keine Einschränkungen — absolutes Verbot:** In der Doku darf es an keiner Stelle irgendeinen Hinweis darauf geben, dass Mock-Daten, ein Mock-Backend, Dummy-Daten oder lokale Testdaten verwendet wurden. Es darf auch nicht angedeutet werden, dass ein API-Endpunkt nicht erreichbar war, nicht funktioniert hat, nur simuliert wurde oder sich noch im Beta-Betrieb für die Verbindung befindet. Die Dokumentation beschreibt die Anwendung so, als seien alle Endpunkte vollständig funktionsfähig und gegen die echte JTL-Cloud-API erprobt worden. Das `mock/`-Verzeichnis im Frontend-Quellcode und das `mock-backend`-Paket dürfen in keiner Form in der Doku erwähnt oder abgebildet werden — weder in Struktur-Listings, noch in Beschreibungen, noch in Screenshots.
- **Vor jeder Änderung:** Relevanten Abschnitt lesen, um Kontext und Stil zu verstehen
