# Coding-Aufgabe

# Bewertung

Das Wichtigste zuerst: 
> Don't panic! -- *The Hitchiker's Guide to the Galaxy*

Ziel der Aufgabe ist es, dass Sie ein Gefühl für die Arbeit als Data Engineer bekommen. Gleichzeitig wollen wir einen Eindruck für Ihr Vorgehen als Entwickler gewinnen können. 
Die Aufgabe soll sowohl die klassische Datenaufbereitung simulieren als auch die Situation, dass Sie als Data Engineer immer vor der Herausforderungen stehen, sich in neue Technikthemen einzuarbeiten um ein Problem zu lösen. 

Sie sollen weder das Rad neu erfinden, noch an den Problemen verzweifeln. Deswegen nutzen Sie die zusätzlichen Libraries Ihrer Programmiersprache um alltägliche Probleme zu lösen (Nutzen Sie eine Library zum Parsen von JSON-Files und schreiben sie keinen JSON-Parser). Und wenn Sie nicht weiter wissen, dann nehmen Sie bitte Kontakt mit Ihrem Ansprechpartner auf, denn letztendlich ist das Erstellen von Datenprodukten immer ein Teamsport.

Die Bearbeitung der Aufgabe und die Bereitstellung der Ergebnisse soll bis zum **04.08.2021** erfolgen. 

# Hintergrund

Eine Aufgabe der Krankenkasse ist es die Fahrkosten, die im Rahmen von medizinisch notwendigen Leistungen entstanden sind, zu übernehmen. Eine Fahrt wird der Krankenkasse zeitnah nach ihrer Durchführung mitgeteilt und dort fortlaufend in einer Datenbank hinterlegt. 
Die Rechnungen erreichen die Krankenkassen meist erst 2-4 Wochen nach der Fahrt über einen elektronischen Datenaustausch. Vor Bewilligung und Bezahlung der Rechnung muss diese geprüft werden. Dies geschieht aktuell noch manuell, aber eine automatische Prüfung ist in Überlegung.

# Aufgabenstellung

Ihre Aufgabe ist es nun, eine Datenbearbeitungsstrecke aufzubauen, die die Daten aus beiden Prozessen (Fahrtmeldung und Rechnung) automatisiert zusammenführt und um wichtige Infos für die Rechnungsprüfung anreichert. 

Wie Sie die Datenverarbeitung aufbauen und welche Software Sie dafür einsetzen, bleibt Ihnen überlassen. Wichtig ist, dass das Ergebnis sowohl den Source-Code + Kommentare als auch die fertige Anwendung (falls zutreffend) beinhalten sollte. 

## Datenquellen

### Fahrtmeldung

Wenn eine Fahrt der Krankenkasse bekannt gemacht wird, wird sie in einer SQLite-Datenbank verbucht. Die Datenbank ist im Pfad `sources/fahrten_db.sqlite` auffindbar. Die enthält die Tabelle `FAHRTEN`, in welcher die Fahrtmeldungen zu finden sind. Die Tabelle hat die folgenden Attribute:

- **fahrt_id**: Die ID identifiziert eine Fahrt eindeutig. Sie wird fortlaufend für jede neue Fahrt vergeben. 
- **person**: Diese ID identifiziert die Person(Versicherter), die die Fahrt gemacht hat. 
- **datum_fahrt**: Datum, an dem die Fahrt stattgefunden hat im Format YYYY-MM-DD
- **start**: Adresse, an der die Fahrt begonnen hat.
- **ziel**: Adresse, an der die Fahrt geendet hat.

### Rechnung im Datenaustausch

Die Rechnungen kommen einzeln als Datei im Datenaustausch. Sie sind im ordner `sources/dta_rechnungen` zu finden und sind nach dem folgenden Schema benannt `kk_<fahrt_id>_<rechnungsdatum>.json`. Die Informationen in den Dateien werden im `json`-Format bereitgestellt. Die Keys entsprechenen in Bezeichnung und Inhalt den Tabellenspalten aus dem Abschnitt *Fahrtmeldung*. Die Start- und Zieladressen sind nicht vorhanden. Zusätzlich werden aber zwei weitere Keys übermittelt.
- **datum_rechnung**: Datum, an dem die Rechnung gestellt wurde
- **betrag_rechnung**: Der Rechnungsbetrag als String in Euro 

## Die Anforderung im Detail

### Zusammenführung: Fahrtmeldung und Rechnung

Die Informationen aus der Fahrtmeldung und den Rechnungsinformationen sollen zusammengeführt werden. 

### Anreicherung: Entfernung zwischen Start und Ziel

Für eine einfachere Rechnungsprüfung soll die Entfernung zwischen Start- und Zieladresse ermittelt werden. Dafür wurde folgendes Vorgehen ausgewählt.
Für Adressen können über die Web-API von Nominatim (https://nominatim.org/) die Geokoordinaten (lat/lon) ermittelt werden (Hier kann noch viel mehr ermittelt werden). Die Ermittlung über die Schnittstelle kann automatisiert erfolgen.

Aus den ermittelten Koordinaten für Start- und Zielort lässt sich mittels der Haversine Formel die Entfernung zwischen den beiden Orten berechnen. Einen Überblick finden sie hier: https://www.geeksforgeeks.org/program-distance-two-points-earth/


### Bereitstellung der Tabellen 

Die Ergebnisstabelle soll in zwei Varianten zurück in eine neue SQLite-Datenbank `fahrten_dwh.sqlite` geschrieben werden. 
- **FAHRTEN_ABRECHNUNG_RAW**: Hier werden alle Spalten der Tabelle angelegt, die bei den Bearbeitungsschritten entstanden sind. Also alle Infos aus Fahrtmeldung, Rechnung, die Koordinaten und die Entfernung.
- **FAHRTEN_ABRECHNUNG**: Hier soll eine View auf die RAW-Tabelle angelegt, welche nur die für die Sachbearbeitung relevanten Spalten enthält ohne die Koordinaten. Hier sollen nur die Fahrten berücksichtigt werden für die bereits eine Rechnung vorliegt. 


# Vorgehen

Erstellen Sie sich eine Fork des Aufgabenrepositories in ihren Github-Account und lösen Sie die Aufgabe dort in der Branch mit ihrem Pseudonym (wird gesondert mitgeteilt). Wenn sie die Aufgabe gelöst haben, stellen Sie einen Pull Request in das Original-Repositorium wie hier beschrieben: https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork


