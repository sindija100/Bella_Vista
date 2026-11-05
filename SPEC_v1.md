# SPEC v1 · Bella Vista

**Quelle:** Meeting #1 mit Marco Ferretti (Bella Vista), 12 Nachrichten. Regeln und Entscheidungen sind den Antworten zugeordnet. Die in der Zukunftsfrage genannte Catering-Funktion ist ein vorläufiger Kandidat für genau eine neue Anforderung aus Meeting #2; sie wird dort konkretisiert und in der finalen SPEC.md festgehalten.

## Entitäten

| Entität | Attribute |
|---|---|
| Restaurant | restaurant_id, name |
| Standort | standort_id, restaurant → Restaurant, name (Kreuzberg, Spandau), adresse |
| Tisch | tisch_id, standort → Standort, tisch_nummer, kapazitaet, status |
| Gast | gast_id, name, telefon (eindeutiger Suchschlüssel), email (optional), besuchsanzahl, bella_card_aktiv, notizen |
| Reservierung | reservierung_id, gast → Gast, standort → Standort, tisch → Tisch, zeitpunkt, personenanzahl, status (bestätigt, gesetzt, storniert, no-show), notizen |
| Bestellung | bestellung_id, standort → Standort, gast → Gast (optional bei anonymem Besuch), reservierung → Reservierung (optional), erstellt_am, status |
| Gericht | gericht_id, name, beschreibung, preis, aktiv |
| Bestellposition | bestellposition_id, bestellung → Bestellung, gericht → Gericht, menge, einzelpreis |
| Mitarbeiter | mitarbeiter_id, name, rolle (Chef, Standortmanager, Service, Küche), standortzugriff |

## Beziehungen

- Restaurant 1:n Standort
- Standort 1:n Tisch
- Standort 1:n Reservierung
- Gast 1:n Reservierung
- Tisch 1:n Reservierung über die Zeit; Reservierungen desselben Tisches dürfen sich zeitlich nicht überschneiden
- Reservierung 0..1:0..1 Bestellung (eine Bestellung kann einer Reservierung zugeordnet sein; Walk-ins können ohne Reservierung bestellen)
- Bestellung n:m Gericht über Bestellposition
- Standort n:m Gericht (standortspezifische Speisekarten)
- Mitarbeiter n:m Standort (der Chef hat Zugriff auf beide Standorte; der Standortmanager nur auf seinen Standort)

## Business Rules

- **BR1** Reservierungen blockieren den zugewiesenen Tisch standardmäßig für zwei Stunden ab Reservierungszeit. (Antwort 3)
- **BR2** Ist ein Gast 30 Minuten nach Reservierungszeit ohne Meldung nicht erschienen, wird die Reservierung als No-show markiert und der Tisch kann freigegeben werden. (Antwort 7)
- **BR3** Ab zehn erfassten Besuchen wird die Bella-Card aktiv; Besuche an Kreuzberg und Spandau zählen zusammen. (Antwort 5)
- **BR4** Mit aktiver Bella-Card erhält der Gast 15 % Rabatt auf die gesamte Bestellung; die Karte gilt an beiden Standorten. (Antwort 5)
- **BR5** Stornierungen werden als Statusänderungen gespeichert, nicht gelöscht. (Antwort 7)

## Widersprüche und Auflösung

- **W1** „Pro Tisch immer zwei Stunden“ (Antwort 3) vs. Freigabe nach 30 Minuten bei Nichterscheinen (Antwort 7) → Die Zweistundenbelegung gilt bei wahrgenommenen Reservierungen. Bei ausbleibendem Gast ohne Meldung greift nach 30 Minuten der No-show-Status und der Tisch wird freigegeben.
- **W2** Stammgäste werden zunächst „vom Gesicht“ erkannt (Antwort 1), später ist die Telefonnummer der verlässliche Schlüssel und Gäste ohne Nummer laufen anonym (Antwort 4 und 6) → Für die App gilt die Telefonnummer als Identifikator. Eine Gesichtserkennung ist nicht Teil der Spezifikation; ohne Telefonnummer erfolgt keine Zuordnung zum Gastkonto und keine Bella-Card-Prüfung.

## Prioritäten

1. Standortübergreifende Echtzeit-Übersicht über Tische und Reservierungen
2. Gäste, Besuchszähler und Bella-Card-Rabatt
3. Bestellungen mit Zuordnung zu Standort und Speisekarte
4. Rollen und standortbezogene Zugriffsrechte

## Kandidat für die neue Anforderung in Meeting #2

- **N1 Catering-Aufträge:** Marco möchte Catering für Firmenevents mit Angebotsverwaltung und Auftragsverfolgung in der App abbilden. Diese neue Anforderung stammt aus seiner Antwort auf die Zukunftsfrage und soll in Meeting #2 konkretisiert werden. (Antwort 12)

## Offene Fragen für Meeting #2

 - Sie haben Catering für Firmenevents erwähnt. Wie läuft ein Catering-Auftrag heute ab, von der ersten Anfrage bis zur Durchführung?
- Welche Angaben brauchen Sie vom Kunden für eine Catering-Anfrage und ein Angebot?
- Was möchten Sie in der App bei einem Catering-Auftrag im Blick behalten, damit nichts Wichtiges vergessen wird?
- Welche genaue Tischkapazität und welche weiteren Tischmerkmale müssen gepflegt werden?
- Welche Informationen soll die Speisekarte zu jedem Gericht haben, z. B. Bilder von Gerichten, Preise, Allergene, Portionsgröße und ob es aktuell verfügbar ist?
- Welche Zahlungsmöglichkeiten haben Sie und welche sollen in die App erfasst werden? Karte, Rechnungen oder Bar? Gibt es vielleicht die Möglichkeit, vorher in der App zu zahlen?
- Wie gehen Sie mit Rezensionen, Bewertungen, Kommentaren um? Wie läuft es jetzt gerade bei Ihnen?
- Gibt es Geschäftsprozesse, die nicht so gut laufen und die eine digitale Unterstützung brauchen?