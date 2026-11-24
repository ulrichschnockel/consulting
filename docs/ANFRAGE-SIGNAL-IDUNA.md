# Entwurf: Anfrage an die SIGNAL IDUNA

An: zuständige Bezirksdirektion, mit der Bitte um Weiterleitung an IT-Sicherheit und Datenschutz
Von: Agenturinhaber
Anlagen: Spezifikation (`docs/SPEC.md`, als PDF), Mockups (Bildschirmfotos)

---

**Betreff:** Bitte um Prüfung und Freigabe: Assistenz-Tool für Postfach und Kalender meiner Agentur

Sehr geehrte Damen und Herren,

in meiner Agentur gehen täglich viele Mails zu laufenden Fällen ein: Schadenmeldungen, Nachreichungen, Rückrufbitten, Terminwünsche. Zu einem Fall kommen oft mehrere Mails von verschiedenen Absendern zu unterschiedlichen Zeitpunkten. Damit nichts liegen bleibt, möchte ich ein Assistenz-Tool einsetzen, das mein Postfach liest, zusammengehörige Mails zu Vorgängen bündelt, nach Dringlichkeit sortiert und Rückrufe sowie Termine übersichtlich darstellt.

Da Postfach und Kalender bei der SIGNAL IDUNA liegen, bitte ich um Prüfung, ob und unter welchen Bedingungen Sie den Zugriff freigeben können.

**Geplanter Zugriff, bewusst minimal:**
- Anmeldung durch mich selbst (delegierte Berechtigungen in Microsoft 365). Das Tool sieht nur mein eigenes Postfach und meinen Kalender, keine anderen Postfächer.
- Berechtigungen: Mails lesen und Antwortentwürfe anlegen (`Mail.ReadWrite`), Kalender lesen und bestätigte Termine eintragen (`Calendars.ReadWrite`), Anmeldung (`User.Read`, `offline_access`).
- **Keine Sendeberechtigung:** Das Tool kann keine Mails versenden. Antworten landen als Entwurf in Outlook und werden von mir geprüft und abgeschickt.
- Die Zustimmung kann jederzeit widerrufen werden.

**Datenverarbeitung:**
- Betrieb ausschließlich in einer EU-Region eines großen Cloud-Anbieters, inklusive der KI-Auswertung der Mails. Die KI wird nicht mit unseren Daten trainiert.
- Auftragsverarbeitungsverträge mit allen Dienstleistern, Löschkonzept, Protokollierung aller Zugriffe.
- Gesundheitsdaten (Kranken, BU, Unfall, Leben) können auf Wunsch nur kategorisiert und nicht inhaltlich ausgewertet werden.

**Meine Fragen an Sie:**
1. Ist eine Freigabe in dieser Form grundsätzlich möglich, und welche Unterlagen oder Prüfungen benötigen Sie (z. B. Sicherheitsfragebogen, Datenschutzkonzept)?
2. Gibt es Vorgaben zu Cloud-Anbietern, Hosting-Regionen oder KI-Diensten?
3. Bietet die SIGNAL IDUNA bereits vergleichbare Funktionen oder Schnittstellen an, die ich stattdessen nutzen sollte?
4. Darf ich für die Zuordnung der Mails einen Auszug meines Kundenbestands (Name, Mailadresse, Telefon, Vertragsnummern) im Tool verwenden?
5. Wäre die SIGNAL IDUNA an einem Pilotbetrieb in meiner Agentur interessiert?

Die technische Umsetzung begleitet ein externer Berater. Für Rückfragen stehen wir gern gemeinsam zur Verfügung, auch in einem kurzen Termin.

Mit freundlichen Grüßen

Markus J. Himmler
Agentur der SIGNAL IDUNA
