# Mail-Cockpit für Versicherungsagenturen – Spezifikation

Version 0.1 (Entwurf zur Abstimmung) · Stand 23.11.2026

Mockups: siehe [`docs/MOCKUPS.md`](MOCKUPS.md) (klickbarer Entwurf, 5 Ansichten).

---

## 1. Ziel in einem Satz

Ein Dashboard, das das Postfach eines Versicherungsvertrieblers automatisch liest, zusammengehörige Mails zu **Vorgängen** bündelt, sie **kategorisiert** und nach **Dringlichkeit** sortiert, sodass der Vertriebler jeden Morgen in wenigen Minuten weiß, was heute zu tun ist, und alles Weitere mit wenigen Klicks direkt aus dem Dashboard erledigt.

## 2. Ausgangslage und Problem

| Beobachtung | Folge heute |
|---|---|
| 80–200 Mails pro Tag aus vielen Quellen: Kunden, Versicherer, Gutachter, Werkstätten, Banken, Newsletter | Wichtiges geht zwischen Routine-Post unter |
| Ein Schadenfall erzeugt über Tage 3–10 Mails von verschiedenen Absendern (Erstmeldung, Fotos, Schadennummer, Gutachten, Rechnung, Nachfrage) | Infos müssen aus mehreren Mails zusammengesucht werden; der aktuelle Stand ist nirgends auf einen Blick sichtbar |
| Rückrufbitten stehen irgendwo im Fließtext („bitte melden Sie sich heute ab 17 Uhr“) | Rückrufe werden vergessen oder zu spät erledigt |
| Fristen (Mahnstufen, Kündigungstermine, Kostenzusagen, Fahrzeugwechsel) sind in Mails versteckt | Gefahr von Deckungslücken und Kundenverlust |
| Vertriebler ist oft unterwegs | Er braucht eine kompakte mobile Sicht „Was brennt?“ |

## 3. Nutzer

**Primär: Vertriebler / Agenturinhaber** (Mailboxinhaber). Wenig Zeit, nicht technikaffin, arbeitet am Laptop im Büro und am Smartphone unterwegs. Will keine Konfiguration, sondern eine sortierte Arbeitsliste.

**Sekundär (Phase 2): Innendienst / Assistenz**, der Vorgänge vorbereitet, Unterlagen anfordert und Rückrufe terminiert.

## 4. Grundprinzipien

1. **Das Dashboard ist die Steuerzentrale.** Jede tägliche Aktion (öffnen, beantworten, weiterleiten, Rückruf erledigen, Wiedervorlage setzen, Zuordnung korrigieren, abschließen) ist von dort in maximal zwei Klicks erreichbar. Outlook muss für die Routinearbeit nicht mehr geöffnet werden.
2. **Vorgang statt Mail.** Die zentrale Einheit ist der Vorgang (z. B. „Auffahrunfall A7 – Kowalski“), nicht die einzelne Mail. Mails sind Belege in der Zeitleiste eines Vorgangs.
3. **Erklärbare Priorität.** Jede Priorität zeigt ihre Gründe („Frist heute“, „Kunde fragt zum 2. Mal nach“). Keine Blackbox.
4. **Mensch entscheidet.** Die App schlägt vor (Zuordnung, Kategorie, Antwortentwurf), versendet aber nichts selbstständig. Unsichere Zuordnungen landen in „Zuordnung prüfen“.
5. **Einfach.** Deutsche Fachsprache der Branche, große Klickflächen, Farbe immer zusammen mit Text (Barrierefreiheit), keine Pflichtkonfiguration vor dem ersten Nutzen.

## 5. Funktionsumfang

### 5.1 Postfach-Anbindung
- Microsoft 365 / Exchange Online über Microsoft Graph (OAuth, nur Lesen + Entwürfe anlegen). Alternativ IMAP/SMTP für andere Anbieter.
- Erstimport der letzten 30 Tage, danach laufende Synchronisation (Push/Delta, max. 2 Minuten Verzögerung).
- Anhänge werden erfasst (Typ, Name, Größe); PDF- und Bildtexte werden für die Analyse ausgelesen (OCR bei Scans).
- Gesendete Mails des Nutzers werden mitgelesen, damit Vorgänge wissen, ob bereits geantwortet wurde.

### 5.2 Analyse je Mail
Für jede eingehende Mail ermittelt eine KI-Pipeline (LLM mit festem Ausgabeschema):

| Feld | Beispiele |
|---|---|
| **Kategorie** (genau eine Haupt-, optional Nebenkategorien) | siehe 5.3 |
| **Sparte** | Kfz, Hausrat, Wohngebäude, Haftpflicht, Rechtsschutz, BU, Leben, Kranken, Gewerbe/Inhalt, Sonstige |
| **Absenderrolle** | Kunde, Versicherer, Gutachter, Werkstatt/Handwerker, Anwalt, Bank, intern, Newsletter |
| **Extrahierte Fakten** | Kundenname, Vertragsnummer, Schadennummer, Kennzeichen, Schadentag/-ort, Beträge, Telefonnummer, IBAN-Änderung, Termine, Fristen |
| **Rückrufwunsch** | ja/nein, Wunschzeitfenster, Telefonnummer |
| **Handlungsbedarf** | „Antwort nötig“, „Weiterleiten an Versicherer“, „Unterlagen fehlen“, „Nur Info“ |
| **Stimmung/Eskalationssignale** | Verärgerung, Kündigungsandrohung, Anwalt, Beschwerde, wiederholte Nachfrage |
| **Kurzfassung** | 1–2 Sätze in Deutsch |

Jeder extrahierte Fakt speichert seine **Quelle** (Mail + Textstelle), damit er im Vorgang belegt angezeigt werden kann.

### 5.3 Kategorien (Standardsatz, erweiterbar)

| Kategorie | Typische Auslöser |
|---|---|
| Schadensmeldung (neu) | Erstmeldung eines Schadens |
| Schaden-Nachtrag | Fotos, Gutachten, Rechnungen, Schadennummer, Rückfragen zum laufenden Schaden |
| Rückrufbitte | „Bitte rufen Sie mich an“, verpasster Anruf |
| Angebot / Neugeschäft | Anfrage nach Tarif, Vergleich, Beratung |
| Vertragsänderung | Adresse, Fahrzeugwechsel, Bankverbindung, Summenanpassung |
| Kündigung | Kündigung durch Kunden oder Versicherer |
| Beschwerde | Unzufriedenheit, Eskalation |
| Beitrag / Mahnung | Lastschriftrückgabe, Mahnstufen, Beitragsanpassung |
| Versicherer-Post | Policen, Nachträge, Provisionsabrechnung, Rundschreiben |
| Termin | Terminvorschläge, -bestätigungen |
| Info / Newsletter | ohne Handlungsbedarf |
| Spam / Sonstiges | |

### 5.4 Vorgangsbildung (Kernfunktion)
Neue Mails werden einem bestehenden Vorgang zugeordnet oder eröffnen einen neuen. Signale, absteigend nach Gewicht:

1. Gleiche **Schadennummer** oder **Vertragsnummer** (stark)
2. Gleicher Mail-**Thread** (In-Reply-To / References)
3. Gleiches **Kennzeichen**, gleiche Adresse, gleicher Kunde + gleiche Sparte in einem Zeitfenster von 60 Tagen
4. Absender ist bekannter Kontakt des Kunden (auch neue Adresse mit Name in der Signatur)
5. Semantische Ähnlichkeit des Inhalts (Embedding) mit offenen Vorgängen

Ergebnis ist eine **Zuordnungssicherheit** (0–100 %):
- **≥ 85 %**: automatisch zugeordnet, im Vorgang als „automatisch“ markiert, jederzeit korrigierbar.
- **50–84 %**: Vorschlag in der Liste **„Zuordnung prüfen“** mit Begründung und Alternativen.
- **< 50 %**: neuer Vorgang wird vorgeschlagen.

Korrekturen des Nutzers (zuordnen, trennen, zusammenführen) werden gespeichert und verbessern künftige Vorschläge (z. B. „diese Adresse gehört zu Kunde X“).

### 5.5 Konsolidierte Fakten je Vorgang
Pro Vorgang entsteht eine **Faktenkarte**, die alle extrahierten Daten zusammenführt (jeweils der neueste Wert gewinnt, Konflikte werden markiert). Dazu kommt eine **Unterlagen-Checkliste** je Vorgangstyp (z. B. Kfz-Schaden: Schadenanzeige, Fotos, Polizeibericht, Gutachten/KVA, Rechnung) mit Status vorhanden / fehlt / angefordert.

### 5.6 Priorisierung
Jeder Vorgang erhält einen **Score von 0 bis 100**, daraus vier Stufen:

| Stufe | Score | Bedeutung |
|---|---|---|
| Kritisch | 85–100 | heute erledigen |
| Hoch | 65–84 | in den nächsten 1–2 Tagen |
| Normal | 35–64 | diese Woche |
| Niedrig | 0–34 | bei Gelegenheit / nur Info |

Bausteine (Startwerte, im Bereich „Regeln“ in Klartext einsehbar und per Schieberegler anpassbar):

| Faktor | Punkte |
|---|---|
| Basis nach Kategorie (Schaden neu 45, Beschwerde 45, Kündigung 40, Rückruf 35, Mahnung 35, Vertragsänderung 25, Angebot 25, Versicherer-Post 10, Info 0) | 0–45 |
| Frist heute / morgen / ≤ 7 Tage | +30 / +20 / +10 |
| Eskalationssignal (Kündigungsandrohung, Anwalt, Verärgerung) | +20 |
| Wiederholte Nachfrage des Kunden | +10 je Nachfrage, max. +20 |
| Wartet auf Antwort seit > 24 h / > 72 h | +5 / +15 |
| Kundenwert (Bestandsbeitrag, Anzahl Verträge) | +0 bis +10 |
| Deckung gefährdet (2. Mahnstufe, Lücke bei Fahrzeugwechsel) | +15 |
| Nutzer hat „Wiedervorlage“ in der Zukunft gesetzt | Vorgang ruht bis dahin |

Die Gründe werden als Chips am Vorgang angezeigt („Warum kritisch?“). Der Nutzer kann die Priorität manuell übersteuern (Pin).

### 5.7 Dashboard (Steuerzentrale) – siehe Mockup 1
- **Kopfzeile**: Begrüßung mit Tageszusammenfassung als Satz, globale Suche (Kunde, Vertrags-, Schaden-, Kennzeichen), Sync-Status.
- **Kennzahlen**: Kritisch · Rückrufe heute · Neue Schäden · Fristen ≤ 7 Tage · Zuordnung prüfen. Jede Kachel ist zugleich ein Filter.
- **Prioritäten-Liste** der offenen Vorgänge: Stufe + Score, Titel, Kunde, Kategorie, Anzahl Mails, „Neu seit gestern“, nächster Schritt, Frist. Schnellaktionen je Zeile: Öffnen, Erledigt, Wiedervorlage, Rückruf.
- **Filter**: Kategorie, Sparte, Stufe, „nur mit Frist“, „wartet auf mich“ / „wartet auf Versicherer“.
- **Seitenleiste rechts**: Rückrufe heute (mit Telefonnummer und Anlass), anstehende Fristen, KI-Briefing („Seit gestern 18 Uhr: 47 neue Mails …“).
- Tastatur: `J/K` navigieren, `E` erledigt, `W` Wiedervorlage, `R` antworten, `/` Suche.

### 5.8 Vorgangsansicht – siehe Mockup 2
- Kopf mit Titel, Kunde, Sparte, Status, Priorität mit Gründen.
- Aktionsleiste: Antwort entwerfen · An Versicherer weiterleiten · Rückruf planen · Wiedervorlage · Erledigt · Zusammenführen/Trennen.
- Faktenkarte mit Quellenverweis je Wert, Unterlagen-Checkliste.
- Zeitleiste aller Mails und Ereignisse (eigene Antworten, Statuswechsel), je Mail KI-Kurzfassung, Original aufklappbar, Anhänge.
- KI-Antwortentwurf (Kunde oder Versicherer), editierbar, wird als **Entwurf im Postfach** abgelegt bzw. nach Klick auf „Senden“ verschickt.

### 5.9 Zuordnung prüfen – siehe Mockup 3
Liste der unsicher zugeordneten Mails mit Vorschlag, Sicherheit, Begründung und Alternativen. Ein Klick: übernehmen, anderen Vorgang wählen, neuen Vorgang anlegen, Absenderadresse dauerhaft einem Kunden zuordnen.

### 5.10 Rückrufe & Wiedervorlagen – siehe Mockup 4
Spalten Überfällig · Heute · Morgen · Diese Woche. Jede Karte: Name, Telefonnummer, Wunschzeit, Anlass (KI-Kurzfassung), verknüpfter Vorgang. Aktionen: Anrufen (tel:-Link), Erledigt mit Gesprächsnotiz, Verschieben. Notiz wird im Vorgang protokolliert.

### 5.11 Mobile Ansicht – siehe Mockup 5
Responsive Web-App (installierbar als PWA): „Heute“-Liste nach Priorität, Rückrufe mit Direktwahl, Vorgang kompakt mit Fakten. Kein eigener App-Store-Client im MVP.

### 5.12 Benachrichtigungen
Optional Push/Mail-Digest: 07:30 Tagesbriefing; sofort bei neuem Vorgang mit Stufe „Kritisch“.

## 6. Nicht im MVP
- Automatischer Versand ohne Freigabe
- Anbindung an Agenturverwaltungs-/Maklerverwaltungsprogramm und BiPRO-Normen (Phase 3, Schnittstelle vorbereitet)
- Mehrbenutzer mit Rollen (Phase 2)
- Telefonie-Integration (Anrufe protokollieren)
- Eigene native Apps

## 7. Datenschutz und Sicherheit
Mails enthalten personenbezogene Daten, teils besondere Kategorien nach Art. 9 DSGVO (Gesundheitsdaten bei BU, Kranken, Unfall). Daraus folgt:
- Hosting in der EU (z. B. Rechenzentrum in Deutschland), Verschlüsselung in Ruhe und beim Transport.
- KI-Verarbeitung nur über Anbieter mit EU-Datenverarbeitung bzw. vertraglich zugesichertem Ausschluss von Training und Speicherung; Auftragsverarbeitungsvertrag (AVV) mit allen Dienstleistern.
- Datenminimierung: Analyseergebnisse und Verweise speichern, Mailinhalte nur so lange wie nötig; Löschkonzept (z. B. abgeschlossene Vorgänge nach konfigurierbarer Frist).
- Zugriff nur per Microsoft-Login mit 2FA; Audit-Log aller Aktionen.
- Option „Gesundheitsdaten maskieren“: Sparten Kranken/BU/Unfall werden nur kategorisiert, nicht inhaltlich zusammengefasst.
- Rechtliche Prüfung (DSGVO, ggf. § 203 StGB, Vorgaben des Versicherers an seine Vertreter) vor Produktivbetrieb.

## 8. Technischer Lösungsvorschlag
| Baustein | Vorschlag |
|---|---|
| Frontend | Web-App (React + TypeScript), responsive, PWA |
| Backend/API | Python (FastAPI) oder Node (TypeScript) |
| Datenbank | PostgreSQL inkl. pgvector für Ähnlichkeitssuche |
| Hintergrundjobs | Queue (z. B. Redis/RQ) für Sync, OCR, Analyse |
| Mail | Microsoft Graph (Delta-Sync, Webhooks), IMAP als Fallback |
| KI | LLM-API mit strukturierter Ausgabe (JSON-Schema) für Klassifikation, Extraktion, Kurzfassung, Antwortentwurf; Embeddings für Zuordnung |
| Betrieb | Container in EU-Cloud; Single-Tenant je Agentur |

### Datenmodell (vereinfacht)
- **Mail**: id, thread_id, von, an, datum, betreff, text, anhänge, kategorie, sparte, absenderrolle, kurzfassung, handlungsbedarf, vorgang_id, zuordnungssicherheit, zuordnung_quelle (auto/manuell)
- **Vorgang**: id, titel, kunde_id, typ, sparte, status (neu, in Bearbeitung, wartet auf Kunde, wartet auf Versicherer, erledigt), score, stufe, gründe[], frist, wiedervorlage, gepinnt
- **Fakt**: vorgang_id, schlüssel (z. B. schadennummer), wert, quelle_mail_id, textstelle, sicherheit
- **Kunde/Kontakt**: name, adressen[], telefon, verträge[] (optional Import per CSV aus der Agentursoftware)
- **Aufgabe**: typ (Rückruf, Wiedervorlage, Unterlage anfordern), fällig, vorgang_id, erledigt, notiz
- **Regel**: Faktor, Gewicht, aktiv

## 9. Umsetzung in Phasen
| Phase | Inhalt | Ergebnis |
|---|---|---|
| 0 – Klickdummy (diese Mockups) | Abstimmung Layout und Begriffe | freigegebenes Design |
| 1 – MVP (ca. 6–8 Wochen) | M365-Anbindung, Analyse, Vorgangsbildung, Priorisierung, Dashboard, Vorgangsansicht, Zuordnung prüfen, Rückrufe | produktiv für einen Nutzer |
| 2 | Antwortentwürfe ins Postfach, Mobile/PWA-Feinschliff, Tagesbriefing, Innendienst-Zugang | Team-Nutzung |
| 3 | Kundenstamm-Import, Agentursoftware-Schnittstelle, Auswertungen | Integration |

## 10. Abnahmekriterien MVP
1. Neue Mails erscheinen spätestens 2 Minuten nach Eingang analysiert im Dashboard.
2. Auf einem Testkorpus von 300 echten (anonymisierten) Agenturmails: Kategorie in ≥ 90 % korrekt, Vorgangszuordnung in ≥ 90 % korrekt oder als „prüfen“ markiert, < 3 % falsch automatisch zugeordnet.
3. Jeder Fakt in der Faktenkarte ist per Klick zur Quellmail rückverfolgbar.
4. Jede Priorität zeigt mindestens einen Grund.
5. Ein Vorgang lässt sich vom Dashboard aus in ≤ 2 Klicks erledigen, wiedervorlegen oder beantworten.
6. Es wird keine Mail ohne ausdrückliche Bestätigung versendet.
7. Bedienbar am Smartphone (390 px Breite) ohne horizontales Scrollen.

## 11. Offene Fragen an den Auftraggeber
1. Welches Mailsystem wird genutzt (Microsoft 365, Exchange on-premises, anderer Anbieter)?
2. Wie viele Mails pro Tag, und gibt es neben dem persönlichen Postfach ein Sammelpostfach (z. B. schaden@)?
3. Welche Sparten machen den Großteil aus (Privat Kfz/Sach vs. Leben/Kranken vs. Gewerbe)?
4. Ausschließlichkeitsvertreter oder Makler? (Anzahl Versicherer, Vorgaben zu IT und Datenschutz)
5. Lässt sich ein Kundenstamm (Name, Mail, Telefon, Vertragsnummern) aus der Agentursoftware exportieren?
6. Cloud-Hosting in der EU akzeptabel oder Betrieb im eigenen Netz gewünscht?
7. Soll die App Antworten selbst versenden oder nur Entwürfe in Outlook ablegen?
8. Nutzt künftig auch Innendienst/Assistenz das Tool?
9. Gibt es feste Fristen/Abläufe, die als Regeln abgebildet werden sollen (z. B. Schadenmeldung binnen 24 h an Versicherer)?
