# Kritische Durchsicht – Doku.tex
*Perspektive: Ausbilder / erfahrener Entwickler mit technischem Vorwissen aber ohne JTL-Spezialwissen*

---

## 1. Inhaltliche Fehler und Widersprüche

### 1.1 MVC-Zuordnung ist falsch (Kap. 4.2)
**Problem:** „Das Backend übernimmt Datenverarbeitung und API-Kommunikation (Model), das Frontend die Darstellung (View) und Benutzerinteraktionen (Controller)."
Das ist falsch: Im MVC-Muster ist der Controller nicht das Frontend. Die Express-Routen im Backend sind der Controller; das Frontend ist View (+ clientseitiger Controller). Wer MVC kennt, wird das sofort bemerken.
**Fix:** Entweder die Analogie korrigieren oder den Begriff „Client-Server-Architektur" ohne MVC-Vergleich stehen lassen – er trägt alleine.

### 1.2 Liniendiagramm vs. Balkendiagramm (Kap. 4.3 und 5.4.3)
**Problem:** In Kap. 4.3 (Entwurf der Benutzeroberfläche) steht: „ein Liniendiagramm für den zeitlichen Verlauf". In Kap. 5.4.3 steht korrekt: „ein Balkendiagramm der Bibliothek Recharts". Das ist ein direkter Widerspruch zwischen Entwurf und Implementierung. Das fällt auf, weil der Prüfer beide Stellen liest.
**Fix:** In Kap. 4.3 auf „Balkendiagramm" korrigieren oder die Abweichung im Soll-/Ist-Vergleich als bewusste Entwurfsentscheidung begründen.

### 1.3 JTL-Cloud API wird als „REST-API" beschrieben, ist aber GraphQL (Kap. 1.5)
**Problem:** In Kap. 1.5 (Projektschnittstellen) steht: „JTL-Cloud API: REST-API, die einen standardisierten Datenaustausch über das HTTP-Protokoll ermöglicht, zum Abruf von Geschäftsdaten". Das ist unvollständig – die JTL-Cloud-API bietet beide Protokolle an, und das Projekt nutzt ausschließlich GraphQL für Datenabrufe. Ein Leser, der das weiß, fragt sich warum hier REST steht.
**Fix:** Formulierung ergänzen: „stellt REST- und GraphQL-Schnittstellen bereit; im Projekt wird die GraphQL-Schnittstelle genutzt."

### 1.4 GET vs. beliebige HTTP-Methoden im `/erp-info`-Endpunkt (Kap. 4.4.1 vs. 5.3.2)
**Problem:** Die Endpunkt-Tabelle in Kap. 4.4.1 zeigt `GET` als HTTP-Methode für `/erp-info/:tenantId/:endpoint`. In Kap. 5.3.2 steht aber: „Dieser nimmt beliebige HTTP-Methoden entgegen". Das ist ein direkter Widerspruch zwischen Entwurf und Implementierung.
**Fix:** In der Tabelle die Methode auf `ANY` oder `*` ändern und in der Beschreibung ergänzen, dass alle Methoden durchgereicht werden.

### 1.5 `@jtl-software/cloud-apps-core` fälschlicherweise als Backend-Paket in der NWA erwähnt (Kap. 4.1)
**Problem:** In der NWA-Kriterien-Erklärung steht: „Die verwendeten Pakete `jose`, `graphql-request` und `@jtl-software/cloud-apps-core` sind Standard-npm-Pakete, die mit jedem Node.js-Framework funktionieren." `@jtl-software/cloud-apps-core` ist jedoch ein Frontend-Paket (AppBridge für React) und wird im Backend gar nicht verwendet. Das ist sachlich falsch und wirkt wie ein Copy-Paste-Fehler.
**Fix:** `@jtl-software/cloud-apps-core` aus dieser Aufzählung entfernen; nur `jose` und `graphql-request` nennen.

---

## 2. Inkonsistenzen innerhalb des Dokuments

### 2.1 Frontend kommuniziert über „interne REST-API" (Kap. 1.5)
**Problem:** In Kap. 1.5 steht: „Frontend (React-SPA): Kommuniziert mit dem eigenen Backend über eine interne REST-API". Das ist unvollständig – die primäre Kommunikation läuft über den `/graphql`-Endpunkt, also GraphQL, nicht REST. Der REST-Endpunkt `/erp-info` wird vom Dashboard gar nicht genutzt.
**Fix:** Formulierung anpassen: „...über eine interne API (GraphQL-Endpunkt für Datenabrufe, REST-Endpunkt für Token-Verifikation)."

### 2.2 `erp-page`-Kommentar im Struktur-Listing (Kap. 5.2)
**Problem:** Das Listing zeigt `erp-page/ # ERP-Integrationsseite`, aber in der gesamten Doku wird diese Seite als „Dashboard" oder „ERP-Dashboard" bezeichnet. Der Kommentar im Listing stimmt mit dem Sprachgebrauch des Dokuments nicht überein.
**Fix:** Kommentar auf `# Dashboard-Seite` ändern.

### 2.3 Iterationsplanung enthält „Implementierung von Filtern" (Kap. 2.4)
**Problem:** Die Iterationsplanung listet „Implementierung von Filtern und Benutzerinteraktionen" als eigenen Schritt. Tatsächlich gibt es im Dashboard jedoch keine Benutzer-interaktiven Filter – `computeDateRange()` ist auf `'daily'` hardcoded. Das schafft die Erwartung einer Funktion, die nicht implementiert wurde, ohne dass das begründet wird.
**Fix:** Formulierung ändern in „Implementierung der Datenbindung und Anzeige-Logik" oder die Abweichung im Soll-/Ist-Vergleich kommentieren.

### 2.4 Zwei Authentifizierungsflüsse nicht klar voneinander getrennt
**Problem:** Das Dokument beschreibt zwei verschiedene Auth-Flows, die nie explizit als separate Konzepte eingeführt werden:
- **Flow A:** Frontend → Backend: Session-Token (JWT, via AppBridge)
- **Flow B:** Backend → JTL-Cloud-API: OAuth2 client_credentials (Access-Token)
Ein Leser ohne JTL-Kenntnis kann leicht durcheinanderkommen, welches Token wozu dient.
**Fix:** In Kap. 4.4.3 einen kurzen einleitenden Satz hinzufügen der erklärt, dass es zwei unterschiedliche Authentifizierungsmechanismen gibt – einen für die Vertrauensstellung zwischen Frontend und Backend, einen für den Backend-Zugriff auf JTL.

---

## 3. Verständlichkeitsprobleme

### 3.1 Kap. 2.3 Entwicklungsprozess zu abstrakt
**Problem:** „iterativ-inkrementeller Ansatz in Anlehnung an agile Vorgehensmodelle" erklärt nicht, was das konkret bedeutet. Was sind die Iterationen? Wie lang? Welches Kriterium entscheidet ob eine Iteration abgeschlossen ist? Für jemanden mit Scrum/Kanban-Wissen klingt das nach Buzzwords ohne Substanz.
**Fix:** Einen konkreten Satz ergänzen: z. B. „Jede Iteration entspricht einem abgeschlossenen Funktionsbereich (z. B. Authentifizierung, GraphQL-Proxy, Dashboard-Ansicht) und endet mit einem lauffähigen Zwischenstand."

### 3.2 `Tenant`-Begriff wird eingeführt aber nicht erklärt was das für den Händler bedeutet (Kap. 4.2)
**Problem:** „Jeder Händler, der die App installiert, stellt einen eigenen Tenant dar." – Technisch korrekt, aber es fehlt die Konsequenz: Was bedeutet das? Warum ist das wichtig? (Antwort: Datenisolation – jeder Händler sieht nur seine eigenen Daten.)
**Fix:** Einen Halbsatz ergänzen: „...stellt einen eigenen Tenant dar, was bedeutet, dass jeder Händler ausschließlich auf seine eigenen Geschäftsdaten zugreifen kann."

### 3.3 Warum existiert `/erp-info` wenn es nicht genutzt wird? (Kap. 5.3.2)
**Problem:** Der REST-Proxy `/erp-info` wird im Dashboard nicht genutzt (das macht GraphQL). Das wird in Kap. 5.3.2 nicht klar erklärt. Ein Prüfer fragt sich: warum wurde das implementiert, wenn GraphQL alles macht? Die Begründung „für künftige Erweiterungen" kommt, aber nicht der Kontext, dass das Dashboard gar kein REST benutzt.
**Fix:** Einen einleitenden Satz ergänzen: „Parallel zum GraphQL-Proxy wurde ein generischer REST-Proxy implementiert. Dieser wird vom Dashboard selbst nicht genutzt, steht aber für künftige Erweiterungen bereit."

### 3.4 `Promise.all` – Erklärung setzt JavaScript-Kenntnisse voraus
**Problem:** Das Code-Listing zeigt `Promise.all`, und der Text erklärt gut warum (parallele Ausführung). Aber `Promise` selbst wird nie erklärt. Ein Prüfer ohne JS-Hintergrund versteht den Code-Kommentar möglicherweise nicht.
**Fix:** Einleitenden Halbsatz ergänzen: „`Promise.all` ist ein JavaScript-Mechanismus, der mehrere asynchrone Vorgänge gleichzeitig startet und wartet bis alle abgeschlossen sind."

---

## 4. Methodische Schwächen

### 4.1 NWA-Tabelle: Spaltenkopf „Gew." ist doppeldeutig
**Problem:** Die NWA-Tabelle hat die Spalten: `Kriterium | Gew. | Bew. | Gew. | Bew. | Gew. | Bew. | Gew.`. Die letzte Spalte pro Framework heißt „Gew." meint aber den **gewichteten Punktwert** (Gew × Bew), nicht die Gewichtung. Das ist verwirrend – „Gew." bedeutet in dieser Tabelle zwei verschiedene Dinge.
**Fix:** Die Spalte für den gewichteten Punktwert umbenennen in „Pkt." oder „W. Pkt." (Gewichtete Punkte).

### 4.2 Make-or-Buy: Logische Schwäche
**Problem:** „da kein Kaufprodukt existiert, ist Make-or-Buy strukturell ausgeschlossen" – das ist argumentativ schwierig, weil Make-or-Buy genau für diesen Fall gedacht ist (Prüfung ob etwas gebaut werden muss). Wenn die Antwort immer „Make" ist weil nichts existiert, braucht man die Analyse nicht. Ein kritischer Prüfer könnte sagen: „Dann hätten Sie das Projekt auch nicht machen müssen."
**Fix:** Die Begründung schärfen: nicht nur „kein Produkt vorhanden", sondern auch erklären dass RIS als JTL-Dienstleister bewusst Eigenentwicklungskompetenz aufbauen will und ein Zukauf diese strategische Zielsetzung nicht erfüllen würde.

### 4.3 Amortisationsrechnung: Lizenzpreis ohne Marktbezug
**Problem:** „29 €/Monat pro Mandant" wirkt willkürlich. Warum 29 €? Woher kommt die Zahl? Ein Prüfer fragt sich, ob das realistisch ist. Ohne Vergleich mit ähnlichen SaaS-Produkten oder Erklärung der Herleitung steht die Zahl im Raum.
**Fix:** Einen Halbsatz ergänzen der erklärt, wie die 29 € hergeleitet wurden: z. B. Orientierung an vergleichbaren JTL-Erweiterungen oder internem Preisrahmen von RIS.

---

## 5. Kleinere Verbesserungen

### 5.1 Kap. 8.1: Soll-/Ist-Tabelle ohne Fließtext
**Problem:** Die Tabelle steht allein ohne einleitenden oder abschließenden Kommentar zu den Abweichungen. IHK-Pflicht: Abweichungen müssen begründet werden. Selbst wenn alle Ist-Werte gleich den Soll-Werten sind, braucht es einen Satz dazu.
**Fix:** Nach der Tabelle einen kurzen Absatz ergänzen (sobald Ist-Zeiten vorliegen): z. B. warum die Authentifizierungsphase länger gedauert hat.

### 5.2 Kap. 1 – erster Satz zu förmlich und redundant
**Problem:** „Die Projektdokumentation schildert den Ablauf des IHK-Abschlussprojektes, welches im Rahmen der Ausbildung..." – das ist eine Aussage die der Prüfer selbst bereits weiß, da er das Dokument vor sich hat.
**Fix:** Den Satz auf das Wesentliche kürzen oder direkt mit der Projektbeschreibung einsteigen.

### 5.3 Kap. 6.3 Deployment – sehr dünn
**Problem:** „lokale Entwicklungsumgebung" als Deployment-Beschreibung ist sehr kurz. Es fehlt: Wie wird die App gestartet? Welche Voraussetzungen? Welche Umgebungsvariablen werden benötigt? Selbst für einen Prototyp erwartet der Prüfer zumindest eine stichwortartige Anleitung.
**Fix:** 2–3 Sätze ergänzen: z. B. `npm run dev` startet Frontend und Backend parallel via Turbo, Voraussetzung sind `.env`-Variablen mit `CLIENT_ID` und `CLIENT_SECRET` aus dem JTL Developer Portal.

### 5.4 Zwei Authentifizierungsflüsse im Sequenzdiagramm nicht beschriftet
**Problem:** Das Sequenzdiagramm (image2.png) zeigt den Token-Verifikationsablauf. Im Fließtext wird es mit „zeigt den vollständigen Ablauf vom Session-Token-Empfang bis zur verifizierten Tenant-Zuordnung" beschrieben. Ob das Diagramm auch den OAuth2-Flow Richtung JTL-Cloud zeigt ist unklar – der Text verweist nur auf eine Richtung.
**Fix:** Die Caption des Diagramms präzisieren: entweder was genau gezeigt wird, oder im Text klar stellen welcher Flow im Diagramm fehlt.

---

## Zusammenfassung nach Priorität

| Priorität | Punkt | Aufwand |
|---|---|---|
| 🔴 Hoch | 1.1 MVC-Zuordnung korrigieren | gering |
| 🔴 Hoch | 1.2 Liniendiagramm → Balkendiagramm in Kap. 4.3 | minimal |
| 🔴 Hoch | 1.4 GET vs. ANY im `/erp-info`-Endpunkt | minimal |
| 🔴 Hoch | 1.5 `@jtl-software/cloud-apps-core` aus NWA-Erklärung entfernen | minimal |
| 🟡 Mittel | 1.3 JTL-Cloud API als REST+GraphQL beschreiben (Kap. 1.5) | minimal |
| 🟡 Mittel | 2.3 Filter-Iteration begründen oder umbenennen | minimal |
| 🟡 Mittel | 2.4 Zwei Auth-Flows explizit trennen | gering |
| 🟡 Mittel | 3.3 REST-Proxy: Klarstellung dass Dashboard ihn nicht nutzt | minimal |
| 🟡 Mittel | 4.1 NWA-Spalte umbenennen (Gew. → Pkt.) | minimal |
| 🟡 Mittel | 4.3 Amortisation: Herleitung der 29 € ergänzen | minimal |
| 🟢 Gering | 2.2 Listing-Kommentar `erp-page` → Dashboard | minimal |
| 🟢 Gering | 3.1 Entwicklungsprozess konkreter beschreiben | minimal |
| 🟢 Gering | 3.4 Promise erklären | minimal |
| 🟢 Gering | 5.3 Deployment-Abschnitt ausbauen | gering |
