# SPEC v1 · Bella Vista

**Quelle:** Meeting #1 mit Marco Ferretti (12 Nachrichten) und Meeting #2 (8 Fragen). Zu jeder Business Rule ist die Quelle angegeben. Meeting #2 ergänzt genau eine neue Funktion: Catering-Auftragsverwaltung.


## Entitäten

| Entität | Attribute |
|---|---|
| Restaurant | restaurant_id, name |
| Standort | standort_id, restaurant → Restaurant, name (Kreuzberg, Spandau), adresse, restaurantschluss |
| Tisch | tisch_id, standort → Standort, tisch_nummer, platzanzahl, bereich (innen, außen) , status |
| Gast | gast_id, typ (privat, firma), name, firmenname (optional), telefon, email, besuchsanzahl, bella_card_aktiv, notizen |
| Reservierung | reservierung_id, gast → Gast, standort → Standort, tisch → Tisch, zeitpunkt, personenanzahl, status (bestätigt, gesetzt, storniert, no-show), notizen |
| Bestellung | bestellung_id, typ (Restaurant, Catering) sgast → Gast (optional), reservierung → Reservierung (optional), standort → Standort, erstellt_am, status, eventdatum (Catering), lieferadresse (Catering), personenanzahl (Catering), menüformat (Catering), vereinbartes_menü (Catering), gesamtpreis, rechnungsstatus (Catering: offen, bezahlt) |
| Gericht | gericht_id, name, beschreibung, preis, Kategorie (Vorspeise, Hauptgang, Dessert, Getränk), verfügbarkeit |
| Bestellposition | bestellposition_id, bestellung → Bestellung, gericht → Gericht, menge, einzelpreis |
| Mitarbeiter | mitarbeiter_id, name, rolle (Chef, Standortmanager, Service, Küche), standortzugriff |

## Beziehungen

- Standort 1:n Tisch
- Standort 1:n Reservierung
- Gast 1:n Reservierung (Reservierung kann ohne bekanntes Gastprofil angelegt werden)
- Tisch 1:n Reservierung über die Zeit
- Reservierung 0..1:n Bestellung (Bestellungen können einer Reservierung zugeordnet sein; Walk-ins haben keine Reservierung)
- Gast 1:n Bestellung (Bestellung kann ohne bekanntes Gastprofil angelegt werden)
- Bestellung n:m Gericht über Bestellposition
- Standort n:m Gericht (Speisekarte und Verfügbarkeit sind standortbezogen)
- Mitarbeiter n:m Standort (Chef: beide Standorte; Standortmanager: eigener Standort)

## Business Rules

- **BR1** Eine Reservierung blockiert den Tisch grundsätzlich für zwei Stunden ab Reservierungszeit. Kommt der Gast ohne Meldung nicht innerhalb von 30 Minuten, wird die Reservierung als No-show markiert und der Tisch kann freigegeben werden. (Meeting #1, Antworten 3 und 7; Auflösung unter W1)
- **BR2** Ab zehn erfassten Besuchen wird die Bella-Card aktiv; Besuche in Kreuzberg und Spandau zählen gemeinsam. (Meeting #1, Antwort 5)
- **BR3** Bella-Card-Inhaber erhalten 15% Rabatt auf die gesamte Restaurantbestellung; die karte gilt an beiden Standorten. (Meeting #1, Antwort 5)
- **BR4** Stornierte Reservierungen bleiben als Datensatz mit Status "storniert" erhalten und werden nicht gelöscht. (Meeting #1, Antwort 7)
- **BR5** Die Küche nimmt Bestellungen nur bis 30 Minuten vor dem Restaurantschluss des jeweiligen Standorts an. (Meeting #2, Antwort auf die Abschlussfrage)

## Widersprüche

- **W1** „Pro Tisch immer zwei Stunden“ (Meeting #1, Antwort 3) vs. Freigabe nach 30 Minuten bei Nichterscheinen (Antwort 7) → Die Zweistundenbelegung gilt bei wahrgenommenen Reservierungen. Bei ausbleibendem Gast ohne Meldung greift nach 30 Minuten der No-show-Status und der Tisch wird freigegeben
- **W2** Stammgäste werden zunächst „vom Gesicht“ erkannt (Meeting #1, Antwort 1), später ist die Telefonnummer der verlässliche Schlüssel und Gäste ohne Nummer laufen anonym (Meeting #1, Antwort 4 und 6) → Die App ordnet Gäste anhand der Telefonnummer zu. Eine Zuordnung per Gesichtserkennung ist nicht vorgesehen; ohne Telefonnummer bleibt der Besuch anonym und zählt nicht zum Gastkonto

## Prioritäten

1. Standortübergreifende Echtzeit-Übersicht über Tische und Reservierungen; Doppelbuchungen verhindern
2. Digitale Restaurantbestellungen mit klarer Weitergabe an die Küche
3. Einheitliches Gastprofil mit Besuchszähler und Bella-Card-Erkennung per Telefonnummer
4. Standortbezogene Speisekarten und Rollenrechte
5. Catering-Aufträge und Rechnungsstatus

## Neue Anforderung aus Meeting #2

- **N1 Catering-Auftragsverwaltung:** Catering-Anfragen, Angebote, bestätigte Aufträge, Lieferung und Zahlung sollen in der App nachvollziehbar sein. Pro Auftrag sollen Firmenkunde/Kontakt, Eventdatum, Personenzahl, Lieferadresse, Menüformat, genau vereinbartes Menü, Gesamtpreis und Rechnungsstatus einsehbar sein. Firmenkontakte sollen mit dem gemeinsamen Gastprofil verknüpft werden, damit dieselbe Person nicht doppelt geführt wird. (Meeting #2, Antworten 1–3)


## Weitere Festlegungen aus Meeting #2

- Restaurantbestellungen erhalten mindestens die Status offen, serviert, bezahlt und storniert. Die konkrete Zahlungsart wird nicht benötigt; die Zahlung läuft über das bestehende Kassengerät
- Catering-Rechnungen werden nach dem Event per Überweisung bezahlt; die App verfolgt, ob die Rechnung offen oder bezahlt ist. Online-Vorauszahlung ist ausgeschlossen
- Speisekarten führen Name, Beschreibung, Preis, Kategorie, Standort und Verfügbarkeit Grillgerichte sind in Spandau nicht verfügbar; ausverkaufte Gerichte können deaktiviert werden, ohne sie zu löschen. Bilder sind nachrangig; Allergene sind für eine spätere Ergänzung vorgemerkt
- Rezensionen aus Google oder TripAdvisor sind nicht Teil der App
- Tischdaten umfassen Nummer, Platzanzahl, Innen-/Außenbereich, Standort und aktiv/inaktiv. Deaktivierte Tische bleiben für die Historie erhalten
