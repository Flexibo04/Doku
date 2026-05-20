# KI-Ton-Review – Doku.tex (v2)

Ziel: Unterscheiden zwischen echten KI-Floskeln (ändern) und korrekter technischer
Fachsprache (behalten). Der Text soll formal und präzise bleiben — nur die generischen,
inhaltsleeren oder übermäßig polierten Stellen sollen natürlicher klingen.

---

## Was NICHT geändert werden muss (korrekte Fachsprache)

Diese Stellen klingen fachlich und korrekt — kein KI-Alarm:

- Gesamte NWA-Kriterien-Begründungen (Kap. 4.1) — konkretes technisches Reasoning
- Kap. 5.3 Session-Token-Verifikation, GraphQL-Proxy, OAuth2-Flow — präzise und konkret
- Kap. 5.4 AppBridge-Beschreibung, useEffect-Erklärung — spezifisch genug
- Kap. 6.1 Testfalltabelle und Code-Review-Beschreibung — passt
- Kap. 4.4.1 GraphQL-Begründung (ein Endpunkt statt fünf, kein Over-Fetching) — gut

---

## Stellen die geändert werden sollten

---

### 1 – Kap. 1, Zeile 316 — Inhaltsleerer Eröffnungssatz

**Problem:** Der Satz sagt exakt das, was auf dem Deckblatt steht. Er fügt keinen Inhalt hinzu.

```
Die Projektdokumentation schildert den Ablauf des IHK-Abschlussprojektes, welches
im Rahmen der Ausbildung zum Fachinformatiker Anwendungsentwicklung durchgeführt wurde.
```

**Fix:** Satz ersatzlos streichen. Der nächste Satz ist der eigentliche Einstieg.

---

### 2 – Kap. 1.2, Zeile 350 — Marketingphrase ohne Inhalt

**Problem:** „strukturiert aufbereitet und in einer benutzerfreundlichen Oberfläche grafisch dargestellt" — diese Kombination aus drei Adjektiven/Adverbien beschreibt jede beliebige Anwendung. Keine projektspezifische Information.

```
Die Daten werden strukturiert aufbereitet und in einer benutzerfreundlichen Oberfläche
grafisch dargestellt.
```

**Fix:**
```
Die abgerufenen Kennzahlen werden als KPI-Karten und Balkendiagramm im Dashboard dargestellt.
```

---

### 3 – Kap. 1.3, Zeile 363 — Managementvokabular

**Problem:** „strategisches Interesse", „Beratungskompetenz auszubauen" — das ist Business-Jargon, der wie aus einem Unternehmensberatungs-Template kopiert klingt.

```
Darüber hinaus hat \gls{ris} ein strategisches Interesse an einer frühen technischen
Evaluierung der neuen JTL-Cloud-\gls{api}, um die eigene Beratungskompetenz für
cloudbasierte JTL-Erweiterungen auszubauen.
```

**Fix:**
```
Darüber hinaus hat \gls{ris} ein Interesse daran, die neue JTL-Cloud-\gls{api} frühzeitig
zu erproben, um JTL-Kunden künftig auch bei Cloud-Erweiterungen technisch begleiten zu können.
```

---

### 4 – Kap. 1.4, Zeile 375 — „strategische Notwendigkeit"

**Problem:** „strategische Notwendigkeit" ist ein inhaltsleerer Managementbegriff. Der Satz sagt im Kern nur: RIS will die API früh kennenlernen. Das ist konkret genug ohne das Buzzword.

```
Für \gls{ris} als JTL-Dienstleister besteht zusätzlich die strategische Notwendigkeit,
die neue Cloud-\gls{api} frühzeitig technisch zu evaluieren und deren Praxistauglichkeit
zu prüfen.
```

**Fix:**
```
Für \gls{ris} als JTL-Dienstleister kam außerdem hinzu, dass die neue Cloud-\gls{api}
zum Projektzeitpunkt noch wenig erprobt war und praktische Erfahrung damit einen
technischen Wissensvorsprung gegenüber anderen Anbietern bedeutet.
```

---

### 5 – Kap. 2.2, Zeilen 444–447 — Padding-Absatz über Open Source

**Problem:** Dieser Einschub erklärt, dass Open-Source nicht immer kostenlos ist — eine Information, die für die Dokumentation irrelevant ist und wie aufgefüllter Text wirkt.

```
Bei der Auswahl der verwendeten Software wurde auf lizenzkostenfreie Werkzeuge
gesetzt. Open-Source-Software ist zwar quelloffen zugänglich und verursacht keine
Lizenzkosten, ist aber nicht grundsätzlich für alle Nutzungsszenarien kostenlos.
Dadurch sollen anfallende Projektkosten möglichst gering gehalten werden.
```

**Fix:**
```
Bei der Softwareauswahl wurde auf lizenzfreie, quelloffene Werkzeuge gesetzt,
um keine zusätzlichen Lizenzkosten zu verursachen.
```

---

### 6 – Kap. 2.3, Zeilen 451–455 — Entwicklungsprozess zu abstrakt

**Problem:** „in Anlehnung an agile Vorgehensmodelle" ist eine Floskel ohne Substanz. Was bedeutet das konkret für dieses Projekt? (Auch in review.md 3.1 erfasst.)

```
Bevor mit der Realisierung des Projekts begonnen werden konnte, musste ein geeigneter
Entwicklungsprozess gewählt werden. Für das Abschlussprojekt wurde ein iterativ-inkrementeller
Ansatz in Anlehnung an agile Vorgehensmodelle gewählt. Die einzelnen Implementierungsphasen
(Backend, Frontend, Qualitätssicherung) werden nacheinander, aber mit regelmäßiger Überprüfung
der Zwischenergebnisse, durchlaufen.
```

**Fix:**
```
Für die Umsetzung wurde ein iterativ-inkrementeller Ansatz gewählt: Die Implementierung
wurde in abgeschlossene Teilbereiche aufgeteilt — zunächst die Backend-Infrastruktur
(Authentifizierung, Proxy-Routen), dann die Frontend-Komponenten, abschließend
Qualitätssicherung. Nach jedem Abschnitt wurde der Zwischenstand gegen die
JTL-Cloud-\gls{api} getestet und bei Bedarf korrigiert, bevor der nächste Bereich
begonnen wurde.
```

---

### 7 – Kap. 3.3, Zeile 537 — Standardformel aus dem Lehrbuch

**Problem:** „Neben der wirtschaftlichen Rechtfertigung ergeben sich folgende nicht-monetäre Vorteile" steht so oder ähnlich in jedem Schüler-Wirtschaftsaufsatz. Keine eigene Formulierung.

```
Neben der wirtschaftlichen Rechtfertigung ergeben sich folgende nicht-monetäre Vorteile:
```

**Fix:**
```
Das Projekt bringt über die direkten Lizenzeinnahmen hinaus weitere Vorteile für \gls{ris}:
```

---

### 8 – Kap. 4.1, Zeile 569 — „nahtlos integriert"

**Problem:** „nahtlos" ist ein Werbewort ohne technischen Inhalt.

```
Die Applikation wird als JTL Cloud App entwickelt, die sich nahtlos in die
JTL-Cloud-Plattform integriert.
```

**Fix:**
```
Die Applikation wird als JTL Cloud App entwickelt und über die JTL-Cloud-Plattform
in die JTL-Wawi eingebettet.
```

---

### 9 – Kap. 4.1, Zeilen 597–603 — React-Begründung mit gestapelten Superlativen

**Problem:** „erstklassige TypeScript-Unterstützung", „fügt sich React inhaltlich gut in das Projekt ein", „begünstigte die weite Verbreitung" — drei Sätze mit jeweils einem leeren Qualifikator. Jeder Satz klingt wie Werbung.

```
Darüber hinaus fügt sich React inhaltlich gut in das Projekt ein: Die verwendete
Diagrammbibliothek Recharts ist eine React-native Bibliothek und ist unmittelbar auf
Dashboard-Anwendungsfälle ausgelegt. React bietet erstklassige TypeScript-Unterstützung,
was angesichts des durchgehenden TypeScript-Einsatzes im gesamten Monorepo die
Konsistenz erhöht. Schließlich begünstigte die weite Verbreitung von React den engen
Zeitrahmen von 80\,h, da auf eine umfangreiche Dokumentation und erprobte Lösungsmuster
zurückgegriffen werden konnte.
```

**Fix:** Gleiche Argumente, konkret statt superlativ:
```
Für React sprachen außerdem praktische Gründe: Die Diagrammbibliothek Recharts ist
React-spezifisch und lässt sich direkt als Komponente einbinden. Da im gesamten Monorepo
TypeScript verwendet wird, kamen die TypeScript-Typen von React dem Projekt
zugute. Der knappe Zeitrahmen von 80\,h wurde durch die große Verbreitung von React
erleichtert — bei technischen Problemen ließen sich schnell Lösungen in der Dokumentation
und in Community-Ressourcen finden.
```

---

### 10 – Kap. 4.2, Zeilen 678–680 — „bewährte Architekturprinzipien"

**Problem:** „bewährte Architekturprinzipien" ist eine inhaltsleere Selbstverständlichkeit. Die eigentliche Information (Backend und Frontend können unabhängig geändert werden) steckt im zweiten Halbsatz.

```
Die Trennung von Datenzugriff und Darstellung orientiert sich an bewährten
Architekturprinzipien und erleichtert die unabhängige Weiterentwicklung beider Schichten.
```

**Fix:**
```
Diese Trennung ermöglicht es, Backend und Frontend unabhängig voneinander weiterzuentwickeln —
Änderungen an der Datenbeschaffungslogik wirken sich nicht auf die Darstellungsschicht aus
und umgekehrt.
```

---

### 11 – Kap. 5.4.3, Zeile 1040 — „Dashboard-Seite bildet das zentrale Dashboard"

**Problem:** Das Wort „Dashboard" zweimal in einem Satz. Der Satz sagt zudem inhaltlich nichts über die Implementierung aus.

```
Die Dashboard-Seite bildet das zentrale Dashboard der Anwendung und stellt die
wichtigsten Geschäftskennzahlen des jeweiligen JTL-Wawi-Mandanten dar.
```

**Fix:**
```
Die ERP-Dashboard-Seite ist die Kernkomponente der Anwendung. Sie zeigt die aktuellen
Kennzahlen des verbundenen JTL-Wawi-Mandanten in einer kompakten Übersicht.
```

---

### 12 – Kap. 8.1, Zeile 1176 — Formulierung als KI-Eröffnung

**Problem:** „Bei einer rückblickenden Betrachtung des Projekts kann festgehalten werden" — diese Formulierung ist eine der häufigsten KI-Einleitungen überhaupt. Kein echter Azubi schreibt das freiwillig.

```
Bei einer rückblickenden Betrachtung des Projekts kann festgehalten werden, dass alle
zuvor festgelegten Anforderungen gemäß dem Pflichtenheft erfüllt wurden.
```

**Fix:**
```
Alle im Pflichtenheft definierten Anforderungen konnten innerhalb des geplanten
Zeitrahmens umgesetzt werden.
```

---

### 13 – Kap. 8.2, Zeile 1214 — „Ein weiterer Erkenntnisgewinn betrifft"

**Problem:** „Erkenntnisgewinn" ist ein typisches KI-Substantiv. Klingt wie aus einem philosophischen Essay.

```
Ein weiterer Erkenntnisgewinn betrifft den Wechsel von REST zu GraphQL als
primärem Datenabrufmechanismus.
```

**Fix:**
```
Rückblickend war auch die Entscheidung, von REST auf GraphQL umzustellen, ein wichtiger Schritt.
```

---

### 14 – Kap. 8.2, Zeile 1222 — „bewährte sich in diesem Kontext"

**Problem:** „bewährte sich in diesem Kontext" ist eine sehr generische Schlussformel, die auf jede Methode in jedem Projekt passt.

```
Der iterativ-inkrementelle Entwicklungsansatz bewährte sich in diesem Kontext:
```

**Fix:**
```
Der iterativ-inkrementelle Ansatz hat sich für dieses Projekt als richtig erwiesen:
```

---

### 15 – Kap. 8.3, Zeilen 1238–1240 — Abschlusssatz zu glatt

**Problem:** Drei Substantivkonstruktionen in einem Satz, perfekt ausbalanciert — typisches KI-Muster. Der Inhalt ist korrekt, aber die Form verrät den Generator.

```
Aufgrund des modularen Aufbaus der Anwendung, der klaren Trennung von Backend und
Frontend sowie der einheitlichen internen \gls{api}, können solche Erweiterungen
mit überschaubarem Aufwand vorgenommen werden.
```

**Fix:**
```
Da Backend und Frontend klar getrennt sind und die interne \gls{api} einheitlich
strukturiert ist, lassen sich diese Erweiterungen ohne größere Umbauten realisieren.
```

---

## Zusammenfassung

| # | Stelle | KI-Muster | Aufwand |
|---|---|---|---|
| 1 | Kap. 1, Zeile 316 | Inhaltsleerer Eröffnungssatz | Satz löschen |
| 2 | Kap. 1.2, Zeile 350 | Generische Adjektiv-Kette | minimal |
| 3 | Kap. 1.3, Zeile 363 | Managementvokabular | minimal |
| 4 | Kap. 1.4, Zeile 375 | „strategische Notwendigkeit" | minimal |
| 5 | Kap. 2.2, Zeilen 444–447 | Padding-Absatz | Absatz kürzen |
| 6 | Kap. 2.3, Zeilen 451–455 | Buzzwords ohne Substanz | 3 Sätze umschreiben |
| 7 | Kap. 3.3, Zeile 537 | Standardformulierung | minimal |
| 8 | Kap. 4.1, Zeile 569 | „nahtlos" als Werbewort | minimal |
| 9 | Kap. 4.1, Zeilen 597–603 | Gestapelte Superlative | 3 Sätze umschreiben |
| 10 | Kap. 4.2, Zeilen 678–680 | „bewährte Architekturprinzipien" | minimal |
| 11 | Kap. 5.4.3, Zeile 1040 | Redundant + nichtssagend | minimal |
| 12 | Kap. 8.1, Zeile 1176 | Häufigste KI-Eröffnungsfloskel | minimal |
| 13 | Kap. 8.2, Zeile 1214 | „Erkenntnisgewinn" | minimal |
| 14 | Kap. 8.2, Zeile 1222 | „bewährte sich in diesem Kontext" | minimal |
| 15 | Kap. 8.3, Zeilen 1238–1240 | Perfekte Drei-Nomen-Struktur | minimal |
